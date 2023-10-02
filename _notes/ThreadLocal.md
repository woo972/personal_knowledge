---
tags: dev, java, info
---
- oracle document: https://docs.oracle.com/javase/7/docs/api/java/lang/ThreadLocal.html
- 한 thread에서 실행되는 코드가 동일한 객체를 사용할 수 있도록 한다.
	- 다수의 layer에서 공통적으로 사용하는 값이 있을 때,  
		- layer간 parameter로 넘기는 경우: 중복코드가 다수발생하고 layer 필요 없는 parameter를 받게 될 수도 있다.
		- public static 변수로 선언하는 경우: mutable한 값이면 멀티 스레드 환경에서 동시성 문제가 발생할 수 있다.
		- thread local을 사용하는 경우: 위와 같은 side effect 없이 공통 값에 접근할 수 있다. 다만, Thread pool 운영시 Thread 사용 완료 후 pool에 반납되어 재사용 되기 때문에, 다음 요청을 위해서 초기화해주어야 한다.
- [[InheritableThreadLocal]]