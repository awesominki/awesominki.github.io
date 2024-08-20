---
layout: page
title: Java note
hide_description: true
sitemap: false
permalink: /java/ 
---

## JVM이란?

JVM은 Java virtual Machine의 줄임말로, OS와 Java Application 사이를 중재해주는 가상 머신이다.

### Java Application이 JVM에서 실행되는 과정

1) JVM은 OS로부터 적당한 메모리(Runtime Data Area)를 할당 받음.

2) 자바 소스 코드(.java)를 자바 컴파일러(javac)에 의해 바이트코드(.class)파일로 변환

3) Class Loader를 통해 .class 파일을 [Runtime Data Area](#jvm-runtime-data-area)로 로딩

4) 로딩된 class 파일을 Execution Engine을 통해 해석 및 실행

### JVM Runtime Data Area

![JVM_RDA](https://devfoxstar.github.io/static/e4f83d59e5a364182ec0e47aa97eb27e/065e2/runtime_data_area.png)

- Method Area
  - 클래스가 로딩될 때 할당된다.
  - 패키지, 클래스, 인터페이스, 생성자, 메소드, 필드, Static 변수 등을 저장한다.
  - 모든 스레드가 공유한다.
- Heap Area
  - 런타임 시에 동적으로 할당된다.
  - new 키워드로 생성된 객체와 배열이 저장되는 영역이다.
  - Heap 영역에 생성된 객체와 배열은 Stack 영역의 변수에서 참조한다.
  - [Garbage Collector](#garbage-collector)가 참조하지 않는 메모리를 제거한다.
- Stack Area
  - 스레드마다 존재하며, 스레드가 시작될 때 할당된다.
  - 지역변수, 파라미터, 리턴값, 연산에 사용되는 값 등이 생성되는 영역이다.
  - 메소드를 호출할 때마다 개별적으로 스택이 생성되며 종료시 해제된다.
  - 선입후출의 구조를 가진다.
  - 기본 타입 변수는 Stack 영역에 직접 값을 가진다.
  - 참조 타입 변수는 Method 영역이나 Heap 영역의 참조 주소를 가진다.

### Garbage Collector

![GC](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbL5Th5%2FbtrPT1O6YaL%2FOhImuZZsGZF3L3NmcnY6Tk%2Fimg.png)

기존의 참조 변수와 참조 변수가 가리키는 동적변수 관계에서 참조 변수가 함수 종료에 의해 pop되고 나면 힙 영역에 동적 변수만 남는데, 
이러한 변수를 Unreachable Object라고 표현하고, 이 변수가 GC의 관리 대상이 된다.

GC를 사용하면 다음과 같은 장단점이 있다.

- 메모리 누수 방지
- 해제된 메모리에 대해 접근하는 것을 방지
- 해제한 메모리를 다시 해제하는 것을 방지
- 개발자가 일일히 동적변수의 제거 선언을 하지 않아도 됨
- 그러나, 개발자가 GC가 언제 수행되는지 정확하게 알 수 없음
- 실행 중인 작업을 중단하고, 리소스를 GC 작업에 내줘야 하므로 오버헤드 발생

### 객체지향 프로그래밍의 특징

- 추상화 (Abstraction)
  - 공통의 속성이나 기능을 묶어서 표현하는 방법
  - 예) 동물을 대표하는 Animal Class
- 캡슐화 (Encapsulation)
  - 높은 응집도(동일 클래스만 변경)와 낮은 결합도(다른 클래스 간섭 배제)를 유지하는 설계 방법
  - 데이터 캡슐화 : 필드와 메소드를 클래스로 묶는 방법
  - 은닉화 : 외부에서 객체 상태 변경에 제한을 두는 방법 (접근 제어)
  - 예) Animal Class는 Human Class에서 접근 불가
- 상속 (Inheritance) 또는 일반화 (Generalization)
  - 자식 클래스가 부모 클래스를 물려받고 확장하는 개념
  - 예) Animal Class를 Dog Class에서 상속해서 부모 클래스의 변수와 메서드를 사용
- 다형성 (Polymorphism)
  - 동일 요청에 대해 서로 다른 방식으로 응답할 수 있는 방법
  - Overriding : 상속 받은 메소드를 재정의
  - Overloading : 동일 클래스의 동일한 메소드가 매개 변수의 타입이나 개수에 따라 다르게 동작
  - 예) Animal Class의 Bark Method를 Cat Class의 Bark Method가 Overriding 해서 사용

### 객체 지향 설계의 5가지 원칙 (SOLID)

- S : 단일 책임 원칙
  - SRP : Single Responsibility Principle
  - 객체는 단 하나의 책임만 가져야 한다.
- O : 개방-폐쇄 원칙
  - OCP : Open Closed Principle
  - 기존의 코드를 변경하지 않으면서 기능을 추가할 수 있도록 설계해야 한다.
- L : 리스코프 치환 원칙
  - LSP : Liskov Substitution Principle
  - 자식 클래스는 자신의 부모 클래스에서 가능한 행위를 수행할 수 있어야 한다.
- I : 인터페이스 분리 원칙
  - ISP : Interface Segregation Principle
  - 클래스는 자신과 관련 없는 인터페이스는 구현하지 않는다.
- D : 의존 역전 원칙
  - DIP : Dependency Iversion Principle
  - 상위 모듈은 하위 모듈에 종속 되면 안되고, 둘 다 추상화에 의존해야 한다.
  - 예) 상위 모듈인 자동차를 만드는데 하위 모듈인 스노우 타이어를 참조하지 말고, 상위 모듈인 타이어를 추상화 해서 사용한다.

### 객체 지향 프로그래밍을 하는 이유는?

- 코드의 재사용성 증가
- 유지보수 용이성
- 간결한 코드
- 높은 확장성
- 강한 응집력과 약한 결합력
- 보안성 향상
- 소프트웨어 생산성 향상

### 접근 제어자 종류

|   제어자   |  동일 클래스  |  동일 패키지  |  자식 클래스   | 전체  |
|:-------:|:--------:|:--------:|:---------:|:---:|
| public  |    O     |O|O|O|
|protected|O|O|O|X|
|default|O|O|X|X|
|private|O|X|X|X|

### Static VS Non-Static

- Member

||  static Member  |non-static Member|
|:---:|:-----------:|:---:|
|From| 클래스에 속하는 멤버 |객체에 속하는 멤버|
|초기화 시점|클래스가 로딩될 때 초기화되며, 프로그램 종료까지 메모리에 유지|객체가 생성될 때 초기화되며, 객체가 삭제될 때까지 메모리에 유지|
|생성 타이밍|클래스를 로딩할 때 생성되며, 클래스의 인스턴스를 생성하지 않고도 사용 가능|클래스의 인스턴스를 생성해야 사용 가능|
|Value|클래스마다 공유되는 값|객체마다 다른 값|
|용도|유틸리티 메서드 등을 표현, 상수 값을 저장하는 데 사용|객체의 상태를 표현, 객체 간의 관계를 나타내는데 사용|
|ex|데이터베이스의 연결 정보나, 프로그램에서 공유해야 할 상수 값|학생 객체를 생성하면서 학생의 이름, 학번, 성적 등의 정보|

- Method

|                 |static Method|non-static Method|
|:---------------:|:---:|:---:|
|      호출 방법      |객체를 생성하지 않고도 클래스 이름으로 직접 호출|객체를 생성한 후, 객체 이름을 사용하여 호출|
|      From       |클래스에 속하는 메서드|객체에 속하는 메서드|
| 객체 상태에 대한 접근 여부 |Static 메서드는 객체 상태에 접근 X|Non-Static 메서드는 객체의 상태에 접근 O|
|    객체 생성 여부     |객체를 생성하지 않고도 호출할 수 있기 때문에, 객체 생성 전에도 호출 O|메서드는 객체를 생성해야만 호출 O|
|       다형성       |다형성X|다형성O|
|       용도        |객체와 관계없는 작업을 수행, 객체를 생성하기 전에 사용해야 하는 작업을 수행할 때 사용|객체의 상태에 따라 동작하는 작업을 수행할 때 사용|

### Java의 main 메소드가 static인 이유? (public static void main(){String args[]})

static 변수를 첫 단계로 [Runtime Data Area](#jvm-runtime-data-area)중 Method Area에 올려서 프로그램을 실행시켜 main() 메소드를 호출하기 때문입니다.

따라서 JVM은 이를 통해 Method Area에 로드된 main()을 실행하게 됩니다.

### 직렬화, 역직렬화

#### 직렬화
- 자바 시스템 내부에서 사용되는 객체 또는 데이터를 외부의 자바 시스템에서도 사용할 수 있도록 바이트 형태로 데이터를 변환하는 기술
- 조건
  - Serializable 인터페이스 상속받은 객체
- ObjectOutputStream 객체를 이용

#### 역직렬화
- 바이트로 변환된 데이터를 다시 객체로 변환하는 기술
- ObjectInputStream 객체를 이용

### 추상 클래스 VS 인터페이스

#### 공통점

- 객체를 생성 할 수 없다.
- 추상 메서드를 포함한다.
- 상속받는 클래스에서는 추상 메서드를 반드시 재정의하여 구현해야 한다.

#### 차이점

|구분|추상 클래스|인터페이스|
|:---:|:---:|:---:|
|개념적 목적|상송 받아서 기능을 확장시키는데 목적|구현 객체의 동일한 실행 기능을 보장하기 위한 목적|
|클래스|클래스다(abstract class)|클래스가 아니다(interface)|
|일반 메서드|일반 메서드 정의가 가능|일반 메서드 정의 불가능(Java8 이후 static, default 메서드 정의 가능)|
|멤버 변수|클래스와 동일하게 변수 선언 및 사용 가능|상수만 사용 가능|
|상속 키워드|extends|implements|
|다중 상속|불가능|가능|

### List, Map, Set 차이

- List
  - 순서와 중복이 허용된다.
  - 배열 크기가 가변적이다.
  - 데이터가 커지면 속도 이슈가 있다.
  - 인덱스로 원소에 접근한다.
- Map
  - Key, Value 한 쌍으로 저장되고 순서가 없다.
  - Key는 중복될 수 없다.
  - Value는 중복이 허용된다.
  - 별도 인덱스가 없다.
  - 검색 속도가 빠르다.
- Set
  - 순서가 없고 중복도 허용되지 않는다.
  - 인덱스를 사용하지 않는다.
  - 검색 속도가 빠르다.

### Optional?

NullPointerException을 피하기 위해 null 여부 검사를 하게 되는데, 검사를 해야 하는 변수가 많을 수록 코드가 번거롭다.
따라서 Optional<T> 클래스를 사용해 NullPointerException 발생을 방지하도록 도와줍니다. 
null이 올 수 있는 값을 감싸는 Wrapper 클래스로, 참조하더라도 NullPointerException이 발생하지 않도록 도와줍니다.

### 동기화(Synchronized)

만일 JavaScript를 다루면서 AJax통신에 대해서 다뤄 보았다면 이해가 더 잘될꺼라 생각된다.

AJax(Asynchronous JavaScript And XML)란 웹의 비동기적 통신을 위한 기술이다.

여기서 비동기적 통신의 의미는 만약, 우리가 웹사이트를 이용할 경우 다양한 기능을 사용할 것이다.
예를 들어, 댓글을 달거나 회원가입 시에 이메일 혹은 문자를 전송할 경우 우리의 웹 페이지는 새로고침이 되지 않는다.

즉, 웹 페이지 전체를 리프레쉬 하지 않고 일부분의 Event만 수행하게 하는 것이 비동기적 통신의 간략한 설명이다.

그렇다면, **Synchronized**란 간단히 말해 하나의 프로세스가 진행될 때 다른 프로세스가 간섭하지 못하게 해당 프로세스만을 진행하고 수행하도록 하는 것이라고 설명할 수 있다.

이 기능은 하나의 프로세스 연산 중에 다른 프로세스들이 동시에 접근하는 것을 허용하지 않을때 주로 사용된다.

### String, StringBuffer, StringBuilder

#### String vs StringBuffer, StringBuilder

String -> 불변성, StringBuffer, StringBuilder 가변성이다. 쉽게 그림 첨부

![String](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbbConT%2FbtrD2HaWZWh%2Fb3w10xYFPDKNItalQpQH91%2Fimg.png)

![SB](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbZzL6Z%2FbtrD7FXgYVV%2FYacgVmDx76kVwqNLjhiEW1%2Fimg.png)

#### StringBuffer VS StringBuilder

StringBuffer는 동기화 키워드를 지원하여 멀티쓰레드 환경에서 안전하다. 반대로 StringBuilder는 멀티쓰레드 환경에서 사용하는 것은 적합하지 않지만 동기화를 고려하지 않는 싱글쓰레드에서의 성능은 StringBuffer보다 뛰어나다.

PS. String도 불변성을 가지기 때문에 마찬가지로 멀티쓰레드 환경에서 안전하다.

### 제네릭

제네릭(Generic)은 클래스 내부에서 타입을 지정하는 것이 아닌, 외부에서 사용자에 의해 지정되는 것을 의미한다.

- 잘못된 타입이 들어오는 것을 컴파일 단계에서 방지할 수 있다.
- 클래스 외부에서 타입을 지정하기 때문에 따로 타입을 체크하고 변환할 필요가 없어 관리하기 용이하다.
- 비슷한 기능을 지원하는 경우 코드의 재사용성이 높아진다.

### 프로세스와 스레드

먼저 프로그램이란 어떤 작업을 하기 위해 실행할 수 있는 파일을 의미합니다.

프로그램이 실행되면 메모리에 올라가며, 프로세스와 스레드에서 작업이 됩니다.

그래서 프로그램 > 프로세스 > 스레드 순으로 실행이 됩니다.

- 프로세스 (Process)
  - 컴퓨터에서 실행되고 있는 프로그램
  - 메모리에서 실행되고 있는 프로그램의 인스턴스
  - 운영 체제로 부터 시스템 자원을 할당 받는 단위
- 스레드 (Thread)
  - 프로세스가 할당 받은 자원을 이용하는 실행 단위
  - 하나의 프로세스에 여러 스레드가 존재하며, 힙을 통해 공유 가능
- 멀티 프로세스 (Multi Process)
  - 하나의 프로그램을 여러 개의 프로세스로 구성하여, 각각의 프로세스가 개별 작업을 빠르게 처리
  - 문제 발생 시에 다른 프로세스에 영향을 주지 않음
  - 프로세스 간에 통신 비용이 크고 복잡함
- 멀티 스레드 (Multi Thread)
  - 하나의 프로그램을 여러 개의 스레드로 구성하여, 각각의 스레드가 개별 작업을 빠르게 처리
  - 자원의 효율성 증대
  - 처리 비용 감소 및 응답 시간 단축
  - 자원을 공유하면서 동기화 문제 발생 가능

### Singleton Pattern


