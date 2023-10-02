---
tags: dev, cs
---
## 개요
---
- TCP/IP 계층 중 어플리케이션 계층에서 HTML 문서같은 리소스를 주고 받기 위한 [[TCP]] 기반 프로토콜.
- 클라이언트가 request 메시지를 보내면 서버에서 response 메시지를 보낸다.
- 보안을 위해 확장된 버전으로 [[TLS(SSL)]]기반으로 수행되는 HTTPS가 있다.
## 특징
---
- 확장성: 클라이언트와 서버간에 헤더값을 통해서 새로운 기능을 추가할 수 있음
	- 쿠팡에서는 X-manual-override: {hosts:modular_list:{host:111.222.333.444, port:80}}과 같은 형식으로 보내면 API gateway에서 프로덕션 서버가 아니라 명시된 호스트로 요청을 리다이렉션 시켜줌
- 무상태: 각 요청들 사이에는 상태 공유가 되지 않음. 상태를 저장하기 위해서는 헤더에 쿠키를 넣는 방식 등을 고려해야함
	- 쿠팡에서는 AB테스팅을 하거나 장바구니를 보여주는 등 각 요청이 동일 고객으로 부터 온 것인지 확인하기 위해 리퀘스트 header.Cookie에 memberSrl=1234;pcid=1234;X-coupang-target-market:KR 등의 정보를 넘기도록 되어 있음
## 흐름
---
- TCP 연결을 연다
- 요청메시지를 보낸다(HTTP 메서드, path, 프로토콜 버전과 헤더로 구성)
```http
GET / HTTP/1.1 
Host: developer.mozilla.org
Accept-Language: fr
```
- 응답 메시지를 받는다(프로토콜 버전, status code, status message와 헤더, 메시지 바디로 구성)
```
HTTP/1.1 200 OK
Date: Sat, 09 Oct 2010 14:28:02 GMT
Server: Apache
Last-Modified: Tue, 01 Dec 2009 20:18:22 GMT
ETag: "51142bc1-7449-479b075b2891b"
Accept-Ranges: bytes
Content-Length: 29769
Content-Type: text/html

<!DOCTYPE html... (here comes the 29769 bytes of the requested web page)
``` 
## 세부설명
---
### HTTP 요청 메서드
- GET
	- 형식: GET /path?query=abc&id=123 +
	- 특징: 
		- 멱등성: 동일한 요청을 여러번 보내도 같은 응답을 받는 것이 보장됨
		- 안정성: 서버의 상태를 바꾸지 않음
		- 캐시가능
		- 요청에 본문이 없음
	- 설명: URL에 모든 내용을 담고 있어 페이지 번호 이동이나 즐겨찾기 등에 유리. 즉, 리소스 조회에 특화
- POST
	- 형식: POST /path
	- 특징
		- 멱등성 보장하지 않음
		- 안정성 없음
		- 캐시가능하나 보통 안함
		- 요청이 분문에 있음
	- 설명: 보통 새로운 데이터를 등록할 때 사용, 서버에 데이터를 추가하기 때문에 멱등성과 안정성 보장이 되지 않음.  (쿠팡에서는 상품목록 조회 시 필터 정보가 많기 때문에 GET이 아니라 POST로 보냄)
- PUT
- PATCH
- DELETE
- CONNECT
- OPTIONS
- TRACE
### HTTP 상태 코드
- 200 OK: 요청 성공
- 201 Created: 리소스 생성 성공
- 400 Bad Request: 서버측에서 처리 불가능한 요청을 받음(리퀘스트 형식 오류 등등)
- 401 Unauthorized: 인증 정보가 불충분하여 요청 실패
- 500 Internal Server Error: 예상치 못한 서버측 에러
### CORS
- Cross Origin Resource Sharing의 약자
- 서로 다른 도메인에 접근할 때 보안 제한을 하기 위한 기능
- 예) 사용자가 www.test.com 도메인을 방문했을 때, 특정 image는 www.another.com 도메인의 리소스를 가져와야 하는 상황에서 Allowing Cross-Origin Requests가 되어있지 않으면 CORS 에러가 발생함. Springboot 기준, 아래와 같이 서버 사이드에서 CORS허용 설정을 해야함
```java
@Configuration  
public class CorsConfig implements WebMvcConfigurer {  
  
	@Override  
	public void addCorsMappings(CorsRegistry registry) {  
		registry.addMapping("/**")  
			.allowedOrigins("http://example.com") // Replace with the allowed origin(s) for your application  
			.allowedMethods("GET", "POST", "PUT", "DELETE") // Specify the allowed HTTP methods  
			.allowedHeaders("*") // Allow all headers (you can customize this based on your needs)  
			.allowCredentials(true); // Enable support for credentials (e.g., cookies)  
	}  
}
```
## Appendinx
---
[HTTP 개요 - HTTP | MDN (mozilla.org)](https://developer.mozilla.org/ko/docs/Web/HTTP/Overview)