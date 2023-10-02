---
tags: dev, java, designpattern
---
자바에서는 new 키워드를 이용해서 객체를 생성한다. 고로 한번만 생성하면 될 객체를 여러번 생성하게 되면 그만큼 메모리에 낭비가 생길 수가 밖에 없다. 때문에 싱글톤 패턴을 적용하여 객체를 한 번만 생성하도록 하는 경우가 있다. 

**public class Test {**

**public static void main(String[] args) {**

**NonSingleton n1 = new NonSingleton();**

**NonSingleton n2 = new NonSingleton();**

**System.out.println(n1 == n2); // false**

**Singleton s1 = Singleton.getInstance();**

**Singleton s2 = Singleton.getInstance();**

**System.out.println(s1 == s2); // true**

**}**

**}**

**class NonSingleton {**

**public NonSingleton() {}** 

**}**

**class Singleton {**

**private static final Singleton** **INSTANCE** **= new Singleton();**

**private Singleton() {}** 

**public static Singleton getInstance() {**

**return** **INSTANCE****;**

**}**

**}**

위 예제에서 n1과 n2는 각기 다른 객체다. 반면, s1과 s2는 같은 객체라는 점을 알 수 있다. 

  

문제(?)는 내가 지금까지 알던 이 패턴에 허점이 있다는 것. 첫째는 해당 인스턴스를 사용할 필요가 없을 때도 생성한다는 점(Lazy implementation으로 해결 가능), 둘째는 직렬화 시에는 또 다시 새로운 객체가 생성된다는 점이고(readResolve() 메서드를 구현해서 해결 가능), 셋째는 아래 코드와 같이 reflection을 이용하면 기껏 만들어 놓은 싱글톤 패턴을 무시할 수 있다는 것이다.

**import java.lang.reflect.Constructor;**

**public class Test {**

**public static void main(String[] args) {**

**Singleton s3 = Singleton.getInstance();**

**Singleton s4 = null;**

**try {**

**Constructor<?>[] constructors =** 

                                    **Singleton.class.getDeclaredConstructors();**

**for ( Constructor<?> constructor : constructors ) {**

**constructor.setAccessible(true);**

**s4 = (Singleton)constructor.newInstance();**

**}**

**} catch (Exception ex) {**

**System.out.println(ex);**

**}**

**System.out.println(s3 == s4); // false**

**}**

**}**

**class Singleton {**

**private static final Singleton INSTANCE= new Singleton();**

**private Singleton() {}** 

**public static Singleton getInstance() {**

**return** **INSTANCE****;**

**}**

**}**

  

그래서 어떻게 하면 되느냐, enum을 사용하면 된다. 아래 처럼

**public class Test {**

**public static void main(String[] args) {**

**Singleton s3 = Singleton.INSTANCE;**

**Singleton s4 = Singleton.INSTANCE;**

**System.out.println(s3==s4);  // true**

**}**

**public enum Singleton {**

**INSTANCE;**

**}**     

        * 예시라서 이렇게 썼지만 실제로 이 부분은 다른 어딘가에 있을 것이다

        * 그 외에 변수나 메서드 등등 필요한게 있을 것이다

**}**

매우 심플해지지 않았는가? 직렬화 구현도 필요 없고, reflection으로부터도 안전하며, 객체가 여러개 생기는 문제도 없는 해결책이다. 

_(단, 직렬화시 멤버변수는 잃게 된다고 한다....https://docs.oracle.com/javase/6/docs/platform/serialization/spec/serial-arch.html#6469)_  

  

참고: https://dzone.com/articles/java-singletons-using-enum