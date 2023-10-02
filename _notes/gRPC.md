---
tags: dev, request, url, endpoint, 통신, api
---
- 보통 마이크로서비스에서 내부 서버간 동기 통신에 사용한다
- 통상 사용하는 HTTP 프로토콜을 사용한 Restful API과 비교하여 다음과 같은 특징이 있다.
	- HTTP/2 프로토콜을 사용한다
	- 바이너리를 활용하여 payload 용량이 작지만, 사람이 읽을 수 없다(?)
	- 더 빠른 통신이 가능하다