---
tags: java, dev, memory
---
Java memory 영역은 아래와 같이 나뉜다.
- Heap
	- java의 모든 쓰레드가 공유하는 영역으로, object들이 runtime에 할당되는 곳이다.
	- Young Generation: object가 새로이 생성되면 할당되는 영역으로 Eden, S0, S1의 세 영역으로 세분화 된다. Eden에서 시작해서 S0<->S1을 이동하며 GC([[Garbage Collection]])에서 살아남은 object는 Old 영역으로 이동한다.
	- Old Generation: 오래 살아남은 object들이 존재하는 영역으로 minor GC의 대상이 아니다. Young Generation보다 큰 영역을 할당한다.
- Method Area(PermGen / Metaspace)
	- Java8 이전에는 PermGen으로 불렸다.
	- Java8 이후로는 Metaspace라고 불린다. 클래스 구조나 상수 pool, 기타 meta 정보들이 저장되며 동적으로 할당되는 영역이다.
- Stack
	- 각 thread는 각자의 Stack을 가지고 있으며, 로컬변수나 메서드 파라미터, 계산 결과 등을 저장하는데  필요하다.
- PC Register
	- thread는 현재 실행포인트(execution point)를 트래킹 하는 program counter를 가지고 있다.
	- 다음으로 실행할 instruction의 주소를 가지고 있다.
- Native Method Stacks
	- JVM에 의해 관리되는 JAVA 이외 언어로 작성된 native method를 위한 데이터를 가지고 있다.