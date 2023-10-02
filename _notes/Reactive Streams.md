---
tags: dev,reactive
---
## 정의 및 개요
--- 
- 데이터 스트림을 Non-Blocking이면서 비동기적으로 처리하기 위한 리액티브 라이브러리 표준사양
- RxJava, Akka Streams, Reactor, Java 9 Flow API 등이 있다.
- Spring WebFlux는 Reactor에 기반하고 있다.
### 리액티브 스트림즈 구성요소
- Publisher: 데이터를 생성하고 통지하는 역할.
- Subscriber: 구독한 Publisher로 부터 통지된 데이터를 받아 처리하는 역할.
- Subscription: Publisher에 요청할 데이터의 개수를 지정하고, 데이터 구독을 취소하는 역할.
- Processor: Publisher와 Subscriber의 기능을 모두 가지고 있음.
### 흐름
- Publisher.subscribe()를 통해 Subscriber가 Publisher의 데이터를 구독한다.
- Publisher는 데이터를 통지할 준비가 되었음을 Subscriber.onSubscirbe()를 통해 알린다.
- Subscriber가 원하는 데이터의 개수를 Subscription.request()를 통해 Publisher에게 알린다. 
- Publisher는 요청받은 데이터 개수 만큼 Subscriber.onNext()를 통해 데이터를 통지한다.
- 모든 데이터 통지가 완료되면 Publisher는 Subscriber.onComplete()를 통해 모든 전송이 끝났음을 알린다.
- 데이터 처리과정에서 에러가 발생하면 Subscriber.onError()를 통해 알린다.
### 인터페이스
- Publisher
```java
public interface Publisher<T> {
	public void subscribe(Subscribe<? super T> s);
}
```
- Subscriber
```java
public interface Subscriber<T> {
	public void onSubscribe(Subscription s);
	public void onNext(T t);
	public void onError(Throwable t);
	public void onComplete();
}
```
- Subscription
```java
public interface Subscription{
	public void request(long n);
	public void cancel();
}
```
### 구현 규칙
#### Publisher
- Publisher가 Subscriber에게 보내는 onNext signal(data)은 구독요청된 데이터의 총 개수 이하여야 한다.
- Publisher는 onComplete(데이터처리 성공시) 또는 onError(데이터 처리 실패시)를 통해 구독을 종료한다.
- 구독이 종료된 이후에는 signal을 발행해서는 안된다.
#### Subscriber
- onNext signal 수신을 위해 Subscription.request()을 통해 Demand signal을 보내야한다.
- onComplete, onError는 Subscription 또는 Publisher의 메서드를 호출해서는 안된다.
- 구독이 더이상 필요하지 않은 경우 Subscription.cancel()을 호출해야한다.
- onSubscribe는 지정된 Subscriber에 대해 최대 한 번만 호출 되어야 한다.
#### Subscription
- 구독은 Subscriber가 onNext, onSubscribe 내에서 Subscription.request를 동기적으로 호출(동일 스레드가 호출)할 수 있게 허용해야한다.
- 구독 중에 Subscription.cancel()은 Publisher가 Subscriber에게 보내는 signal 발행을 중지하고, 구독자에 대한 참조를 삭제하도록 요청해야한다. (GC에서 유효하지 않은 Subscriber 객체를 수집하여 메모리를 확보한다)
- Subscription.cancel()에 대한 응답으로 예외를 던져서는 안된다.
- Subscription.request(long n)의 매개변수가 0 이하이면, IllegalArgumentException과 함께 onError signal을 보내야 한다.
- 구독이 취소된 이후에는 Subscription.request와 Subscription.cancel이 추가 호출되더라도 효력이 없어야 한다.
- 구독은 무제한의 request 호출(Unbounded Stream)을 지원해야하며, 최대 2^63-1개의 Demand를 지원해야한다.
