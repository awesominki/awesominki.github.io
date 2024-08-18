---
layout: page
title: 2. CSRF (Cross-Site Request Forgery)
description: >
hide_description: true
sitemap: false
---

### 2.1 CSRF란?

- 웹 애플리케이션의 취약점을 이용해 사용자가 의도하지 않은 요청을 보내도록 하는 공격 기법입니다.
- 공격자는 사용자가 인증된 상태를 악용하여 사용자가 원하지 않는 행동을 수행하게 만듭니다
- 예를 들어, 사용자가 로그인된 상태에서 악의적인 웹사이트를 방문하면, 그 웹사이트가 사용자의 권한을 이용해 은행 계좌에서 돈을 송금하도록 할 수 있습니다.

### 2.2 CSRF 상황

1. **사용자가 로그인**: 사용자가 웹 애플리케이션에 로그인합니다.
2. **세션 유지**: 로그인 후 세션 쿠키가 브라우저에 저장됩니다.
3. **악성 웹사이트 방문**: 사용자가 다른 웹사이트를 방문합니다. 이 웹사이트는 CSRF 공격 코드를 포함하고 있습니다.
4. **악의적인 요청 전송**: 악성 웹사이트는 사용자의 세션 쿠키를 이용해 원본 웹 애플리케이션으로 요청을 보냅니다.
5. **서버 처리**: 서버는 요청을 정상적인 사용자의 요청으로 인식하고 처리합니다.

### 2.3 공격예시

- 공격자 웹페이지 코드

    ```java
    <!DOCTYPE html>
    <html>
    <body>
      <h1>Free Gift</h1>
      <img src="http://bank.com/transfer?amount=1000&to=attacker" style="display:none;" />
    </body>
    </html>
    ```

    - 이 예시에서 사용자가 이 페이지를 방문하면, 이미지 태그를 통해 http://bank.com/transfer?amount=1000&to=attacker 요청이 자동으로 실행됩니다. 사용자가 이미 bank.com에 로그인되어 있다면, 이 요청은 인증된 상태로 처리됩니다.

### 2.4 방지방법

- **Referer 헤더 검증**
    - 서버는 요청의 Referer 헤더를 확인하여 요청이 신뢰할 수 있는 출처에서 온 것인지 확인할 수 있습니다. 그러나, Referer 헤더는 사용자가 조작할 수 있고, 일부 브라우저에서는 이 헤더를 포함하지 않을 수 있습니다.
- **CSRF 토큰 사용**
    - 가장 일반적인 방지 방법은 CSRF 토큰을 사용하는 것입니다. 서버는 각 요청에 대해 고유한 토큰을 생성하고, 이를 폼에 포함시킵니다. 서버는 요청이 들어올 때 이 토큰을 검증합니다.
- form 대신 API사용
    - API를 통해 JSON데이터로 통신한다면 해당 이슈를 피할 수 있다

### 2.5 CSRF 토큰 실습

- [start.spring.io](http://start.spring.io) 에 접속하여 프로젝트를 생성합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F9f469a9d-099a-4f89-bfa6-4abe3599f68b%2FUntitled.png?table=block&id=dee7fc50-5f4a-4a28-9db3-1dce2983117e&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

- SampleContorller.java

    ```java
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.bind.annotation.RequestParam;
    
    @Controller
    public class SampleContorller {
    
        @GetMapping("/")
        public String showForm() {
            return "form";
        }
    
        @PostMapping("/submit")
        public String  handleFormSubmit(@RequestParam("name") String name, @RequestParam("_csrf") String csrfToken) {
            // CSRF 토큰 로그 출력
            System.out.println("Received CSRF token: " + csrfToken);
            System.out.println("Received name: " + name);
            return "result";
        }
    }
    
    ```

- SampleSecurityConfig.java

    ```java
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.security.config.annotation.web.builders.HttpSecurity;
    import org.springframework.security.web.SecurityFilterChain;
    import org.springframework.security.web.csrf.CookieCsrfTokenRepository;
    
    @Configuration
    public class SampleSecurityConfig {
    
        @Bean
        public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
            http
                    .authorizeHttpRequests(authorize -> authorize
                            .anyRequest().permitAll()
                    )
                    .csrf(csrf -> csrf
                            .csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())
                    );
    
            return http.build();
        }
    }
    
    ```

- resources/templates/form.html

    ```html
    <!DOCTYPE html>
    <html xmlns:th="http://www.thymeleaf.org">
    <head>
        <title>CSRF Example</title>
    </head>
    <body>
    <form th:action="@{/submit}" method="post">
        <label for="name">Name:</label>
        <input type="text" id="name" name="name"/>
        <button type="submit">Submit</button>
    </form>
    </body>
    </html>
    ```

- resources/templates/result.html

    ```html
    <!DOCTYPE html>
    <html xmlns:th="http://www.thymeleaf.org">
    <head>
        <title>Result</title>
    </head>
    <body>
    <h1>Form submitted successfully!</h1>
    <a th:href="@{/}">Go back to form</a>
    </body>
    </html>
    ```

- 애플리케이션을 런시킨 후,  [http://localhost:8080](http://localhost:8080/)에 접속합니다. 크롬 검사 탭을 클릭하여 Elements 탭을 확인합니다. 폼 안에 _csrf가 있는것을 확인할 수 있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F98e9c2c6-aa16-421d-9b7a-32339a091dd0%2FUntitled.png?table=block&id=e247dc89-f3c5-4428-b851-2807137ef7a5&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- 폼의 값을 입력하고 제출하면 애플리케이션 로그에 CSRF 값이 노출 되는것을 볼 수 있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F62e75807-e9b2-4431-87be-c2aa1aa44524%2FUntitled.png?table=block&id=b52dd2cc-855b-472f-8479-06ea0dd9d2b0&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

- 만약 폼dml _csrf 값을 임의로 수정 한 후 제출하면, 403 페이지로 이동하는 것을 볼 수 있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F37932a55-c4ff-46cb-8639-582fb0576d81%2FUntitled.png?table=block&id=ce2d3be4-04a0-4456-a4a3-66490e285f78&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1910&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F9f212afd-2728-4e69-9d13-dbe0486d3c42%2FUntitled.png?table=block&id=a0e9a2ac-bc51-41ac-9a0f-f9ccfa0cdec7&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)