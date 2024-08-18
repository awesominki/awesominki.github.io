---
layout: page
title: 1. CORS (Cross-Origin Resource Sharing)
description: >
hide_description: true
sitemap: false
---

### 1.1 CORS란?

- 한 출처(도메인, 프로토콜, 포트)에서 실행 중인 웹 애플리케이션이 다른 출처의 리소스에 접근할 수 있도록 브라우저에서 제공하는 보안 기능입니다.
- 웹 애플리케이션은 기본적으로 동일 출처 정책(Same-Origin Policy)에 따라 동작하며, 이는 보안상의 이유로 다른 출처의 리소스 접근을 제한합니다.
- CORS는 이러한 제한을 완화하여 특정 조건 하에 다른 출처의 리소스 접근을 허용합니다.

### 1.2 **동일 출처 정책(Same-Origin Policy)**

- 동일 출처 정책은 보안 메커니즘으로, 웹 브라우저가 스크립트가 로드된 출처(origin)와 동일한 출처의 리소스만 접근할 수 있도록 제한합니다.
- **출처의 구성 요소**: 출처는 스키마(프로토콜), 호스트(도메인), 포트의 조합으로 정의됩니다.
    - 예: http://example.com:80와 http://example.com:8080은 포트가 다르므로 동일 출처가 아님.

### 1.3 **CORS의 필요성**

- **API 호출**: SPA(Single Page Application)와 같이 클라이언트 중심의 웹 애플리케이션은 종종 다른 도메인에서 호스팅되는 API를 호출해야 합니다.
- **리소스 공유**: 여러 도메인 간의 이미지, 스타일시트, 스크립트, 폰트 등의 리소스를 공유할 필요가 있습니다

### 1.4 **CORS의 동작 원리**

- Preflight 란?
    - Preflight 요청은 CORS (Cross-Origin Resource Sharing) 요청의 일종으로, 브라우저가 실제 요청을 보내기 전에 서버에 요청할 권한이 있는지 확인하는 과정입니다.
    - 이는 보안상의 이유로, 특정 조건을 만족하는 HTTP 요청이 서버에 전송되기 전에 실행됩니다.
        - **HTTP 메서드가 단순 요청이 아닐 때** (GET, HEAD, POST 외의 메서드, 예: PUT, DELETE).
        - **특정 헤더를 사용할 때**: 커스텀 헤더 또는 특정 표준 헤더를 사용할 때.
        - **특정 Content-Type을 사용할 때**: application/x-www-form-urlencoded, multipart/form-data, text/plain이 아닌 Content-Type을 사용할 때.
    - 동작 방식은 다음과 같습니다
        1. **브라우저가 Preflight 요청을 보냄**:
            - OPTIONS 메서드를 사용하여 서버에 사전 요청을 보냅니다.
            - 이 요청에는 실제 요청의 메서드와 헤더 정보가 포함됩니다.
        2. **서버가 Preflight 요청에 응답**:
            - 서버는 요청된 메서드와 헤더를 허용할지 여부를 결정하여 응답합니다.
            - 응답 헤더에는 Access-Control-Allow-Origin, Access-Control-Allow-Methods, Access-Control-Allow-Headers 등이 포함됩니다.
        3. **브라우저가 응답을 확인**:
            - 브라우저는 서버의 응답을 확인하고, 요청이 허용되면 실제 요청을 보냅니다.
            - 요청이 허용되지 않으면, 브라우저는 실제 요청을 차단합니다.

- **Simple Request (단순 요청)**
    - 간단한 HTTP 요청으로, Preflight 요청 없이 바로 서버에 전달됩니다
        - HTTP 메서드가 GET, POST, HEAD 중 하나.
        - 커스텀 헤더가 없고, Content-Type이 application/x-www-form-urlencoded, multipart/form-data, text/plain 중 하나
- **Preflight Request (사전 요청)**
    - 단순 요청 조건을 충족하지 않는 요청은 서버의 CORS 정책을 확인하기 위해 브라우저가 먼저 OPTIONS 메서드를 사용하여 Preflight 요청을 보냅니다.
    - 서버가 Preflight 요청에 적절히 응답하면 실제 요청이 진행됩니다.

### 1.5 **CORS 설정 시 주의사항**

- **보안 고려사항**:
    - 신뢰할 수 없는 출처를 허용하지 않도록 주의.
    - allowedOrigins에 와일드카드(*)를 사용하면 모든 출처에서의 요청을 허용하므로 주의가 필요.
    - 민감한 정보를 보호하기 위해 Access-Control-Allow-Credentials를 신중하게 설정.
- **성능 고려사항**:
    - Preflight 요청이 빈번하면 성능 저하가 발생할 수 있으므로, Access-Control-Max-Age를 설정하여 Preflight 요청을 캐싱.
    - 불필요한 Preflight 요청을 최소화하기 위해 단순 요청 조건을 충족하도록 API 설계를 검토.

### 1.6 CORS 해결하기

- 백앤드 애플리케이션에서 CORS 설정을 해준다.
- 전역설정

    ```java
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.web.servlet.config.annotation.CorsRegistry;
    import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
    
    @Configuration
    public class WebConfig {
    
        @Bean
        public WebMvcConfigurer corsConfigurer() {
            return new WebMvcConfigurer() {
                @Override
                public void addCorsMappings(CorsRegistry registry) {
                    registry.addMapping("/**")
                            .allowedOrigins("http://localhost:3000")  // 허용할 출처
                            .allowedMethods("GET", "POST", "PUT", "DELETE", "OPTIONS")
                            .allowedHeaders("*")
                            .allowCredentials(true)
                            .maxAge(3600);  // Preflight 요청 캐시 시간
                }
            };
        }
    }
    ```

- 컨트롤러 레벨 설정

    ```java
    import org.springframework.web.bind.annotation.CrossOrigin;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    @RestController
    public class MyController {
    
        @CrossOrigin(origins = "http://localhost:3000")
        @GetMapping("/api/data")
        public String getData() {
            return "Data from server";
        }
    }
    ```