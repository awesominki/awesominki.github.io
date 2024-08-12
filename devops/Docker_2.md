---
layout: page
title: 2. Docker Compose
description: >
hide_description: true
sitemap: false
---

### 2.1 Docker Compose 개요

### 2.1.1 Docker Compose란?

- **정의**:
    - Docker Compose는 다중 컨테이너 Docker 애플리케이션을 정의하고 실행하기 위한 도구입니다.
    - `docker-compose.yml` 파일 하나로 애플리케이션의 서비스, 네트워크, 볼륨 등을 정의할 수 있습니다.

### 2.2 Docker Compose 설치

Docker 20.10부터는 Docker Compose가 기본적으로 설치됩니다. 따라서 별도로 설치할 필요가 없습니다.

이전 버전은 중간에 “-” 가 필요하지만 기본 설치 부터는 “ ”으로 대체 되었습니다.

```bash
docker-compose
docker compose
```

### 2.3 Docker Compose 파일 구조

- `docker-compose.yml`:
    - Docker Compose 파일은 YAML 형식으로 작성되며, 애플리케이션의 서비스, 네트워크, 볼륨 등을 정의합니다.

    ```yaml
    
    version: '3'
    services:
      web:
        image: nginx
        ports:
          - "8080:80"
      app:
        build: .
        ports:
          - "8081:8080"
        depends_on:
          - db
      db:
        image: postgres
        environment:
          POSTGRES_PASSWORD: example
    
    ```

    - `version`: Docker Compose 파일의 버전을 지정합니다.
    - `services`: 애플리케이션의 각 서비스를 정의합니다.
    - `web`, `app`, `db`: 각각의 서비스 이름입니다.
    - `image`: 서비스를 실행할 Docker 이미지를 지정합니다.
    - `build`: Dockerfile이 있는 디렉토리 경로를 지정하여 이미지를 빌드합니다.
    - `ports`: 호스트와 컨테이너 간의 포트를 매핑합니다.
    - `depends_on`: 다른 서비스가 먼저 실행되어야 하는 순서를 지정합니다.
    - `environment`: 컨테이너의 환경 변수를 설정합니다.

### 2.4 Docker Compose 사용법

### 2.4.1 docker-compose.yml 파일 작성

- 예제 `docker-compose.yml` 파일:

    ```yaml
    version: '3'
    services:
      web:
        image: nginx
        ports:
          - "8080:80"
      app:
        build: .
        ports:
          - "8081:8080"
        depends_on:
          - db
      db:
        image: postgres
        environment:
          POSTGRES_PASSWORD: example
    
    ```


### 2.4.2 Docker Compose 명령어

- `docker compose up`:
    - `docker-compose.yml` 파일에 정의된 서비스를 빌드하고 시작합니다. 만약 이미 빌드된 이미지가 있다면 이를 사용합니다.
    - 백그라운드에서 실행하려면 `d` 옵션을 추가합니다.

        ```bash
        docker compose up -d
        docker compose -f /path/to/your/project/docker-compose.yml up
        ```

- `docker compose down`:
    - 실행 중인 모든 서비스를 중지하고 컨테이너, 네트워크, 볼륨 등을 정리합니다.

        ```bash
        docker compose down
        ```

- `docker compose build`:
    - `docker-compose.yml` 파일에 정의된 서비스를 빌드합니다.

        ```bash
        docker compose build
        ```

- `docker compose ps`:
    - 현재 실행 중인 서비스를 확인합니다.

        ```bash
        docker compose ps
        ```

- `docker compose logs`:
    - 실행 중인 서비스의 로그를 확인합니다.

        ```bash
        docker compose logs
        ```


### 2.4.3 실습 예제

1. **docker-compose.yml 파일 작성**

    ```yaml
    version: '3'
    services:
      web:
        image: nginx
        ports:
          - "8080:80"
      app:
        build: .
        ports:
          - "8081:8080"
        depends_on:
          - db
      db:
        image: postgres
        environment:
          POSTGRES_PASSWORD: example
    
    ```

2. **Dockerfile 작성**
    - `app` 서비스의 이미지를 빌드하기 위한 Dockerfile을 작성합니다.

    ```
    FROM openjdk:11-jre-slim
    COPY target/myapp.jar /app/myapp.jar
    WORKDIR /app
    ENTRYPOINT ["java", "-jar", "myapp.jar"]
    ```

3. **Docker Compose로 서비스 시작**
    - `docker-compose.yml` 파일이 있는 디렉토리에서 다음 명령어를 실행하여 서비스를 시작합니다.

    ```bash
    docker compose up -d
    ```

4. **서비스 상태 확인**
    - 실행 중인 컨테이너의 상태를 확인합니다.

    ```bash
    docker compose ps
    ```

5. **서비스 로그 확인**
    - 실행 중인 서비스의 로그를 확인합니다.

    ```bash
    docker compose logs
    ```

6. **서비스 중지 및 정리**
    - 모든 서비스를 중지하고 컨테이너, 네트워크, 볼륨 등을 정리합니다.

    ```bash
    docker compose down
    ```


이러한 방식으로 Docker Compose를 사용하면 여러 컨테이너로 구성된 애플리케이션을 쉽게 관리하고 배포할 수 있습니다. `docker-compose.yml` 파일 하나로 모든 서비스의 설정을 관리할 수 있어 일관된 환경을 제공할 수 있습니다.

참고: Docker Compose는 Docker 버전 20.10부터 기본적으로 포함되어 있으므로 별도의 설치가 필요하지 않습니다.