---
tags: system_design, db, event_stream, message_queue
---
## Background
---
- DB와 Message Queue를 사용하는 경우 데이터 불일치가 발생할 수 있어 동기화 방법이 필요하다
- 예시
	- 장애 복구 뒤 메시지를 DB에 저장 후 DLQ에서 삭제하는 상황
		- DB에 저장하는 과정에서 오류 발생시, DLQ에서는 삭제되고 DB에는 저장되지 않아 데이터 유실
	- 퍼블리셔가 메시지를 DB에 저장 후 해당 메시지를 Queue에 발행하여 컨슈머 서비스에서 사용하는 상황
		- DB에 저장하는 과정에서 오류 발생, Queue는 발행 완료 시 컨슈머 서비스에서는 DB에 존재하지 않는 데이터를 사용
		- DB에 업데이트 과정에서 커밋 트랜잭션이 느린 경우, 컨슈머 서비스는 변경된 데이터를 사용하나 해당 DB를 사용하는 다른 서비스들은 변경되지 않은 데이터 사용

## Process
---
### concept
- Prepare: DB에 데이터를 저장하고 저장 성공시 '준비 완료' 상태로 기록 후 다음 단계 수행
	- prepare 단계에서 DB 저장이 이뤄지지 않으면 메시지는 발행되지 않는다
- Commit: 메시지를 DLQ에서 삭제하거나 메시지 큐로 발행하는 등 메시지큐 관련 작업을 수행하고 성공시 '커밋 완료' 상태로 기록
	- commit 단계에서 발행이 정상적으로 이뤄지지 않으면 DB 저장을 취소하여 데이터 일관성을 유지한다
### example
_DLQ에 있는 메시지를 DB에 저장 후 DLQ에서 삭제하는 경우_
- Kafk의 Transaction 사용을 위해 producer 설정 추가
```yaml
spring:
  kafka:
    producer:
      bootstrap-servers: localhost:9092
      transactional-id-prefix: tx-   # prefix for transactional.id property
```
- Spring @Tranactional을 사용해 consumer 업데이트
	- `new TocicPartition(record.topic(), record.partition())`: 현재 처리중인 메시지의 토픽과 파티션
	- `new OffsetAndMetadata(record.offset() +1)`: 다음에 읽어야할 메시지 offset
```java
@Service
public class DLQConsumer {

    private final KafkaTemplate<String, String> kafkaTemplate;
    private final PushEventRepository pushEventRepository;

    @Autowired
    public DLQConsumer(KafkaTemplate<String, String> kafkaTemplate, PushEventRepository pushEventRepository) {
        this.kafkaTemplate = kafkaTemplate;
        this.pushEventRepository = pushEventRepository;
    }

    @Transactional
    @KafkaListener(topics = "${spring.kafka.topic.dlq}", groupId = "${spring.kafka.consumer.group-id}")
    public void consume(ConsumerRecord<String, String> record) {
        // Convert the record value to PushEventEntity
        PushEventEntity entity = convertToEntity(record.value());

        // Save the entity to DB
        pushEventRepository.save(entity);

        // Commit the Kafka offset
        kafkaTemplate.sendOffsetsToTransaction(Collections.singletonMap(new TopicPartition(record.topic(), record.partition()), new OffsetAndMetadata(record.offset() + 1)));

/* 메시지를 발행하는 경우의 코드
	kafkaTemplate.executeInTransaction(kt -> { kt.send("your_topic", data); return null; });
*/

    }

    private PushEventEntity convertToEntity(String value) {
        // Implement the logic to convert the record value to PushEventEntity
        // If you're using a format like JSON, you can use libraries like Jackson to do the conversion
    }
}
```
## 주의사항
---
- 모든 장애를 처리/예방할 수 없으므로 장애복구를 위한 명확한 절차와 모니터링/알림 시스템을 갖추는 것이 더 중요할 수 있음
- Global Lock 이슈: 2PC 사용 시 참가자들(DB, 메시지큐)은 코디네이터(위 예시에서는 consume 메서드를 제공하는 서비스)의 결정을 기다리는 동안 리소스를 잠그게 되므로 시스템 throughput에 영향이 있음
- 사용중인 Tech Stack에서 2PC를 지원하지 못하는 경우가 있을 수 있고, 2PC를 사용하기 위해서는 DB와 메시지 큐 간에 직접적인 종속성이 생기게 됨
	- [[Transactional Outbox Pattern]]으로 해결 가능
- 2PC는 데이터베이스와 kafka간의 작업을 동기화하는 것은 아니기 때문에 완벽한 원자성 보장은 아님
	- 블로킹 문제:  'Commit' 단계에서 코디네이터 혹은 참가자(메시지 큐)와의 통신에 문제가 생기면 트랜잭션 블로킹이 발생할 수 있다
	- SPoF: 2PC 작업 중 코디네이터 중단 시 전체 프로토콜이 중단되어 원자성이 위협받을 수 있다
	- [[3PC(Three Phase Commit)]], [[Saga Pattern]] 등으로 해결 가능