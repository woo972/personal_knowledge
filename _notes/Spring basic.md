---
tags: dev, web, spring, java
---
- Spring Framework Documentation (5.3.9) : https://docs.spring.io/spring-framework/docs/5.3.9/reference/html/index.html
### Spring IoC Container 
#### Container Overview
- link: https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-basics
- `org.springframework.context.ApplicationContext` 가 metadata(@Configuration, @Bean, @Import, @DependsOn)를 읽음으로써 bean의 인스턴스화, 설정 등을 어떻게 해야할지 알고 있다. 
- ApplicationContext는 bean들의 종속관계를 알고 있으며, `T getBean(String name, Class<T> requiredType)` method를 통해 bean의 instance를 조회할 수 있다. 그러나 application에서 getBean method를 써서는 안되고(=Spring API에 대한 종속성을 가진 코드를 작성해서는 안되고), metadata를 통해 특정 bean에 대한 종속성을 선언해야한다(@Autowiring 등)
#### Bean Overview
- link: https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-definition
- Bean을 생성할 때 Container에 제공할 configuration metadata를 작성해야하며 이를 bean definition이라고 한다. 
- Container 내에서 BeanDefinition 오브젝트는 아래와 같은 metadata를 가지고 있다.
	- package-qualified class name
	- Container내에서의 bean의 행위 (scope, lifecyle callbacks 등)
	- 다른 bean과의 종속성
	- pool size, connection 수 등 기타 configuration
	- 위 정보는 아래와 같은 property로 설정된다
	  ![](Pasted%20image%2020221115223622.png)
##### Instantiating Beans
- 통상적으로 bean은 container가 [Java Reflection](Java%20Reflection.md)을 사용해 constructor를 호출함으로써 생성된다. (new 연산자를 사용하는 것과 어느정도 동일하다)
- static factory method를 통해 오브젝트를 생성하는 경우에는 container가 class에 존재하는 static factory method를 호출하여 bean을 생성한다.
#### Dependencies
- DI(Dependency Injection)은 오브젝트에 대한 종속성을 생성자 arg, factory mothod arg, 혹은 인스턴스화 이후 set property를 통해 주입하는 과정을 말한다. Container는 bean 생성 시점에 이러한 종속성을 주입하며, 이 과정은 클래스 직접 생성, 혹은 [Service Locator Pattern](https://luv-n-interest.tistory.com/1113)을 활용하는 제어역전이다. 
- 생성자 기반 DI와 Setter 기반 DI가 있으며,  통상 생성자 기반 DI를 사용하고 특수한 경우에만 Setter 기반 DI를 사용해야 한다. Setter 기반 DI의 장점은 종속성 주입을 나중에 할 수 있다는 점이며, 가장 큰 단점은 클래스 인스턴스화 시점에 종속성 주입이 되지 않으므로, null체크가 항상 필요할 수 있다는 점이다.
