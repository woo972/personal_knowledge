---
tags: dev, java, info
---
- oracle document: https://docs.oracle.com/javase/7/docs/api/java/lang/InheritableThreadLocal.html
- 부모 Thread에서 생성된 값을 자식 Thread에서 상속하여 사용하게 하는 클래스 
- 통상 spring-web에서는 하나의 request당 하나의 thread만을 처리하기 때문에 문제가 없지만, spring-webflux처럼 하나의 request 처리 시 여러개의 thread를 넘나드는 경우에는 InheritableThreadLocal을 사용해볼 수 있음
- zipkin에서 현재 thread에서 다른 thread로 넘어갈 때 문제가 없도록 하기 위해  CurrentTraceContext에 InheritableThreadLocal을 사용하고 있음
- 잘못 사용하는 경우, 한 요청에서 다른 요청으로 정보가 유출될 수 있음
	- 관련 아티클: https://stackoverflow.com/questions/14498503/why-should-i-avoid-using-inheritablethreadlocal-in-servlets