---
tags: system_design, distributed_transaction, 2pc, 3pc)
---
## Background
---
- 2PC의 단점(블로킹 및 SPoF 이슈)을 극복하기 위한 분산 트랜잭션 프로토콜
## Concept
---
### Process
- Prepare: 코디네이터는 참가자들에게 CanCommit? 메시지를 보내고 "YES" 응답을 기다린다.
- Commit: "YES"응답을 받으면 코디네이터가 PreCommit 메시지를 보내고, 참가자들은 트랜잭션을 수행할 준비를 마친 뒤 "ACK" 응답을 보낸다.
- Completion: "ACK" 응답을 받은 뒤,  코디네이터가 DoCommit 명령을 보내고 참가자들은 커밋한다.
### Example
#### coordinator
```java
@Service
public class TransactionService {
   
	private ConcurrentHashMap<String, String> participantResponses = new ConcurrentHashMap<>();
    @Autowired
    private KafkaTemplate<String, String> kafkaTemplate;

    public void initiateThreePhaseCommit() {
        // Prepare Phase
        if (askParticipantsForPreparation()) {

            // Commit Phase
            if (askParticipantsForCommit()) {

                // Completion Phase
                completeTransaction();
            }
        }
    }

	public String askParticipantsForPreparation() {
		// Prepare 메시지를 참가자에게 전송
		kafkaTemplate.send("prepareTopic", "Prepare for transaction");
	
		// ... 코디네이터는 prepareResponseTopic을 구독하여 참가자들의 응답을 기다린다.
		// ... 타임아웃 처리 및 참가자들의 응답 처리 로직이 필요하다.
	
		return "Transaction started";
	}

    @KafkaListener(topics = "prepareResponseTopic")
    public void receivePrepareResponse(String response) {
        // 응답을 받아서 참가자의 응답을 추적
        participantResponses.put(response, response);

        // ... 필요한 경우 다른 로직(예: 모든 참가자로부터 응답을 받았는지 확인)을 수행
    }
}
```

#### participant
```java
@Service
public class ParticipantService {

    @Autowired
    private KafkaTemplate<String, String> kafkaTemplate;

    @KafkaListener(topics = "prepareTopic")
    public void receivePrepareRequest(String message) {
        // Prepare 요청을 받으면 준비 작업을 수행

        // ... 여기서 실제 비즈니스 로직 또는 준비 작업을 수행

        // 준비가 완료되면 코디네이터에게 응답을 보낸다.
        kafkaTemplate.send("prepareResponseTopic", "Prepared for transaction");
    }
}

```
## 주의사항
---
- 2PC의 블로킹 문제를 해결하기 위해 타임아웃을 설정하여, 타임아웃 발생시 참가자가 트랜잭션을 중단하거나 롤백할 수 있게 해야한다
- 2PC의 SPoF 문제를 해결하기 위해 코디네이터와 참가자 사이의 연결이 끊어진 경우, 코디네이터의 상태에 따라 트랜잭션을 중단하거나 롤백할 수 있게 해야한다
- 여전히 특정 실패 시나리오에서는 블로킹 등에 빠질 수 있다