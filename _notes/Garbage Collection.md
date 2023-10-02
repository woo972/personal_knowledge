---
tags: dev, gc, java, jvm
---
## Overview
---
Java에서는 자동으로 memory를 관리하기 위해 사용되지 않는 object를 정리하며 이를 Garbage Collection이라 부른다. 세부적인 알고리즘과 성능은 JVM 구현과 버전마다 다르며, 통상 Mark and Sweep 전략을 따른다.
- Marking: Garbage Collector가 object들을 순회하며 접근 가능한 object를 "live"로 marking 한다.
- Sweeping: Marking이 끝나면 Garbage Collector는 "live"가 아닌 object들을 식별하여 모은다. 이 object들이 garbage object다.
- Freeing: garbage object가 차지하고 있는 메모리를 heap 영역에 반환한다.
GC는 MinorGC와 MajorGC(FullGC)로 나누어 수행한다.
## Minor GC
---
Young Generation 영역에서 아래와 같은 절차로 수행된다.
- Initial marking: garbage collector가 live object들을 식별하여 marking한다.
- Concurrent marking: thread가 중지되면 inital marking에서 식별된 object들에 접근가능한지 순회하며 live object를 marking한다. 
- Remarking: concurrent marking 단계에서 다시 접근가능해지거나 수정된 object들을 최종 marking한다.
- Copying and Compaction: live로 식별된 object들을 eden에서 survivor로, survivor에서 다른 survivor(예, S0 -> S1)로 이동시킨다.(동시에 압축을 수행한다) 이를 통해 메모리 파편화를 줄이고, 적어도 한 survivor는 비어있음을 보장한다.
- Promotion: 몇 번의 minorGC 사이클 동안 살아남는다면, old generation으로 이동시킨다.
- Freeing: garbage로 식별된 object가 점유하고 있는 memory는 반환된다.
## Full GC
---
Young Generation과 Old Generation 영역에서 아래와 같은 절차로 수행된다.
- Stop-the-World: GC가 끝날 때까지 thread들이 중지된다
- Marking: 활성화된 thread에서 직접 참조하고 있는 object부터 시작해서 접근가능한 object를 순회하며 live로 식별한다
- Sweeping: heap 전체 영역에 대해 "live"로 marking되지 않은 object를 모아 garbage(반환대상)로 간주한다.
- Compaction: 메모리의 파편화와 사용 최적화를 위해 압축을 수행할 수도 있다.
- Freeing: garbage object에 의해 점유되는 메모리를 반환한다.

Full GC는 아래와 같은 상황에서 발생한다.
- Young Generation이 full 일 때
- Old Generation이 full 일 때
- Full GC 수행 명령이 명시적으로 내려졌을 때

