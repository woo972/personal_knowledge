---
tags: system_design, db, event_stream, message_broker
---
## Background
---
- 메시지를 DB에 저장하고 메시지큐로 보내는 과정에서 데이터의 불일치가 발생할 수 있다. [[2PC(Two Phase Commit)#Background]]
## Concept
---
### 구성요소
- 메시지를 생성하는 서비스 A
- 메시지를 생성하는 서비스가 메시지를 저장하는 Table
- outbox용 Table
- 메시지를 중개하는 서비스(메시지 릴레이, A서비스 내부에 있을 수도 있음) 
- 메시지큐
- 메시지를 소비하는 서비스 B
### Process
- A서비스에서 메시지를 생성하고 데이터를 DB에 저장한다. 이 때 원래 메시지가 저장되어야 할 Table과 Outbox용 Table에 함께 저장한다. (single transaction)
- 메시지 릴레이는 Outbox Table을 주기적으로 조회하여 메시지큐에 전송한다. 이 때 발행에 성공한 메시지는 별도 표기하거나 삭제한다.
- B서비스는 메시지큐에 발행된 메시지를 컨슘한다. 이 때 메시지가 중복되어 발행되었을 수 있으므로, 메시지가 한번 이상 발행된 것인지 확인하여 처리한다. (메시지 id 트래킹 등)
	- 메시지 발행 시 장애가 발생하여, 컨슈머까지 도달하였음에도 발행완료로 간주되지 못한 경우,  메시지큐가 재시작되면 메시지발행을 다시 시도할 수 있기 때문이다
![[Pasted image 20230806130157.png]]
### 장점
- B서비스에서 컨슘하는 데이터는 항상 실제 DB에 저장된 데이터이다.
- [[2PC(Two Phase Commit)]]와 비교했을 때 원래의 Table과 메시지큐 사이의 종속성이 없어진다.
### 단점
- 메시지 릴레이 서비스는 스케쥴링에 의해 지속 실행되므로 불필요한 리소스를 사용하게 된다.
- Outbox Table을 위지하기 위한 추가 공간을 사용한다.
- OutBox Table 관리 비용이 증가한다(예> 원래 Table을 개발자가 직접 업데이트한 뒤에 Outbox Table에 동일하게 반영하지 않으면, 최신 메시지 발행이 누락된다.)

## Appendix
---
- https://blog.gangnamunni.com/post/transactional-outbox/
- https://microservices.io/patterns/data/transactional-outbox.html