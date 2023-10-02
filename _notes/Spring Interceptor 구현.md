---
tags: dev, spring, interceptor
---


[참고: [https://www.baeldung.com/spring-mvc-handlerinterceptor](https://www.baeldung.com/spring-mvc-handlerinterceptor)]

Spring Interceptor 구현

- 사용자 request -> Servlet Filter -> Dispatcher Servlet -> Interceptor -> Controller
- HttpServletRequest, ModelAndView 등을 제공 받기 떄문에 필터보다 더 정교하게 로직 구현 가능
- Interceptor가 Spring Bean이므로 다른 Bean으로 DI 받아 활용 가능
1. Dependency 설정
compile(org.springframework.boot::spring-boot-starter-web')
2. Interceptor 구현
@Component
public class HttpInterceptor implements HandlerInterceptor{
@Override
public boolean preHandler(HttpServletRequest req,
HttpServletResponse res,
Object handler) throws Exception {
// example code
HttpSession session = req.getSession(false);
if(session != null){
    
    ```
    // session이 있지만 token이 없는 경우, 옳지 못한 token인 경우 logout 처리 하도록 변경하는 로직 추가
     	...
     
     	return true; // 호출한 uri의 컨트롤러로 이동
     }else{
     	try{
     		//req.getSession(true);
     		res.sendRedirect("/users/login"); // 로그인이 되어있지 않으면 로그인 페이지로 이동
     		
     	}catch(IOException e){
     		// error on redirection
     	}
     	return false;
     }
    ```
    
    }
    @Override
    public void postHandler(HttpServletRequest req,
    HttpServletResponse res,
    Object handler,
    ModelAndView model) throws Exception {
    ....
    }
    @Override
    public void afterCompletion(HttpServletRequest req,
    HttpServletResponse res,
    Object handler,
    Exception e) throws Exception {
    ....
    }
    

//	@Override
//	public void afterConcurrentHandlingStarted(HttpServletRequest req,
// HttpServletResponse res,
// Object handler) throws Exception {
//	...
//	}
}

- preHandler
컨트롤러 진입 전에 수행
true일 경우 정상 진행, false일 경우 컨트롤러 진입 X
Object handler는 핸들러 매핑이 찾은 컨트롤러 클래스 객체
- postHandler
컨트롤러 진입 후 view 랜더링 되기 전 수행
ModelAndView를 통해 화면 단에 전송되는 데이터 조작 가능
- afterCompletion
view가 정상적으로 랜더링 된 이후 수행
- afterConcurrentHandlingStarted
비동기 요청시 postHandler와 afterCompletion을 수행하지 않고 본 메서드 수행
1. 인터셉터 등록
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {
@Autowired
private HttpInterceptor interceptor;
    
    @Override
    public void addInterceptors(InterceptorRegistry registry){
    registry.addInterceptor(interceptor)
    .addPathPatterns("/**")
    .excludePathPatterns("/user/**")
    }
    }
    
- addInterceptor(interceptor) 등록할 인터셉터 설정
- addPathPatterns("/**") 적용할 url 패턴
- excludePathPatterns("/user/**") 제외할 url 패턴

- IRP 
		- 퇴직전 개설하여 퇴직금을 일시 수령이 아닌 IRP로 넣어두면 세제혜택
		- 연간 납입 한도는 총 1800만원
		- 자유 적립식 가능
		- 공격형 투자는 70%까지 가능
		- 중도 출금 불가 (만 55세까지)
		- 최대 900만원 한도로 세액공제 대상 
		- 퇴직 소득세의 30%감면(연금으로 수령시)
		- irp 계좌에서 발생하는 이자, 배당수익에 대해서는 15.4% 소득세 부과 제외(연금 수령시 소득세 5.5% 부과 - 과세이연 효과)