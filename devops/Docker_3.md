---
layout: page
title: 3. Docker Network & Spring Boot 연동
description: >
hide_description: true
sitemap: false
---

## 3.1 개요

- 2개의 스프링 컨테이너를 생성하고 사용자가 service-a 컨테이너의 컨트롤러를 호출하면, service-b 컨테이너를 호출 합니다.
- 최종 사용자에게 노출 되는 메시지는 아래와 같습니다.
  ”service-a: hi  ###### service-b: hello”
- 메시지 “service-b: hello” 는 service-b 컨테이너의 메시지입니다.

## 3.2 애플리케이션 생성

### 3.2.1 프로젝트 생성

- 스프링 프로젝트 2개를 생성해주세요 (A,B 동일)

![스프링](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F8c8269e5-ac81-4e45-8cf8-aec21fe4f26d%2F6018e261-efb3-41f7-a12f-a3a1e340db62.png?table=block&id=8ee9bacd-8c64-4a17-8fea-cefcdc72dada&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1030&userId=&cache=v2)

### 3.2.2 service-a

- AApplication.java

    ```java
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cloud.openfeign.EnableFeignClients;
    
    @EnableFeignClients
    @SpringBootApplication
    public class AApplication {
    
    	public static void main(String[] args) {
    		SpringApplication.run(AApplication.class, args);
    	}
    
    }
    ```

- AController.java

    ```java
    import lombok.RequiredArgsConstructor;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    @RestController
    @RequiredArgsConstructor
    public class AController {
    
        private final BServiceClient bServiceClient;
    
        @GetMapping("/hi")
        public String hello() {
            String hello = bServiceClient.getHello();
            return "sevice-a: hi ###### service-b: " + hello;
        }
    }
    ```

- BServiceClient.java

    ```java
    import org.springframework.cloud.openfeign.FeignClient;
    import org.springframework.web.bind.annotation.GetMapping;
    
    @FeignClient(name = "service-b", url = "${service.b.url}")
    public interface BServiceClient {
        @GetMapping("/hello")
         public String getHello();
    }
    ```

- application.properties

    ```bash
    spring.application.name=service-a
    
    server.port=18080
    
    service.b.url=http://localhost:18081
    ```


### 3.2.3 service-b

- BController.java

    ```java
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    @RestController
    public class BController {
    
        @GetMapping("/hello")
        public String hello() {
            return "hello";
        }
    }
    ```

- application.properties

    ```bash
    spring.application.name=service-b
    
    server.port=18081
    ```


### 3.2.3 RUN

- 두 애플리케이션을 실행하고 “http://localhost:18080/hi”로 접속하여 메시지를 확인합니다.

  ![두](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F86956a9c-9d9a-4020-8376-2691640863af%2FUntitled.png?table=block&id=9a3053bc-98fa-4111-83bb-a81634673100&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=770&userId=&cache=v2)


## 3.3 도커 사용

### 3.3.1 [application.properties](http://application.properties) 수정

>📌 포트를 모두 아래와 같이 8080 으로 변경합니다.

>Why?!
>도커의 외부 포르를 조절 하여 애플리케이션을 실행할 것이기 떄문입니다.


- service-a > application.properties

    ```java
    spring.application.name=service-a
    
    server.port=8080
    
    service.b.url=http://service-b:8080
    ```

- service-b > application.properties

    ```java
    spring.application.name=service-b
    
    server.port=8080
    ```


### 3.3.2 Dockerfile 생성


>📌 포트를 모두 아래와 같이 8080 으로 변경합니다.

>Why?!
>도커의 외부 포트를 조절 하여 애플리케이션을 실행할 것이기 때문입니다.


- service-a > Dockerfile

    ```docker
    FROM openjdk:17-jdk-slim
    
    VOLUME /tmp
    
    ARG JAR_FILE=build/libs/*.jar
    
    COPY ${JAR_FILE} app.jar
    
    ENTRYPOINT ["java","-jar","/app.jar"]
    ```

- service-b > Dockerfile

    ```docker
    FROM openjdk:17-jdk-slim
    
    VOLUME /tmp
    
    ARG JAR_FILE=build/libs/*.jar
    
    COPY ${JAR_FILE} app.jar
    
    ENTRYPOINT ["java","-jar","/app.jar"]
    ```


### 3.3.2 도커 네트워크 생성

- 도커끼리 컨테이너 이름으로 호출하기 위해선 기본 브리지 네트워크가 아닌 사용자 정의 네트워크에서 진행해야 합니다.

    ```docker
    docker network create my-network
    ```


### 3.3.2 Service-b 실행하기

>📌 service-b의 프로젝트 루트 폴더에서 진행합니다(Dockerfile의 위치)


- 프로젝트 빌드

    ```docker
    ./gradlew clean bootJar  
    ```

- 이미지 생성

  -이미지 이름을 img-service-b 로 만듭니다. (마지막의 . 은 도커파일의 위치)

    ```docker
     docker build -t img-service-b .
    ```


- 컨테이너 생성
    - 컨테이너의 이름을 service-b, 외부 포트는 18081 로 지정합니다. 내부 포트가 8080 임을 확인합니다.

    ```docker
    docker run -d --name service-b \
     --network my-network \
     -p 18081:8080 \
     img-service-b
    ```

- 컨테이너 확인
    - 컨테이너 리스트 명령어로 컨테이너가 실행되어있는지 확인합니다.(확인하는 습관을 만드세요~!)

    ```docker
    docker ps                                                   
    CONTAINER ID   IMAGE                                 COMMAND                  CREATED         STATUS                PORTS                                                                                                                                  NAMES
    ffdea753159b   img-service-b                         "java -jar /app.jar …"   6 seconds ago   Up 2 seconds          0.0.0.0:18081->8080/tcp, :::18081->8080/tcp    
    ```

- http://localhost:18081/hello 로 접속하여 service-b가 호출 되는지 확인합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fbe6306e1-2f7c-4276-aa94-f6655edb7df3%2FUntitled.png?table=block&id=94044530-32f4-48fd-9abf-4b8e40d55ceb&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=860&userId=&cache=v2)


### 3.3.3 Service-a 실행하기

>📌 service-a의 프로젝트 루트 폴더에서 진행합니다(Dockerfile의 위치)


- 프로젝트 빌드

    ```docker
    ./gradlew clean bootJar  
    ```

- 이미지 생성

  -이미지 이름을 img-service-a 로 만듭니다. (마지막의 . 은 도커파일의 위치)

    ```docker
     docker build -t img-service-a .
    ```

- 컨테이너 생성
    - 컨테이너의 이름을 service-a, 외부 포트는 18080 로 지정합니다. 내부 포트가 8080 임을 확인합니다.
    - service-b를 호출하는 url을 확인합니다. 컨테이너의 이름 및 내부포트로 호출하는것을 볼 수 있습니다.

    ```docker
    docker run -d --name service-a \
     --network my-network \
     -p 18080:8080 \
     -e SERVICE_B_URL=http://service-b:8080 \
    img-service-a
    ```

- 컨테이너 확인
    - 컨테이너 리스트 명령어로 컨테이너가 실행되어있는지 확인합니다.(확인하는 습관을 만드세요~!)

    ```docker
    (base) ➜  com.service.a docker ps                                                   
    CONTAINER ID   IMAGE                                 COMMAND                  CREATED          STATUS                PORTS                                                                                                                                  NAMES
    9fea304cd49e   img-service-a                         "java -jar /app.jar"     5 seconds ago    Up 2 seconds          0.0.0.0:18080->8080/tcp, :::18080->8080/tcp     
    ```

- http://localhost:18081/hello 로 접속하여  service-a가 service-b를 호출 하는지 확인합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fc11481dc-f706-4d5a-a650-83e09773b526%2FUntitled.png?table=block&id=f7fb922f-75ad-43d9-9603-6132c6e868fc&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=960&userId=&cache=v2)


## 3.4 도커 컴포즈 사용

>📌 도커를 사용하면 컨테이너 두개를 띄우는것도 매우 헷갈립니다. 컴포즈를 사용하면 이러한 상황을 피할 수 있습니다.


### 3.4.1 기존 도커  컨테이너를 모두 삭제 합니다.(이미지가 아닙니다.)

```docker
docker rm -f 서비스_A_컨테이너_아이디
docker rm -f 서비스_B_컨테이너_아이디
```

### 3.4.2 Docker compose 파일 생성

- service-a 프로젝트와 service-b 프로젝트의 상위 폴더에서 docker-compose.yml 파일을 생성하여 아래와 같이 작성합니다

```docker
version: '3.8'

services:
  service-a:
    image: img-service-a
    ports:
      - "18080:8080"
    environment:
      - SERVICE_B_URL=http://service-b:8080
    depends_on:
      - service-b

  service-b:
    image: img-service-b
    ports:
      - "18081:8080"

networks:
  default:
    driver: bridge
```

### 3.4.3 실행

- 도커 컴포즈를 실행하고 http://localhost:18080/hi 에 접속하여 확인합니다.

    ```docker
    docker compose up -d
    ```

  ![스크린샷 2024-07-07 오전 12.04.27.png](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fc11481dc-f706-4d5a-a650-83e09773b526%2FUntitled.png?table=block&id=f7fb922f-75ad-43d9-9603-6132c6e868fc&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=960&userId=&cache=v2)


### 3.4.4 네트워크는 어떻게 된걸까??

<aside>
📌 Docker Compose를 사용하여 서비스를 실행하면, Docker Compose는 기본적으로 새 브리지 네트워크를 생성하여 각 서비스 컨테이너를 그 네트워크에 연결합니다. 이 네트워크는 docker-compose.yml 파일에 정의된 모든 서비스가 서로 통신할 수 있도록 합니다.

</aside>

- **네트워크 생성 확인**

  Docker Compose는 자동으로 네트워크를 생성하며, 이 네트워크는 docker-compose.yml 파일이 있는 디렉토리의 이름을 기반으로 합니다. 네트워크의 이름은 다음과 같은 형식으로 생성됩니다

  **“directoryname_default”**

  `docker network ls` 를 사용하여 네트워크가 생성되었는지 확인해봅니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fdf266149-3746-403b-bd8a-762278dde36f%2FUntitled.png?table=block&id=d0b81de9-9c16-44dd-bf36-b64dc42c8867&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1150&userId=&cache=v2)