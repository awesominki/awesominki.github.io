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



[//]: # (* [1. Call by value vs Call by Reference]{:.heading.flip-title})

[//]: # (* [2. String 종류, 접근 제어 지시자]{:.heading.flip-title})

[//]: # (* [3. == 와 equals&#40;&#41; 차이 & Wrapper Class]{:.heading.flip-title})

[//]: # (* [4. 기본형과 참조형, 오버라이딩과 오버로딩]{:.heading.flip-title})

[//]: # (* [5. final, non-static & static]{:.heading.flip-title})

[//]: # (* [6. 추상 클래스]{:.heading.flip-title})

[//]: # (* [7. 인터페이스]{:.heading.flip-title})

[//]: # (* [8. 변수의 종류와 메모리 구조]{:.heading.flip-title})

[//]: # (* [9. Thread]{:.heading.flip-title})

[//]: # (* [10. String]{:.heading.flip-title})

[//]: # (* [11. JVM]{:.heading.flip-title})

[//]: # ()
[//]: # ()
[//]: # ([1. Call by value vs Call by Reference]: 1.md)

[//]: # ([2. String 종류, 접근 제어 지시자]: 2.md)

[//]: # ([3. == 와 equals&#40;&#41; 차이 & Wrapper Class]: 3.md)

[//]: # ([4. 기본형과 참조형, 오버라이딩과 오버로딩]: 4.md)

[//]: # ([5. final, non-static & static]: 5.md)

[//]: # ([6. 추상 클래스]: 6.md)

[//]: # ([7. 인터페이스]: 7.md)

[//]: # ([8. 변수의 종류와 메모리 구조]: 8.md)

[//]: # ([9. Thread]: 9.md)

[//]: # ([10. String]: 10.md)

[//]: # ([11. JVM]: 11.md)