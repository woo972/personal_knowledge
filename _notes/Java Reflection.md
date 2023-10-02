---
tags: java, dev, reflection, info
---
## 개요
- Reflection은 런타임에 클래스의 행위를 수정하거나 조회할 수 있게 만든 API이다.
- 주요 패키지: java.lang, java.lang.reflection, java.lang.Class 
- 사용처 예시 - Intellij 같은 IDE, DEBUGGER, TEST TOOL 
- encapsulation을 깰 수 있고, JVM 최적화가 되지 않으므로 되도록 사용하지 않는 것이 좋음
## Reflection의 기본 활용
### 주요 method
![](Pasted%20image%2020221110232859.png)
### 주요 예시
#### 클래스의 인스턴스를 얻는 3가지 방법
###### forName() method of Class class
- 클래스를 동적으로 로드하기 위해 사용한다
- class명을 알고 있을 때 사용한다. 
- 원시타입에는 사용 불가능하다
```java
class TestClass {}

public void main() {
	Class c = Class.forName("TestClass");
}
```
###### getClass() method of Object class
- class의 type을 알고 있을 때 사용한다
- 원시타입에도 사용가능
```java
class TestClass {}

class NameClass {
	public void getName(Object o){
		Class c = o.getClass();
		return c.getName();
	}
}

public void main(){
	TestClass t = new TestClass();
	NameClass n = new NameClass();
	n.getName(t);
}
```
###### .class Syntax
- type이 존재하고 인스턴스가 아닐 때 사용 가능하다
- 원시타입에도 사용가능하다
```java
class TestClass {}

public void main(){
	Class c1 = boolean.class;
	Class c2 = TestClass.class;
}
```
#### Class object 판단
```java
interface MyInterface {}

public void main(){
	Class c = Class.forName("MyInterface");
	c.isInterface()
}

```
#### newInstance()
###### 기본적으로는 NO-ARGS CONSTRUCTOR로 생성한다
```java
class MyClass {}

public void main(){
	Class c = Class.forName("MyClass")
	MyClass m = (MyClass)c.newInstance();
}
```

```java
class MyClass {
	private String msg;
	MyClass (String msg){
		this.msg = msg;
	}
}

public void main(){
	Class[] s = {String.class};
	Class c = Class.forName("MyClass");

	Constructor con = c.getConstructor(s);

	String msg = "hello";
	Object o = con.newInstance(msg)
} 
```
#### private method 접근
- setAccessible method를 통해 private method 접근이 가능하다
```
public class MyClass{
	private void sayHello(){
		System.out.println("hello");
	}

	private void sayHelloTo(String name){
		System.out.println("hello "+name);
	}
}

public class Caller{
	public void main(){
		Class c = Class.forName("MyClass");
		Object o = c.newInstance();
		Method m = c.getDeclaredMethod("sayHello", null);
		m.setAccessible(true);
		m.invoke(o, null);

		Method m2 = c.getDeclaredMethod("sayHelloTo", new Class[]{String.class});
		m2.setAccessible(true);
		m2.invoke(o, "Tom");
	}
}
```
>[!note]
>아래와 같은 방법으로 private 생성자에도 접근 가능하다.
>Constructor<T> con = T.class.getDeclaredConstructor();
> con.setAccessible(true);
- Reflection으로 인해 singleton 패턴 구현 시 주의 점: [Java Singleton Pattern](Java%20Singleton%20Pattern.md)

## Spring @Bean 생성 원리

- AbstractAutowireCapableBeanFactory.createBean(Class<T> beanClass)
	- RootBeanDefinition(beanClass)로 bean definition 생성 및 scope(prototype)으로 설정
	- 생성된 bean definition으로 bean 생성
- 