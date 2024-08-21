---
layout: page
title: Spring note
hide_description: true
sitemap: false
permalink: /spring/ 
---

### IoC란?

객체의 생성부터 생명주기의 관리까지 모든 객체에 대한 제어권이 바뀐 것을 의미합니다.

정확히 설명하면, 디자인 원칙 중 하나로 클래스간의 결합도를 줄이기 위해 다양한 종류의 제어를 반전시킬 것을 권장하는 것을 의미합니다.

### DI란?

IoC가 디자인 원칙이라면, DI는 IoC를 구현할 때 자주 등장하는 '디자인 패턴' 중 하나입니다. DI는 의존 관계 주입으로, 의존 객체를 직접 생성하는 것이 아니라, 생성자나 setter를 통해 넣어줌으로써 의존 관계를 주입하는 것을 의미합니다.

### Spring Bean과 IoC,DI

Spring IoC 컨테이너가 관리하는 자바 객체로 어노테이션, XML 등으로 설정할 수 있다.

특정 클래스의 동일 인스턴스가 생길 때마다 "동일한" 메모리의 인스턴스 사용의 특징을 가진 스프링의 Bean을 사용한다면, 
동일한 역할을 수행하는 객체를 여러 번 만들지 않아도 된다! 딱 1번 만들어두면, 동일한 역할의 인스턴스일 경우 앞서 만들어둔 Spring 빈만 계속 재활용한다!

#### 왜 Spring은 '프레임워크'인가?

'프레임워크'와 '라이브러리'의 차이를 설명하는 핵심 키워드는 IoC이다.

라이브러리는 동작하는 도중 필요한 기능이 있을 능동적으로 라이브러리를 사용할 뿐이다. 

반면에 프레임워크는 거꾸로 애플리케이션 코드가 프레임워크에 의해 사용된다. 보통 프레임워크 위에 개발한 클래스를 등록해두고, 
프레임워크가 흐름을 주도하는 중에 개발자가 만든 애플리케이션 코드를 사용하도록 만드는 IoC방식이다.

스프링은 이러한 IoC 원칙과 DI 구현을 대신 해주는 IoC 컨테이너를 제공하는 프레임워크이다.

### Spring Filter, Interceptor

- 필터 (Filter)
  - Dispatcher Servlet에 요청이 전달되기 전후로 작업을 진행한다.
  - 보통 web.xml에 등록해서 인코딩, 보안 등 공통 처리를 담당한다.
  - ![filter](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbZQx9K%2Fbtq9zEBsJ75%2FdEAKj1HEymcKyZGZNOiA80%2Fimg.png)
- 인터셉터 (Interceptor)
  - Dispather Servlet이 컨트롤러를 호출하기 전후로 작업을 진행한다.
  - 로그인, 권한, 실행 시간, 로그 등 스프링 공통 기능들을 구현한다.
  - ![interceptor](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FSz6DV%2Fbtq9zjRpUGv%2F68Fw4fZtDwaNCZiCFx57oK%2Fimg.png)

### Spring AOP와 @Transactional의 동작 원리

#### Spring AOP

AOP란 Aspect Oriented Programming의 약자로 관점 지향 프로그래밍이라고 한다. 여기서 Aspect(관점)이란 흩어진 관심사들을 하나로 모듈화 한 것을 의미한다.

![AOP](https://velog.velcdn.com/images/ann0905/post/9c2fa7d8-9c97-45e0-8b0a-dd8b57ccbee4/image.png)

AOP 없이 흩어진 관심사를 처리하면 다음과 같은 문제가 발생한다.

- 여러 곳에서 반복적인 코드를 작성해야 한다.
- 코드가 변경될 경우 여러 곳에 가서 수정이 필요하다.
- 주요 비즈니스 로직과 부가 기능이 한 곳에 섞여 가독성이 떨어진다.

Spring AOP는 기본적으로 프록시 방식으로 동작한다. 프록시 패턴이란 어떤 객체를 사용하고자 할 때, 객체를 직접적으로 참조 하는 것이 아니라, 해당 객체를 대행(대리, proxy)하는 객체를 통해 대상객체에 접근하는 방식을 말한다.

#### Proxy 형태로 동작하는 @Transactional

트랜잭션 처리를 위한 @Transactional 애노테이션은 Spring AOP의 대표적인 예이다.

![Transactional](https://velog.velcdn.com/images/ann0905/post/56a48b12-b2d0-4071-b09e-959e585551bb/image.png)

1. target에 대한 호출이 들어오면 AOP proxy가 이를 가로채서(intercept) 가져온다.
2. AOP proxy에서 Transaction Advisor가 commit 또는 rollback 등의 트랜잭션 처리를 한다.
3. 트랜잭션 처리 외에 다른 부가 기능이 있을 경우 해당 Custom Advisor에서 그 처리를 한다.
4. 각 Advisor에서 부가 기능 처리를 마치면 Target Method를 수행한다.
5. interceptor chain을 따라 caller에게 결과를 다시 전달한다.

### 웹 서버 VS WAS

|구분|웹 서버|                    WAS                     |
|:---:|:---:|:------------------------------------------:|
|정의|정적인 콘텐츠(HTML, CSS, 이미지 등)를 제공하는 서버|      동적인 콘텐츠(웹 애플리케이션)를 처리하고 제공하는 서버       |
|기능|HTTP 프로토콜을 이용해 클라이언트에게 웹 페이지 제공| 웹 애플리케이션 실행 및 데이터 처리, 웹 서버와 클라이언트 간의 중계 역할 |
|주요 소프트웨어|Apache, Nginx, IIS|     Tomcat, JBoss, WebLogic, WebSphere     |