---
tags: dev, python
---
업무를 하면서 문자열 등을 다룰 일이 꽤 생긴다. 쉘 스크립트로 처리하면 가장 이상적이겠지만, 워낙 불편해서 펄을 해볼까 하다 업데이트도 잘 안되는 듯 하고... 파이썬이 적합할 것 같아서 기초적인 공부를 해보기로 했다.

  

파이썬 패키지를 일일이 설치하기가 귀찮을 것 같아서 배포판을 선택, 그 중에서도 Anaconda로 설치했다.

(다운로드 : [https://www.anaconda.com/download/](https://www.anaconda.com/download/))

  

C 밑에 바로 설치했는데, C:\Anaconda3\Lib\idlelib <- 이 경로로 가보면 idle.bat 이라는 파일이 있다. 이 것을 실행하면 python shell이 실행되면서 여기에서 간단한 테스트를 해 볼 수 있다.

  

다음은 파이썬 기초 및 특징

1. { }, ( ) 를 쓰지 않는다

쉘 스크립트나 PL/SQL을 쓰면서도 가장 적응이 안되는 부분인데, JAVA나 Javascript와 다르게 { }, ( )를 쓰지 않는다.

코드의 포함관계는 들여쓰기로 해결한다. 때문에 함부로 들여쓰기를 해서는 안된다. (Syntax error가 발생한다)

또한 if문 for문 에서는 괄호 없이 바로 조건문을 적는다.

**2. 기본 데이터 타입**

int, float, string 세 가지의 기본적인 데이터 타입이 존재한다.

3. 문자열은 ''와 ""로 표현가능

Javascript와 마찬가지로 문자열은 '' 안이나 "" 안에 넣어서 쓰면 된다.

4. 변수 타입을 정하지 않는다

int, char, float 등 타입을 명시하지 않고 바로 var = 'test_var' <- 이와 같이 변수를 선언하여 사용한다.

**5. 기본 자료구조**

리스트, 튜플, 딕셔너리라는 파이썬 자체의 기본 자료구조가 있다.

1) 리스트 : 일반적인 그 리스트와 비슷하다. list1 = ['a','b','c']와 같이 선언하며, 요소에 접근하기 위해서는 list1[0]과 같이

[] 안에 인덱스 번호를 주면 된다. 특이하게도 list1[-1]처럼 뒤에서부터 세는 방법도 있는데 이는 튜플에서도 쓸 수 있다.

아래와 같은 주요 메서드가 있다.

(1) append : list1.append('d')의 형태로 사용하며, 리스트 맨 끝에 요소를 추가한다.

(2) insert : list1.insert(2, 'd')의 형태로 사용하며, 지정한 인덱스에 요소를 추가한다.

  2) 튜플 : 조작이 불가능한 배열이라고 해야할 듯 싶다. tu = ('a', 'b', 'c')와 같이 선언하며, 삽입이나 삭제가 되지 않는다.

리스트보다 속도가 빠르다는 장점이 있다. 접근은 리스트와 동일한 방식으로 한다.

3) 딕셔너리 : 맵 자료구조와 같다. dic = {'key1':'value1', 'key2':'value2'}와 같이 선언하며, dic['key1']과 같이 키 값을

인자로 주면 해당하는 값을 꺼낼 수 있다. 아래와 같은 주요 메서드가 있다.

(1) keys : dic.keys()의 형태로 사용하며, 딕셔너리가 가진 키 값들을 반환한다.

(2) values : dic.values()의 형태로 사용하며, 딕셔너리가 가진 밸류 값들을 반환한다.

(3) items : dic.items()의 형태로 사용하며, 키와 밸류의 쌍을 반환한다.

**6. 자료구조 다루기**

자료구조를 다루는 공통적인 함수 및 방법들이 있다.

1) del : del(list1[0])의 형태로 사용하며 인자로 넣은 요소를 삭제한다.

위에서 언급했듯이 튜플은 선언 이후 데이터 조작이 불가능하기 때문에 del을 사용하면 에러가 발생한다.

(del(tup)의 형태로 변수 자체를 삭제하는 것은 가능하다)

2) len : len(tup)의 형태로 사용하며 인자로 넣은 변수의 요소의 수, 문자열의 길이 등을 반환한다.

3) list : list(dic.keys())의 형태로 사용하며 인자로 넣은 값을 리스트로 변환하여 반환한다.

(list("abcd")와 같이 문자열을 넣으면 ['a','b','c','d']와 같은 형태로 바꾸어 준다)

4) slicing : 리스트 등의 자료구조에서 원하는 범위의 값을 가져올 때 쓰는 방법이다. list1[2:5]와 같은 형태로 사용하는데,

이는 앞의 인덱스의 요소를를 포함하고 뒤의 인덱스의 요소는 포함하지 않는 범위를 선택하게 해준다.

list1[0:], list1[:3]처럼 써서 ~부터 끝까지, 혹은 처음부터 ~까지의 범위를 지정할 수도 있다.

**7. 조건문**

다음과 같은 형태로 조건문을 사용한다.

1) if 문 : 

if a < 0:

print('OK')

2) if ~ else 문 :

if a < 0 :

print('OK')

else :

print('NOT OK')

3) if ~ else if 문 :

if a == 0 :

print('1')

elif a == 1 :

print('2')

elif a == 2 :

print('3')

**8. 반복문**

다음과 같은 형태로 반복문을 사용한다.

for var in range(1, 10):

print(var)

위 코드에서 range(1, 10)은 1 이상 10 미만을 반환해준다. range 대신 리스트나 튜플이 올 수도 있고 딕셔너리를 써도 된다.

딕셔너리 자료구조를 쓸 경우에는 아래와 같이 사용한다.

for k, v in dic.items():

print("%s : %s", %(k, v))

위 코드에서 %s로 표현된 부분은 C의 printf처럼 사용하는 것이며, 쉼표(,)뒤에 % 이후 작성한 변수를 읽어들인다.
**1. 함수** 

   파이썬에서 함수는 아래와 같이 작성한다.

   def do_something(input_value):

       return_value = input_value + 1

       return return_value

  

**2. 모듈**

   코드 재사용을 위해서 함수와 변수들을 묶어 모듈화할 수 있다. 예를 들어 my_module.py라는 파일에 다음과 같이 정의한 경우,

   _def my_function():_

       _pass_   

   _my_var = 'test'_    

   _if __name__ == "__main__":_

      _print('main code')_

  

   다른 파일에서 _import my_module_ 이라고 선언함으로써 _my_module.my_function, my_module.my_var_의 형태로 접근할 수 있다.

   위 예시에서 if문으로 시작하는 부분은, 해당 파일을 import가 아닌 직접  실행할 때만 구동되는 코드이다. 

   파이썬은 Java나 C++처럼 main부가 따로 없기 때문에 위와 같은 형태를 쓰는 것 같다.

   모듈을 import 하는 방법이 하나 더 있는데, _from my_module import my_function_ 처럼 특정 기능을 명시하는 것이다.

   이렇게 하면 _my_module.my_function()_이 아닌 _my_function()_ 그 자체로 호출할 수 있다.

**3. 클래스**

    파이썬에서는 클래스 또한 지원한다. 아래와 같은 형태로 사용하며, __init__은 생성자, __del__은 소멸자이다.

    _class MyClass():_

        _var = 0_

        _def __init__(self, input_value):_

            _self.input_value = input_value_

            _var += 1_

        _def __del__(self):_

            _var = 0_

     클래스를 상속할 때는 다음과 같이 쓴다. 

     단순히 클래스의 인자에 부모 클래스를 넘기기만 하면되며, 부모 클래스의 생성자를 활용할 경우에는

     super.__init__()을 호출해서 사용하면 된다.

     _class MyClass(ParentClass):_

        _def __init__(self):_

            _super.__init__()_

            _self.parent_func1('input_value')_

     클래스를 활용하려면 인스턴스를 생성해야 한다. _c1 = MyClass()_와 같이 쓴다.

  

**3. 파일입출력**

    파일입출력은 Java보다는 C형태에 좀 더 가깝다. 

    1) 파일입력

        _f = open('C:\\Users\\user\\Desktop\\test.txt','rt')_

        _line_of_file = f.readlines()_

        위 코드에서 r은 read의 의미이고 t는 text파일이라는 뜻이다. 경로는 \ 하나가 아닌 \\ 두개를 써야함을 명심하자.

     2) 파일출력

        _f = open('C:\\Users\\user\\Desktop\\test.txt','wt')_

        _f.write('test message')_

        _f.close()_

        파일 출력은 r 대신 w를 쓴다는 점이 다르다. _f.write()_로 내용을 입력하고 _f.close()_로 닫아준다.