---
tags: dev, db
---
## 동시성 문제
---
- DB에서 특정 entity를 꺼내와 업데이트를 하려고 할 때, 동시성 문제가 발생할 수 있다.
- 예) 빈 타임 슬롯을 조회한 뒤에 예약 정보를 업데이트하는 경우, 슬롯 조회와 예약 정보 업데이트 사이에 다른 트랜잭션이 수행될 수 있다.
```java
public void makeReservation(MakeReservationCommand command) {  
	var slot = slotRepository.findById(command.getSlotId())  
		.orElseThrow(() -> new RuntimeException("Slot does not exist."));  
	if (!slot.getEnable()) {  
		throw new RuntimeException("Slot is disabled.");  
	}  
	var reservation = new Reservation(  
		UUID.randomUUID().toString(),  
		command.getUserId(),  
		slot.getId()  
	);  
	var newSlot = new Slot(  
		slot.getId(),  
		slot.getReservationCount() + 1,  
		slot.getStartTime(),  
		slot.getEndTime(),  
		slot.getReservationCount() + 1 <= slot.getThresholdCount()  
	);  
	save(reservation, newSlot);  
}  
  
@Transactional  
private void save(Reservation reservation, Slot slot) {  
	reservationRepository.save(reservation);  
	slotRepository.save(slot);  
}
```
- JPA 사용시, 이를 해결하기 위한 방법으로 Optimistic Lock과 Pessimistic Lock 두가지가 있다.
### Optimistic Lock
#### 개념
- 데이터를 변경할 때 version 정보가 업데이트 되었는지 확인하여, 이미 version이 업데이트 되었다면 OptimisticLockingFailureException을 던진다
#### 사용조건
- 레이스컨디션이 자주발생하지 않는다
- Read가 Update나 Delete보다 자주 일어난다
- 충돌이 발생하지 않는한 동시 접근을 허용하고 싶다
- 성능상 문제를 겪고 싶지 않다
#### 사용방법
- @Version 어노테이션을 붙인 필드를 Entity 클래스에 생성한다 (_int_, _Integer_, _long_, _Long_, _short_, _Short_, _java.sql.Timestamp_ 만 가능)
```java
@Entity public class Slot {
	// Other fields 
	@Version private Long version; 
	// Getters and setters 
}
```
```java
@Transactional
public void makeReservation(MakeReservationCommand command) {  
	... as-is code ...
}  
```
### Pessimistic Lock
#### 개념
- DB레벨에서 락을 걸어 다른 조회요청을 막고, 경합이 발생하면 PessimisticLockingFailureException을 던진다
#### 사용조건
- 레이스컨디션이 예상된다
- Update나 Delete가 자주 일어난다
- 성능저하를 감수하더라도 데이터 일관성을 강력하게 유지하고 싶다
#### 사용방법
- @Lock(LockModeType.PESSIMISTIC_WRITE) 어노테이션을 Repository 클래스의 메서드에 붙인다
```java
@Repository
public interface SlotRepository extends JpaRepository<Slot, Long> {

    @Lock(LockModeType.PESSIMISTIC_WRITE)
    Optional<Slot> findById(Long id);
    
    // Other methods...
}
```
```java
@Transactional
public void makeReservation(MakeReservationCommand command) {  
	... as-is code ...
}  
```
### Exception Handling
- Retry, Notify user, Logging, Rollback 등 다양한 액션을 취할 수 있다
#### Retry 예시
```java
@Transactional  
public void makeReservation(MakeReservationCommand command) {  
	int maxRetries = 3; // Maximum number of retries  
	int retries = 0; // Counter for retry attempts  
  
	while (retries < maxRetries) {  
		try {  
			// as-is code  
			// If the reservation process is successful, break out of the loop  
			break;  
		} catch (OptimisticLockingFailureException ex) {  
			// Log the concurrency conflict  
			retries++;  
			if (retries >= maxRetries) {  
				// Retry limit reached, handle the failure  
				handleRetryFailure(command);  
			}  
		}  
	}  
}  
  
private void handleRetryFailure(MakeReservationCommand command) {  
	// Handle the failure when retry limit is reached  
}
```
## IoC를 활용한 데이터 원자성 확보
- https://blog.gangnamunni.com/post/isolate-transaction-from-domain-model/
## Appendix
---
- Optimistic Lock: https://www.baeldung.com/jpa-optimistic-locking
- Pessimistic Lock: https://www.baeldung.com/jpa-pessimistic-locking
- `@Transactional` 어노테이션 없이 Pessimistic Lock을 사용하여 `TransactionRequiredException`가 발생한 사례: https://woonys.tistory.com/entry/Transactional은-만능이-아닙니다-2-트랜잭션의-격리성과-lock
- 동시성 문제 유닛 테스트: https://blog.mimacom.com/testing-optimistic-locking-handling-spring-boot-jpa/