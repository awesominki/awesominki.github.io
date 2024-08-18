---
layout: page
title: 5. 애플리케이션 로그 모니터링
description: >
hide_description: true
sitemap: false
---

### 5.1 Loki

- Loki는 Grafana Labs에서 개발한 로그 집계 시스템으로, Prometheus의 메트릭 수집 방식과 유사하게 로그 데이터를 수집하고 쿼리할 수 있도록 설계되었습니다.
- Loki는 주로 로그 데이터를 저장하고, 이를 Grafana를 통해 시각화하는 데 사용됩니다.
- Loki의 주요 특징 중 하나는 라벨 기반의 메타데이터를 사용하여 로그를 효율적으로 검색할 수 있다는 점입니다.

### 5.2 **loki-logback-appender**

- loki-logback-appender는 Logback을 사용하는 Java 애플리케이션에서 로그를 Loki로 직접 전송하기 위한 라이브러리입니다.
- 이 라이브러리를 사용하면 별도의 Promtail 설정 없이도 로그를 Loki로 전송할 수 있습니다.

### 5.3 스프링 프로젝트 설정

- build.gradle 파일에

    ```groovy
    dependencies {
    	implementation 'com.github.loki4j:loki-logback-appender:1.5.1' //추가
    
    	implementation 'org.springframework.boot:spring-boot-starter-actuator'
    	implementation 'org.springframework.boot:spring-boot-starter-web'
    	runtimeOnly 'io.micrometer:micrometer-registry-prometheus'
    	testImplementation 'org.springframework.boot:spring-boot-starter-test'
    	testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
    }
    ```

- SampleController.java

  루프 페이지에 접속하면 403에러를 발생하며 로그도 발생시킵니다.

    ```java
    import jakarta.servlet.http.HttpServletResponse;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    import java.io.IOException;
    
    @RestController
    public class SampleController {
    
        private static final Logger logger = LoggerFactory.getLogger(SampleController.class);
    
        @GetMapping("/")
        public String hello(HttpServletResponse response) throws IOException {
            logger.info("Attempted access to / endpoint resulted in 403 Forbidden");
            response.sendError(HttpServletResponse.SC_FORBIDDEN, "Access Denied");
            return null;
        }
    }
    ```

- resources/logback.xml 파일을 생성합니다.

    ```groovy
    <configuration>
        <appender name="LOKI" class="com.github.loki4j.logback.Loki4jAppender">
            <http>
                <url>http://localhost:3100/loki/api/v1/push</url>
            </http>
            <format>
                <label>
                    <pattern>app=my-app,host=${HOSTNAME}</pattern>
                </label>
                <message class="com.github.loki4j.logback.JsonLayout" />
            </format>
        </appender>
    
        <root level="DEBUG">
        <appender-ref ref="LOKI" />
        </root>
    </configuration>
    ```


### 5.4 Loki (With Docker)

- 폴더를 만들고 loki-config.yml 파일을 만듭니다.

  https://grafana.com/docs/loki/latest/setup/install/docker/ 문서를 참고 합니다.

    ```groovy
    auth_enabled: false
    
    server:
      http_listen_port: 3100
      grpc_listen_port: 9096
    
    common:
      instance_addr: 127.0.0.1
      path_prefix: /tmp/loki
      storage:
        filesystem:
          chunks_directory: /tmp/loki/chunks
          rules_directory: /tmp/loki/rules
      replication_factor: 1
      ring:
        kvstore:
          store: inmemory
    
    query_range:
      results_cache:
        cache:
          embedded_cache:
            enabled: true
            max_size_mb: 100
    
    schema_config:
      configs:
        - from: 2020-10-24
          store: tsdb
          object_store: filesystem
          schema: v13
          index:
            prefix: index_
            period: 24h
    
    ruler:
      alertmanager_url: http://localhost:9093
    
    # By default, Loki will send anonymous, but uniquely-identifiable usage and configuration
    # analytics to Grafana Labs. These statistics are sent to https://stats.grafana.org/
    #
    # Statistics help us better understand how Loki is used, and they show us performance
    # levels for most users. This helps us prioritize features and documentation.
    # For more information on what's sent, look at
    # https://github.com/grafana/loki/blob/main/pkg/analytics/stats.go
    # Refer to the buildReport method to see what goes into a report.
    #
    # If you would like to disable reporting, uncomment the following lines:
    #analytics:
    #  reporting_enabled: false
    ```

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fb6e6caa4-ed85-4d99-a274-1cabf68b4ed2%2FUntitled.png?table=block&id=f64a5249-a590-4fbe-a1ff-b4b5861c54a4&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F5206cf36-dcc8-4fe7-81ec-fa2627e2192c%2FUntitled.png?table=block&id=0be826bb-581a-4bdf-b25a-0566b86926ae&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- 도커 컨테이너를 실행합니다.

  http://localhost:3100/ready에 접속하면 상태를 확인할 수 있습니다.

    ```bash
    docker run --name loki -d -v ${loki-config.yml 이 저장된 폴더}:/mnt/config -p 3100:3100 grafana/loki:3.0.0 -config.file=/mnt/config/loki-config.yml
    ```

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F25bc200f-ea66-4d51-b6d1-dac6239712a0%2FUntitled.png?table=block&id=9391fc3f-5353-490e-ac24-2d25edb245f3&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)


### 1.6.5 그라파나 설정 및 확인

- 사이드 메뉴의 Data sources 페이지에 접속하여 Add new data source 버틐을 클릭하고 Loki 를 선택합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fe1fde05a-15ce-41e8-903a-232ef1ba6240%2FUntitled.png?table=block&id=5c4c0b1d-5f8b-47eb-8843-6d4b6e3361e0&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

- 이름을 입력하고 커넥션에 Loki의 주소를 입력합니다. Docker를 사용했기 때문에 host.docker.interal을 사용합니다. Save & test를 클릭하여 상태를 확인하고 저장합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Feb9adc76-3f73-4cae-a57b-a957925ec005%2FUntitled.png?table=block&id=48f590c4-7344-4c2f-9a57-4e3eb2f0f7c9&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F577b688a-7613-4b3f-8646-4106453034d7%2FUntitled.png?table=block&id=974cb236-06b2-4f3f-9e07-63074dd8d2e0&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)


- 웹에서 루트 페이지에 접근하면 403 에러페이지가 발생합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F4e5eb700-27ec-455e-9046-a1f9e0526edb%2FUntitled.png?table=block&id=5d51b3a5-2bd0-4ee6-9870-0ed4d5b2ce91&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)


- 그라파나의 사이드 메뉴에서 Explore 페이지에 접근하여 Loki를 선택합니다.

  쿼리을 컨트롤러의 로그를 볼수 있도록 수정합니다.(이미지 확인)

  페이지에 접속하고 Run query를 실행하면 해당 로그가 출력되는것을 확인할 수있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fdf716eaa-ae9a-49ee-b65e-0c7ba0b1203a%2FUntitled.png?table=block&id=57020447-466a-471b-858b-5f40467995cd&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)