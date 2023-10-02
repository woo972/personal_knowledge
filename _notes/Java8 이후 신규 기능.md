---
tags: dev, java
---
## Java9
- jShell: Java expression과 statement를 사용할 수 있는 interactive shell
	- REPL tool로 Java의 모든 코드를 작성하지 않아도, code snippet 실행이 가능하다
```shell
$ jshell
jshell> int a = 5;
a ==> 5 
jshell> int b = 10; 
b ==> 10 
jshell> int sum = a + b; 
sum ==> 15 
jshell> System.out.println(sum); 15
```
- Java Platform Module System: modular application 생성과 명시적 종속성 정의 가능
	- Java code를 module로 정리할 수 있고 모듈간의 종속성을 가시성있게 표현
	- 아래 예시에서 `com.example.myapp`은 `some.module`과 `another.module`이 필요하며, 해당 패키지는 외부로 노출된다는 점이 명시되어 있음
```java
// module-info.java
module com.example.myapp {     
	requires some.module;
	requires another.module;
	exports com.example.myapp; 
}
```
- Stream API에 dropWhile, takeWhile, ofNullable 추가
	- 순차적으로 stream element에 접근하여, 조건이 성립하는 동안(while)만 drop/take하고 조건이 성립하지 않는 순간부터는 중단
```java
public List<Integer> dropWhile(){  
    List<Integer> list = List.of(1,2,3,4,5,3,2,1);  
    return list  
            .stream()  
            .dropWhile(n -> n >=3)  
            .toList();  // => 결과: 1,2,3,4,5,3,2,1
}
```
- native process를 쉽게 관리할 수 있는 ProcessHandle 클래스 추가
```java
import java.lang.ProcessHandle; 
public class ProcessExample {
	public static void main(String[] args) {
		ProcessHandle currentProcess = ProcessHandle.current(); 
		System.out.println("Process ID: " + currentProcess.pid());
		System.out.println("Is alive? " + currentProcess.isAlive()); // Destroy the process 
		currentProcess.destroy(); 
		System.out.println("Is alive? " + currentProcess.isAlive()); 
	} 
}
```
## Java10
- 지역변수에 var 타입으로 선언가능
- Optional에 orElseThrow, stream 추가
## Java11
- Java EE 모듈 삭제 (Jakarta EE로 따로 개발)
	- JSP, Java Servlet 등
- 람다 파라미터로 var 사용 가능
```
BiFunction<Integer, Integer, Integer> add = (var a, var b) -> a + b;
```
- 표준 HTTP client 라이브러리인 HTTP Client API 추가
	- 기존에는 HttpURLConnection 활용
```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class HttpClientExample {
    public static void main(String[] args) throws Exception {
        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                .uri(new URI("https://api.example.com/data"))
                .build();

        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
        System.out.println(response.body());
    }
}

```
- Epsilon GC 구현 (퍼포먼스 테스팅시 유용한 no-op GC)
	- memory 할당 시 GC를 수행하지 않기 때문에, memory가 어떻게 활용되는지 누수는 없는지 확인하기 좋고 알고리즘 성능이나 동시성 테스트가 용이하다
## Java12
- switch 표현식 추가
	- 변수에 직접할당 가능, 화살표 표현식 사용가능, break 생락가능 등으로 가독성 증가
```
int numLetters = switch (dayOfWeek) { 
	case "MONDAY", "FRIDAY", "SUNDAY" -> 6;
	case "TUESDAY" -> 7;
	case "THURSDAY", "SATURDAY" -> 8; 
	case "WEDNESDAY" -> 9; 
	default -> throw new IllegalArgumentException("Invalid day of the week: " + dayOfWeek); 
};
``` 
- localized number를 포매팅하는 Compact Number Format 추가
	- 1M, 100만원, $100.00 등 각 locale에 맞는 결과 값을 쉽게 얻을 수 있음
	- 통화관련된 값을 하드코딩 없이 없을 수 있음
```
public String formatCompact(long inputNumber, Locale locale, NumberFormat.Style style) {  
    NumberFormat numberformat = NumberFormat.getCompactNumberInstance(locale, style);  
    return numberformat.format(inputNumber);  
}
```
```
NumberFormat numberFormat = NumberFormat.getCurrencyInstance(Locale.US);  
Currency currency = numberFormat.getCurrency();  
System.out.println(currency.getDisplayName(Locale.US)); // US Dollar  
System.out.println(currency.getCurrencyCode()); // USD  
System.out.println(currency.getSymbol()); // US$  
System.out.println(currency.getDefaultFractionDigits()); // 2  
System.out.println(currency.getNumericCode()); //840  
System.out.println(currency.getSymbol(Locale.US)); // $
```
## Java13
- text block을 통해 멀티 라인 String 작성 가능
```
String str = """
	long text
	it is not \n 
""";
```
- switch에서 yeild 키워드 사용 가능 
	- break대신 사용함으로써 값을 return 가능하게함
```
int day = 3; 
int numLetters = switch (day) {
	case 1, 3, 5 -> { yield 5; } 
	case 2, 4, 6 -> { yield 6; } 
	default -> throw new IllegalArgumentException("Invalid day: " + day); 
};
```
## Java14
- 불변 데이터를 위한 클래스를 간결하게 생성할 수 있는 Record 기능 추가
- instanceof 향상
```
Object obj = "object";    
if (obj instanceof String str) { // instanceof 다음 바로 할당이 가능하다  
    System.out.println(str.toUpperCase());  
} else {  
    System.out.println("not a string");  
}
```
- NullPointerException 메시지 향상
```
Exception in thread "main" java.lang.NullPointerException: Cannot invoke "String.length()" because "str" is null
	at java14_practice.HelpfulNullPointerException.main(HelpfulNullPointerException.java:6)
```
- nested class의 private 멤버에 접근을 향상시키는 Nestmate 기능 추가
```
public class OuterClass { 
	private static String outerStaticField = "Outer static field"; 
	private String outerInstanceField = "Outer instance field";

public static void main(String[] args) {
	OuterClass outer = new OuterClass(); 
	InnerClass inner = outer.new InnerClass(); 
	inner.accessOuterMembers(); 
} 

public class InnerClass {
	private String innerInstanceField = "Inner instance field";
	public void accessOuterMembers() { 
	System.out.println(outerStaticField); // Accessing outer static field 
	System.out.println(outerInstanceField); // Accessing outer instance field 
	System.out.println(innerInstanceField); // Accessing inner instance field 
} } }  
```
## Java15
- 클래스나 인터페이스에서 서브클래스를 제한하는 sealed class 기능 추가
```
sealed interface Shape permeits Circle, Triangle
```
- ZGC 추가
	- 대규모 어플리케이션에서 low-latency GC를 달성하기 위해 만들어짐.
	- heap을 고정된 사이즈(2MB ~ 32MB)의 지역으로 나눈 뒤 병렬 GC를 수행하며 application thread와 동시에 수행.
	- Marking 단계: application thread가 run하는 동안, 살아 있는 object들을 식별한다.
	- Relocation 단계: 파편화를 줄이기 위해 살아 있는 object들을 다른 지역으로 옮긴다. 이는 heap의 작은 부분부터 점진적으로 진행되어 어플리케이션 퍼포먼스에 최대한 영향이 가지 않게 한다.
## Java16
- Stream API에 두개의 collector를 합치는 teeing collector 추가
```
Collector<T, ?, R> teeing(Collector<? super T, A1, R1> downstream1,
                          Collector<? super T, A2, R2> downstream2,
                          BiFunction<? super R1, ? super R2, R> merger);

```
```
public int teeing(){  
    List<Integer> numbers = List.of(1,2,3,4,5);  
    return numbers.stream().collect(  
            Collectors.teeing(  
                    Collectors.summingInt(n -> n),  
                    Collectors.counting(),  
                    (sum, count) -> (int) (sum / count)));  
}
```
## Java17
- 네이티브 코드와 interaction할 수 있는 foreign function and memory API 추가
- Java applet + Java web start deprecation