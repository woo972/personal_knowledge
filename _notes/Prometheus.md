---
tags: dev, monitoring, metrics
---
## 특징
---
- Dimensionality: 매트릭 이름에 태그(key-value)붙여서 복잡한 정보를 표현할 수 있다
- Publishing: 매트릭을 polling 해간다 
- Rate aggregation: 누적된 값을 받아 통계를 산출한다
	- Counter 등 CPU 사용시간 처럼 증가하기만 하는 매트릭의 경우, 지정한 시간 범위내에서의 변화량을 연산한다 `(sample(t2) - sample(t1)) / (t2 - t1)`
		- 프로세스 재실행 등으로 메트릭값이 감소하면 리셋으로 간주하고 0에서부터 재측정한다
### 데이터 타입
---
- Instant Vector: 같은 시간대의 샘플 집합
	- `http_request {container = A}`, `http_request {container = B}` 등
- Range Vector: t1 ~ t2 시간 사이의 샘플 집합
	- `http_request {container = A}`
- Scalar: 시간 정보가 없는 값