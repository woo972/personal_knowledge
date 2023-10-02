---
tags: dev, apigateway, proxy
---

proxy 역할

인증/권한 

서비스 검색 통합

응답 캐싱

서킷브레이커

로드밸런싱

로깅

ip white list 

클라이언트에서 직접 마이크로 서비스 서비스를 호출하는게 아니라, api gateway를 호출하도록 함 

spring cloud에서 msa간 통신

restTemplate

RestTemplate r = new RestTemplate();

r.getForObject("주소", User.class, 200);

fegin client

[FeignClient("stroes")](API%20Gateway%20Service%20ea73b6b3c6b34f9492e15b5da4e181ab/FeignClient(%20stroes%20)%201fa71b5c014a4442a8739c77ce3ea801.md) ← 호출할 msa 

public interface StoreClient{

[GetMapping("/stores")](API%20Gateway%20Service%20ea73b6b3c6b34f9492e15b5da4e181ab/GetMapping(%20stores%20)%206b49865455464a93af4fc8a5f00d32ff.md) 

List<Store> getStores();

로드밸런서를 어디에 둘것인가?

ribbon : client side (비동기처리가 안됨, deprecated), api gateway 대신 사용

- 서비스 이름으로 호출
- health check

netflix zuul: deprecated 

- routing
- api gateway
- spring boot: 2.3.8

예제

first service: localhost:8081/welcome

```jsx
@RestContorller
@RequestMapping("/")
public class FirstController{
	@GetMapping("/welcome")
	public String hello(){
		return "first";
	}
} 
```

second service : localhost:8082/welcome

zuul service: @EnableZuulProxy 

```jsx
zuul:
	routes:
		first-service: // 임의 이름
			path: /first-service/**
			url: http://localhost:8081
		second-service:
			...
```

```jsx
@Component
public class ZuulLoggingFilter extends ZuulFilter{
	Logger logger = LoggerFactory.getLogger(ZuulLoggingFilter.class);
	// 실제 동작 구현
	public Object run() throws Exception {
		RequestContext ctx = RequestContext.getCurrentContext();
		HttpServletRequest req = ctx.getRequest();
		logger.info(req.getRequestURI()); //  /second-service/welcome 이 출력
		return null;
	}
	// 사전 / 사후 구분
	public String filterType(){
		return "pre";
	}
	// 필터가 여러개일 때 적용 순서
	public int filterOrder(){
			return 1;
	}
	// 필터로 사용여부
  public boolean shouldFilter(){
		return true;
	}
}
```

localhost:8000/first-service/welcome → first serivce 호출됨

Spring Cloud Gateway?

```jsx
spring:
	cloud:
		gateway:
			routes:
				- id: first-service
					uri: http://localhost:8081/
					prediecates: // 조건절
						- Path=/first-service/** // zuul의 path에 해당
				- ...
```

```jsx
@RestContorller
@RequestMapping("/first-service") // 수정해줘야 함  http://localhost:8081/first-serivce/welcome 으로 호출할 것 이기 때문에
public class FirstController{
	@GetMapping("/welcome")
	public String hello(){
		return "first";
	}
} 
```

filter

request / response header 값을 넣는다

→ 다른 서비스에서는 header값에 뭔가 추가된 것을 확인할 수 있다

gateway handler mapping (요청 정보가 들어오면)

predicate (어떤 정보인지 판단하고)

prefilter /postfilter  ← java code / property (필터를 적용한다)

routing과 filter를 application.yml이 아니라 java code에서도 설정이 가능하다

```jsx
@Configuration
스프링부트에서 위 어노테이션이 등록된 빈을 먼저 실행시킨다
public class FilterConfig {
	@Bean
	public RouteLocator filter(RouteLocatorBuilder builder){
		return builder.routes()
									.route(
											r -> r.path("/xxx/**)
														.uri("http://xxxxx:7777")
														.filters(f -> f.addRequestHeader("headername", "headervalue")
																						.addResponseHeader("headername2","headervalue")
										)
		.build();
	}
}

```

AbstractGatewayFilterFactory 상속받는 방법

```jsx
@Sf4j
@Component
public class CustomFilter extends AbstractGatewayFilterFactory<CustomFilter.Config> {
	public CustomFilter(){
		super(Config.class);
	}
	public static class Config{
		
  }
	@Override
	public GatewayFilter apply(Config config) {
		GatewayFilter filter = new OrderedGatewayFilter((exchange, chain) -> {
			ServerHttpRequest request = exchange.getRequest();
      log.info("request id {}", request.getId());
			return chain.filter(filter);
		}, ORDERED.1);
		return filter;
	}

}
```

```jsx
spring:
	clould:
		
```