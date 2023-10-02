---
tags: dev, design-pattern, architecture
---
## 개요
---
- 프로그램의 흐름을 외부 트리거에 의해 결정(한쪽에서 이벤트를 생성하여 해당 이벤트를 Message broker, Event Stream 등에 보내고 Listening하고 있는 쪽에서 응답을 소비) 하는 패턴
- 일반적인 Rest API 기반의 HTTP 요청/응답 방식에 비해, 서비스 간의 결합도를 낮추고 확장을 용이하게 한다
## 주의점
---
- Message broker, Event Stream 장애
	- 네트워크 단절, 서비스 장애 등으로 데이터가 유실될 수 있다
	- Dead Letter Queue를 만들어, 실패한 요청을 따로 모아두고 추후 처리한다
		- 장애가 장기화 되어 DLQ에 메시지가 오래 머무는 경우 사이드 이펙트가 있을 수 있다
			- Resource Usage: DLQ에 계속해서 메시지가 쌓이면서 디스크공간, 메시지 처리를 위한 메모리, 네트워크 등의 부하가 생길 수 있다.
			- Operational Overhead: DLQ의 메시지를 재처리하는 로직이 필요하므로 시스템 복잡성이 증가한다.
			- Data Aging: 메시지의 유효성이 저하될 수 있다. 예) 특정 시간에 보내야하는 마케팅 이벤트 푸쉬 등
			- Potential Data Loss: 시스템 장애, DLQ 오버플로우로 인해 메시지가 손실될 수 있다
- Eventual Consistency
	- 정의: 이벤트 드리븐 아키텍처에서는 여러 이유로 데이터가 일치하지 않을 수 있으며, 특정 시간이 지나면 결국 모든 데이터가 동기화 되어야 한다는 의미이다.
	- 방법
		- 멱등성 보장: 같은 메시지를 여러번 보내도 결과가 바뀌어서는 안된다.
		- 장애 대응 기능: 
			- 재시도 로직: 일반적으로 단순 재시도는 네트워크 상태 등을 무시한채 진행되므로, 점진적으로 재시도 대기 시간을 늘리는 Exponential Backoff(Spring retry, Resilience4j, Kafka의 Retry and Backoff 설정 등에서 지원)방식을 추천
			- 데이터 재처리: 재시도 임계치를 넘어서면, Dead Letter Queue에 처리가 실패한 데이터를 저장하고 추후 처리(Kafka에서는 명시적으로 지원하지 않아 dead-letter-queue용 토픽을 생성하고 이벤트를 발행하는 방식으로 처리해야함)
			- 데이터 동기화: DB와 Message Queue를 동시에 사용할 때 발생하는 데이터 불일치를 해결해야한다
				- [[2PC(Two Phase Commit)]]
				- [[Transactional Outbox Pattern]]
			- 장애 추적: 로깅, 모니터링 등
		- 메시지 순서 보장: Message broker 혹은 Event Stream에서 메시지 순서를 보장하거나 애플리케이션에서 이를 처리해야 함
		- 트랜잭션 관리
