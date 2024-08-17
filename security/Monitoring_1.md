---
layout: page
title: 1. Monitoring 개요 및 Spring Boot Actuator
description: >
hide_description: true
sitemap: false
---

## 1.1 Monitoring 개요

### 1.1.1 모니터링이란?

- 모니터링은 시스템의 성능, 안정성 및 가용성을 실시간으로 관찰하고 측정하는 과정을 의미합니다.
- 모니터링 도구를 사용하여 시스템의 상태를 추적하고, 문제를 감지하고, 성능을 최적화하며, 장애 발생 시 빠르게 대응할 수 있습니다.

### 1.1.2 모니터링의 중요성

- **문제 예방**: 모니터링을 통해 잠재적인 문제를 사전에 발견하고 해결할 수 있습니다.
- **빠른 대응**: 문제가 발생했을 때 신속하게 대응할 수 있어 시스템 다운타임을 최소화합니다.
- **성능 최적화**: 애플리케이션의 성능 병목 지점을 찾아 개선할 수 있습니다.
- **사용자 경험 향상**: 안정적이고 빠른 서비스를 제공하여 사용자 만족도를 높입니다.
- **비즈니스 연속성**: 시스템 가용성을 보장하여 비즈니스 운영의 연속성을 유지할 수 있습니다.

### **1.1.3 모니터링의 범위**

- **서버 모니터링**: CPU, 메모리, 디스크 사용량, 네트워크 트래픽 등 서버 자원 사용을 모니터링합니다.
- **애플리케이션 모니터링**: 애플리케이션의 상태, 성능, 로그 등을 모니터링합니다.
- **네트워크 모니터링**: 네트워크 트래픽, 대역폭 사용량, 네트워크 지연 등을 모니터링합니다.
- **데이터베이스 모니터링**: 쿼리 성능, 연결 수, 데이터베이스 사용량 등을 모니터링합니다.
- **보안 모니터링**: 보안 이벤트, 침입 시도, 취약점 등을 모니터링합니다.

## 1.2 Spring Boot Actuator

### **1.2.1 Spring Boot Actuator란?**

- Spring Boot Actuator는 Spring Boot 애플리케이션의 상태와 성능을 모니터링하고 관리할 수 있도록 다양한 엔드포인트를 제공하는 기능입니다.
- Actuator는 헬스 체크, 메트릭스, 환경 정보, 로그 정보 등 여러 가지 중요한 정보를 쉽게 확인할 수 있도록 도와줍니다.

### 1.2.2 **Actuator 의존성 추가**

```bash
implementation 'org.springframework.boot:spring-boot-starter-actuator'
implementation 'org.springframework.boot:spring-boot-starter-web'
```

### 1.2.3 **Actuator 엔드포인트**

- Actuator는 기본적으로 여러 엔드포인트를 제공합니다. 이러한 엔드포인트는 /actuator 경로 하위에 위치하며, 각각의 엔드포인트는 다양한 정보를 제공합니다.
    - **/actuator/health**: 애플리케이션의 상태를 확인합니다.
    - **/actuator/metrics**: 애플리케이션의 메트릭 정보를 제공합니다.
    - **/actuator/loggers**: 로깅 설정을 조회하고 변경할 수 있습니다.
    - **/actuator/env**: 환경 변수와 설정 정보를 확인합니다.
    - **/actuator/beans**: 애플리케이션 컨텍스트에 있는 빈 목록을 확인합니다.
    - **/actuator/threaddump**: 스레드 덤프를 확인합니다.
    - **/actuator/httptrace**: 최근 HTTP 요청 및 응답을 추적합니다.

### 1.2.4 **Actuator 엔드포인트 설정**

- 기본적으로 모든 엔드포인트가 활성화되어 있지 않을 수 있습니다. application.properties 파일을 사용하여 필요한 엔드포인트를 활성화하거나 비활성화할 수 있습니다.
- **설정 옵션 설명**
    - never: 헬스 체크 상세 정보를 절대 표시하지 않습니다.
    - always: 모든 사용자에게 헬스 체크 상세 정보를 항상 표시합니다.
    - when_authorized: 인증된 사용자에게만 헬스 체크 상세 정보를 표시합니다.

  **src/main/resources/application.properties**

    ```yaml
    spring.application.name=sample
    
    server.port=8080
    
    #모든 엔드포인트 노출 설정
    management.endpoints.web.exposure.include=* 
    
    #헬스 체크 엔드포인트 상세 정보 표시 설정
    management.endpoint.health.show-details=always # 이 설정은 /actuator/health 엔드포인트에서 헬스 체크 정보를 항상 상세히 보여주도록 설정합니다. 기본적으로, 헬스 체크 엔드포인트는 요약된 상태 정보만 제공하며, 상세 정보는 노출되지 않습니다.
    ```


### 1.2.5 **주의사항 및 권장사항**

- **보안**
    - 모든 엔드포인트를 노출하는 설정은 개발 및 테스트 환경에서는 유용할 수 있지만, 운영 환경에서는 보안 위험을 증가시킬 수 있습니다. 필요한 엔드포인트만 노출하는 것을 권장합니다.
    - 헬스 체크 엔드포인트에서 상세 정보를 항상 노출하는 설정 역시 운영 환경에서는 민감한 정보를 포함할 수 있으므로, show-details설정을 when_authorized 옵션을 사용하는 것이 좋습니다.
- **엔드포인트 보호**
    - actuator 접근 포트만 다른 포트로 설정하여 보호 할 수 있습니다.

        ```bash
        # 애플리케이션의 기본 포트를 8080으로 설정
        server.port=8080
        
        # Actuator 엔드포인트를 19090 포트에서 서비스하도록 설정
        management.server.port=19090
        ```

    - Spring Security를 사용하여 민감한 엔드포인트에 접근 제어를 설정하는 것이 좋습니다.
    - 예를 들어, /actuator 경로에 대한 보안 설정을 추가할 수 있습니다.

        ```java
        import org.springframework.context.annotation.Configuration;
        import org.springframework.security.config.annotation.web.builders.HttpSecurity;
        import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
        import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
        
        @Configuration
        @EnableWebSecurity
        public class SecurityConfig extends WebSecurityConfigurerAdapter {
            @Override
            protected void configure(HttpSecurity http) throws Exception {
                http.authorizeRequests()
                    .antMatchers("/actuator/**").authenticated()
                    .and()
                    .httpBasic();
            }
        }
        ```



### 1.2.6 실습

- 스프링 프로젝트를 만듭니다.
    - [start.spring.io](http://start.spring.io) 에 접속하여 프로젝트를 생성합니다. 아래의 이미지 참고

      ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fa0573311-1aad-440b-a523-90aa0cb41ce2%2FUntitled.png?table=block&id=36fe63bd-5cb9-42b7-bb9b-f3d0e7d31da2&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1300&userId=&cache=v2)

- http://localhost:8080/actuator 에 접근하면 확인 가능한 actuator 리스트를 볼 수 있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F7aeffbd3-33d7-4290-81d2-7d22d854da33%2FUntitled.png?table=block&id=1f20a6b7-4c59-4453-9c24-de5b13e3b64b&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)