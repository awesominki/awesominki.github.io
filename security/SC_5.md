---
layout: page
title: 5. Other Security Problem
description: >
hide_description: true
sitemap: false
---

### **5.1 Open Redirect**

- **Open Redirect**는 공격자가 웹 애플리케이션의 리디렉션 기능을 악용하여 사용자를 악성 사이트로 유도하는 공격입니다.
- 예시
    - 상황
        - 사용자가 자주 이용하는 금융 웹사이트 [http://bank.example.com이](http://bank.example.xn--com-5k0o/) 있습니다. 사용자는 로그인 페이지로 이동하여 계정 정보를 입력합니다. 로그인 후, 사용자가 원래 접근하려던 페이지로 리디렉션되기 위해 URL 파라미터를 통해 리디렉션 URL을 전달받습니다.

          > 로그인 폼의 URL은 다음과 같습니다.
          >
          > - http://bank.example.com/login?redirectUrl=http://bank.example.com/dashboard
        - 이를 통해, 로그인 성공 후 redirectUrl 파라미터에 명시된 URL로 사용자를 리디렉션합니다.
    - 공격 시나리오
        1. **공격자의 준비** : 공격자는 피싱 사이트 [http://malicious.example.com을](http://malicious.example.xn--com-of0o/) 운영합니다. 이 사이트는 실제 금융 웹사이트와 매우 유사하게 보이도록 만들어져 있습니다.
        2. **공격자 링크 제작** : 공격자는 다음과 같은 링크를 만듭니다.

           > http://bank.example.com/login?redirectUrl=http://malicious.example.com
           >
            - 이 링크는 사용자가 bank.example.com에 로그인한 후 malicious.example.com으로 리디렉션되도록 설정되어 있습니다.
        3. **사용자 유도** : 공격자는 이메일, 소셜 미디어, 메시지 앱 등을 통해 이 링크를 사용자에게 보냅니다. 사용자는 이 링크를 클릭하면, 자신이 신뢰하는 bank.example.com 로그인 페이지로 이동합니다.
        4. **사용자 로그인** : 사용자는 평소와 같이 bank.example.com에 로그인합니다. 로그인 성공 후, 사용자는 자동으로 malicious.example.com으로 리디렉션됩니다.
        5. **피싱 사이트** : 사용자는 리디렉션된 malicious.example.com에서 실제 금융 웹사이트와 매우 유사한 페이지를 보게 됩니다. 사용자는 자신의 계정 정보를 다시 입력하라고 요구받습니다. 사용자가 정보를 입력하면, 이 정보는 공격자에게 전송됩니다.

- **방어 기법**
    - **허용된 URL 목록 사용**
        - 리디렉션할 수 있는 URL 목록을 미리 정의하고, 해당 목록에 포함된 URL로만 리디렉션합니다.

            ```java
            import java.util.Arrays;
            import java.util.List;
            import javax.servlet.http.HttpServletRequest;
            import javax.servlet.http.HttpServletResponse;
            
            public class RedirectHandler {
                private static final List<String> ALLOWED_URLS = Arrays.asList(
                        "http://bank.example.com/dashboard",
                        "http://bank.example.com/account",
                        "http://bank.example.com/support"
                );
            
                public void handleRedirect(HttpServletRequest request, HttpServletResponse response) throws IOException {
                    String redirectUrl = request.getParameter("redirectUrl");
                    if (ALLOWED_URLS.contains(redirectUrl)) {
                        response.sendRedirect(redirectUrl);
                    } else {
                        response.sendError(HttpServletResponse.SC_BAD_REQUEST, "Invalid URL");
                    }
                }
            }
            ```

    - **입력 검증**
        - 리디렉션 대상 URL을 철저히 검증하여, 신뢰할 수 있는 URL로만 리디렉션하도록 합니다.

            ```java
            public void handleRedirect(HttpServletRequest request, HttpServletResponse response) throws IOException {
                String redirectUrl = request.getParameter("redirectUrl");
                try {
                    URL url = new URL(redirectUrl);
                    if ("bank.example.com".equals(url.getHost())) {
                        response.sendRedirect(redirectUrl);
                    } else {
                        response.sendError(HttpServletResponse.SC_BAD_REQUEST, "Invalid URL");
                    }
                } catch (MalformedURLException e) {
                    response.sendError(HttpServletResponse.SC_BAD_REQUEST, "Malformed URL");
                }
            }
            ```

    - 상대 경로 사용
        - 리디렉션을 외부 URL이 아닌 애플리케이션 내의 상대 경로로 제한하여 공격을 방지할 수 있습니다.

            ```java
            public void handleRedirect(HttpServletRequest request, HttpServletResponse response) throws IOException {
                String redirectPath = request.getParameter("redirectPath");
                if (redirectPath != null && redirectPath.startsWith("/")) {
                    response.sendRedirect(request.getContextPath() + redirectPath);
                } else {
                    response.sendError(HttpServletResponse.SC_BAD_REQUEST, "Invalid path");
                }
            }
            ```


### **5.2 Directory Traversal**

- **Directory Traversal**은 공격자가 웹 애플리케이션의 파일 시스템에서 허용되지 않은 파일이나 디렉토리에 접근할 수 있도록 하는 공격입니다.
- 이는 주로 애플리케이션이 파일 경로를 사용자 입력을 통해 받아서 처리할 때 발생합니다.
- 예시
    - 상황
        - 파일 다운로드 기능이 있는 웹 애플리케이션이 있습니다. 사용자는 파일명을 URL 파라미터로 전달하여 파일을 다운로드할 수 있습니다. 예를 들어, 다음과 같은 URL을 통해 파일을 다운로드할 수 있습니다

          > http://example.com/download?file=report.pdf
    >
    - 공격 시나리오
        1. **공격자 준비** : 공격자는 애플리케이션이 상대 경로를 제대로 검증하지 않는다는 사실을 발견합니다.공격자는 시스템의 중요한 파일에 접근하기 위해 상대 경로를 이용합니다.
        2. **공격자 링크 제작** : 공격자는 다음과 같은 URL을 만듭니다

           > http://example.com/download?file=../../etc/passwd
        >
        3. **파일 접근** : 애플리케이션은 사용자가 제공한 파일명을 검증하지 않고, 해당 경로의 파일을 열어서 사용자에게 전송합니다.
- **방어 기법**
    - **서버 설정 강화** : 웹 서버 설정을 통해 허용되지 않은 디렉토리 접근을 차단합니다.
    - **경로 검증 :** 파일 경로를 검증하여 허용된 디렉토리 내에서만 파일을 접근하도록 합니다.

        ```java
        import java.io.File;
        import java.io.FileInputStream;
        import java.io.IOException;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
        
        public class SimpleFileDownloadHandler {
            private static final String BASE_DIRECTORY = "/var/www/html/";
        
            public void handleFileDownload(HttpServletRequest request, HttpServletResponse response) throws IOException {
                String fileName = request.getParameter("file");
                File file = new File(BASE_DIRECTORY + fileName);
        
                // 파일이 허용된 디렉토리 내에 있는지 확인
                if (!file.getCanonicalPath().startsWith(new File(BASE_DIRECTORY).getCanonicalPath())) {
                    response.sendError(HttpServletResponse.SC_FORBIDDEN, "Access Denied");
                    return;
                }
        
                // 파일이 존재하고 읽을 수 있는지 확인
                if (!file.exists() || !file.isFile()) {
                    response.sendError(HttpServletResponse.SC_NOT_FOUND, "File not found");
                    return;
                }
        
                // 파일을 읽어서 사용자에게 전송
                try (FileInputStream fis = new FileInputStream(file)) {
                    response.setContentType("application/octet-stream");
                    response.setHeader("Content-Disposition", "attachment; filename=\"" + fileName + "\"");
                    byte[] buffer = new byte[4096];
                    int bytesRead;
                    while ((bytesRead = fis.read(buffer)) != -1) {
                        response.getOutputStream().write(buffer, 0, bytesRead);
                    }
                }
            }
        }
        ```

    - **허용된 파일 목록 사용 :** 허용된 파일 목록을 미리 정의하고, 해당 목록에 포함된 파일만 접근하도록 합니다. 이 방법은 파일 이름을 미리 정의된 안전한 목록에서 확인합니다.

        ```java
        import java.util.Arrays;
        import java.util.List;
        import java.io.File;
        import java.io.FileInputStream;
        import java.io.IOException;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
        
        public class SafeFileDownloadHandler {
            private static final String BASE_DIRECTORY = "/var/www/html/";
            private static final List<String> ALLOWED_FILES = Arrays.asList(
                    "report.pdf",
                    "summary.txt",
                    "data.csv"
            );
        
            public void handleFileDownload(HttpServletRequest request, HttpServletResponse response) throws IOException {
                String fileName = request.getParameter("file");
                if (!ALLOWED_FILES.contains(fileName)) {
                    response.sendError(HttpServletResponse.SC_FORBIDDEN, "Access Denied");
                    return;
                }
        
                File file = new File(BASE_DIRECTORY + fileName);
                if (!file.exists() || !file.isFile()) {
                    response.sendError(HttpServletResponse.SC_NOT_FOUND, "File not found");
                    return;
                }
        
                // 파일을 읽어서 사용자에게 전송
                try (FileInputStream fis = new FileInputStream(file)) {
                    response.setContentType("application/octet-stream");
                    response.setHeader("Content-Disposition", "attachment; filename=\"" + file.getName() + "\"");
                    byte[] buffer = new byte[4096];
                    int bytesRead;
                    while ((bytesRead = fis.read(buffer)) != -1) {
                        response.getOutputStream().write(buffer, 0, bytesRead);
                    }
                }
            }
        }
        ```


### **5.3 Clickjacking**

- **Clickjacking**은 공격자가 웹 페이지의 투명한 프레임을 사용하여 사용자가 클릭하도록 유도하는 공격입니다. 이를 통해 공격자는 사용자가 알지 못하는 사이에 악성 동작을 수행할 수 있습니다.
- 예시
    - 공격 시나리오
        1. **공격자 준비** : 공격자는 사용자가 신뢰하는 사이트의 ‘좋아요’ 버튼을 투명한 iframe으로 공격자의 웹 페이지에 삽입합니다.
        2. **공격자 페이지 제작** : 공격자는 다음과 같은 HTML 페이지를 만듭니다

            ```html
            <!DOCTYPE html>
            <html>
            <head>
                <title>Free Gift!</title>
                <style>
                    iframe {
                        position: absolute;
                        top: 0;
                        left: 0;
                        width: 100%;
                        height: 100%;
                        opacity: 0;
                    }
                </style>
            </head>
            <body>
                <h1>Click here to get a free gift!</h1>
                <iframe src="http://social.example.com/like-button"></iframe>
            </body>
            </html>
            ```

        3. **사용자 유도** : 공격자는 이메일, 소셜 미디어, 메시지 앱 등을 통해 이 링크를 사용자에게 보냅니다. 사용자는 이 링크를 클릭하면, 자신이 ‘Free Gift!’ 버튼을 클릭했다고 생각하지만, 실제로는 ‘좋아요’ 버튼을 클릭하게 됩니다.

- **방어 기법**
    - **X-Frame-Options 헤더 사용**: DENY 또는 SAMEORIGIN 값을 사용하여 웹 페이지가 iframe으로 포함되지 않도록 설정합니다.
        - DENY: 어떠한 사이트에서도 이 페이지를 iframe으로 포함할 수 없도록 합니다.
        - SAMEORIGIN: 동일 출처에서만 이 페이지를 iframe으로 포함할 수 있도록 합니다.

      필터 예시

        ```java
        import org.springframework.boot.web.servlet.FilterRegistrationBean;
        import org.springframework.context.annotation.Bean;
        import org.springframework.context.annotation.Configuration;
        
        @Configuration
        public class FilterConfig {
        
            @Bean
            public FilterRegistrationBean<ClickjackingProtectionFilter> clickjackingFilter() {
                FilterRegistrationBean<ClickjackingProtectionFilter> registrationBean = new FilterRegistrationBean<>();
                registrationBean.setFilter(new ClickjackingProtectionFilter());
                registrationBean.addUrlPatterns("/*");
                return registrationBean;
            }
        }
        ```

      등록 예시

        ```java
        import org.springframework.boot.web.servlet.FilterRegistrationBean;
        import org.springframework.context.annotation.Bean;
        import org.springframework.context.annotation.Configuration;
        
        @Configuration
        public class FilterConfig {
        
            @Bean
            public FilterRegistrationBean<ClickjackingProtectionFilter> clickjackingFilter() {
                FilterRegistrationBean<ClickjackingProtectionFilter> registrationBean = new FilterRegistrationBean<>();
                registrationBean.setFilter(new ClickjackingProtectionFilter());
                registrationBean.addUrlPatterns("/*");
                return registrationBean;
            }
        }
        ```

    - **Content Security Policy (CSP)**: CSP 헤더를 사용하여 iframe의 포함을 제한합니다
        - 이 설정은 동일 출처(self)에서만 이 페이지를 iframe으로 포함할 수 있도록 합니다.

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
                http
                    .headers()
                    .contentSecurityPolicy("frame-ancestors 'self'")
                    .and()
                    .frameOptions().deny(); // 또는 .sameOrigin();
            }
        }
        ```


### **5.4 Sensitive Data Exposure**

- **Sensitive Data Exposure**는 애플리케이션이 민감한 데이터를 충분히 보호하지 않아 공격자가 이를 탈취하는 공격입니다.
- 민감한 데이터가 안전하게 저장되거나 전송되지 않을 때 발생합니다. 예를 들어, 비밀번호가 평문으로 저장되거나, 신용카드 정보가 암호화되지 않은 채로 전송되는 경우가 있습니다.
- 예시
    - 공격 시나리오
        1. **평문 비밀번호 저장** : 애플리케이션이 사용자의 비밀번호를 평문으로 데이터베이스에 저장합니다. 공격자는 데이터베이스를 탈취하여 비밀번호를 쉽게 알아낼 수 있습니다.
        2. **암호화되지 않은 전송** : 애플리케이션이 HTTP를 통해 신용카드 정보를 전송합니다. 공격자는 네트워크 트래픽을 도청하여 신용카드 정보를 탈취할 수 있습니다.
- **방어 기법**
    - **데이터 암호화**: 전송 중인 데이터와 저장된 데이터를 암호화합니다.
    - **강력한 암호 정책**: 강력한 암호 정책을 적용하고, 주기적으로 암호를 변경하도록 권장합니다.
    - **HTTPS 사용**: 민감한 데이터를 전송할 때 HTTPS를 사용하여 데이터를 암호화합니다.
    - **접근 제어**: 민감한 데이터에 대한 접근을 최소한으로 제한합니다.
- **[추가정보] http와 https**
    - 웹에서 데이터를 전송하는 주요 프로토콜입니다
    - **HTTP (HyperText Transfer Protocol)**
        - **보안**: 데이터가 암호화되지 않고 평문으로 전송됩니다. 이는 네트워크를 통해 전송되는 데이터가 도청, 중간자 공격 등에 취약할 수 있음을 의미합니다.
        - **포트**: 기본적으로 80번 포트를 사용합니다.
        - **속도**: HTTPS에 비해 약간 빠를 수 있지만, 보안이 취약합니다.
        - **사용**: 보안이 크게 중요하지 않은 웹사이트에서 주로 사용됩니다.
    - **HTTPS (HyperText Transfer Protocol Secure)**
        - **보안**: SSL/TLS(보안 소켓 계층/전송 계층 보안) 프로토콜을 사용하여 데이터를 암호화합니다. 이는 데이터의 기밀성과 무결성을 보장하며, 사용자의 개인정보와 민감한 데이터를 보호합니다.
        - **포트**: 기본적으로 443번 포트를 사용합니다.
        - **속도**: 암호화/복호화 과정이 필요하기 때문에 약간 느릴 수 있습니다. 그러나 현대의 컴퓨팅 능력과 최적화된 SSL/TLS 구현 덕분에 차이가 거의 느껴지지 않습니다.
        - **사용**: 보안이 중요한 웹사이트(온라인 쇼핑, 뱅킹, 로그인 페이지 등)에서 필수적으로 사용됩니다.
    - **왜 HTTPS를 사용해야 하는가?**
        - **데이터 보호**: HTTPS는 데이터를 암호화하여 사용자의 개인 정보와 민감한 데이터를 보호합니다.
        - **신뢰성**: 사용자와 웹사이트 간의 신뢰를 구축합니다. 브라우저는 HTTPS를 사용하지 않는 사이트에 대해 경고를 표시할 수 있습니다.
        - **SEO 이점**: 검색 엔진(특히 Google)은 HTTPS를 사용하는 웹사이트를 선호합니다. 이는 검색 순위에 긍정적인 영향을 미칠 수 있습니다.
        - **데이터 무결성**: HTTPS는 데이터가 전송 중에 변조되지 않도록 보호합니다.
        - 따라서, 보안이 중요한 모든 웹사이트는 HTTPS를 사용해야 하며, 대부분의 현대 웹사이트는 기본적으로 HTTPS를 사용하고 있습니다.

### **5.5 Insecure Deserialization**

- **Insecure Deserialization**은 공격자가 악의적으로 조작된 객체를 애플리케이션에 전달하여 실행시키는 공격입니다.
- 이를 통해 공격자는 시스템 내에서 임의의 코드를 실행하거나 데이터를 조작할 수 있습니다.
- 애플리케이션이 직렬화된 데이터를 역직렬화할 때, 해당 데이터가 신뢰할 수 있는지 검증하지 않으면 공격자가 악의적으로 조작된 직렬화 데이터를 주입할 수 있습니다.
- 이를 통해 공격자는 애플리케이션에서 임의의 코드를 실행하거나 데이터를 조작할 수 있습니다.
- 예시
    - 상황
        - 전자상거래 사이트 [http://shop.example.com이](http://shop.example.xn--com-5k0o/) 있습니다.
        - 사용자는 이 사이트에서 상품을 구매할 때, 이전 구매 내역을 업로드할 수 있습니다. 이 과정에서 사이트는 업로드된 데이터를 역직렬화하여 객체로 변환합니다.

    - 공격 시나리오
        1. **공격자 준비** : 공격자는 악의적으로 조작된 직렬화 객체를 생성합니다. 이 객체는 역직렬화될 때 악성 코드를 실행하도록 설계되어 있습니다.
        2. **공격자 파일 제작** : 공격자는 다음과 같은 직렬화된 데이터를 만듭니다

            ```java
            import java.io.*;
            
            public class MaliciousObject implements Serializable {
                private static final long serialVersionUID = 1L;
            
                private void readObject(ObjectInputStream ois) throws IOException, ClassNotFoundException {
                    ois.defaultReadObject();
                    // 악의적인 코드 실행
                    Runtime.getRuntime().exec("calc.exe");
                }
            }
            
            // 직렬화
            public class SerializeMaliciousObject {
                public static void main(String[] args) {
                    try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("malicious.obj"))) {
                        oos.writeObject(new MaliciousObject());
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }
            ```

        3. **사용자 유도** : 공격자는 이 파일을 전자상거래 사이트에 업로드하도록 유도합니다. 사이트는 파일을 역직렬화할 때 악성 코드를 실행합니다.
- 방어 기법
    - **데이터 검증**: 역직렬화된 객체가 예상된 타입인지 검증하여, 신뢰할 수 없는 객체를 역직렬화하지 않도록 합니다.
    - **안전한 라이브러리 사용**: Java의 기본 직렬화 대신, 안전한 직렬화 라이브러리를 사용하여 역직렬화 과정에서의 보안을 강화할 수 있습니다. 예를 들어, Jackson 라이브러리를 사용하여 JSON 데이터로 직렬화 및 역직렬화하는 방법을 사용할 수 있습니다.
- **[추가정보] 직렬화와 역직렬화**
    - **직렬화 (Serialization)**
        - **직렬화**는 객체를 저장하거나 전송하기 위해 객체의 상태를 바이트 스트림으로 변환하는 과정입니다.
        - 이를 통해 객체를 파일로 저장하거나 네트워크를 통해 전송할 수 있습니다.
    - **역직렬화 (Deserialization)**
        - **역직렬화**는 바이트 스트림으로 변환된 객체 데이터를 다시 원래의 객체 형태로 복원하는 과정입니다.
        - 이를 통해 저장된 객체를 다시 사용하거나, 네트워크로 전송된 객체 데이터를 수신하여 사용할 수 있습니다.


### **5.6 Insufficient Logging & Monitoring**

- **Insufficient Logging & Monitoring**은 애플리케이션이 적절한 로그를 기록하지 않거나, 이상 행동을 감시하지 않아 공격을 조기에 발견하지 못하는 경우입니다.
- 방어기법
    - **포괄적인 로깅**: 중요한 이벤트와 에러를 포괄적으로 로그로 기록합니다.
    - **실시간 모니터링**: 실시간 모니터링 시스템을 사용하여 이상 행동을 감지합니다.
    - **로그 검토**: 주기적으로 로그를 검토하여 이상 징후를 발견하고 대응합니다.