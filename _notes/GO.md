---
tags: dev, go
---
# Go VS CODE 환경설정
- go 인스톨 및 gopath 지정
- vscode: go 확장 설치 (하단 missing plugins 모두 설치 )
- vscode: 설정에서 gopath 검색 후 settings.json에 지정
- vscode: 터미널에서 go env -w GO111MODULE=auto 실행


## Web Programming
https://thebook.io/006806/ch08/03/
- net/http 를 이용해서 웹 프로그래밍을 할 수 있다
-  라우터 -> 미들웨어(로깅, 인증, 에러처리 등) -> 핸들러(로직 수행, 응답헤더 및 본문 작성) -> 렌더러로 이어지는 기본적인 흐름
	- http.ListenAndServe(":8080", nil) 을 통해 서버를 특정 포트로 동작하게 할 수 있다. nil을 부여할 경우 http.DefaultServeMux가 핸들러로 동작한다
		- http.DefaultServeMux ?  (https://www.alexedwards.net/blog/an-introduction-to-handlers-and-servemuxes-in-go)
			- ServeMux : 미리 정의된 url과 해당 handler에 대한 매핑을 저장한다
	- 라우터
		- http.HandleFunc("/", func(w http.ResponseWriter, r \*http.Request){ ... }) 로 패턴 URL 매칭 가능
		- 