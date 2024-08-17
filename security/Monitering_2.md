---
layout: page
title: 2. Prometheus
description: >
hide_description: true
sitemap: false
---

### 2.1 Prometheus 란?

- Prometheus는 오픈소스 시스템 모니터링 및 경고 도구입니다.
- SoundCloud에서 시작되어 현재는 Cloud Native Computing Foundation(CNCF)에서 호스팅하고 있습니다.
- Prometheus는 시계열 데이터베이스를 사용하여 메트릭 데이터를 수집하고, 쿼리 및 시각화를 통해 시스템 상태를 모니터링하고 경고를 설정할 수 있습니다.

### 2.2 **Prometheus의 주요 구성 요소**

- **Prometheus 서버**:
    - 메트릭 데이터를 수집하고 저장하는 핵심 컴포넌트입니다. 각 타겟으로부터 데이터를 주기적으로 스크랩(scrape)하여 시계열 데이터베이스에 저장합니다.
    - 시계열 데이터베이스(Time Series Database, TSDB)는 시간에 따라 변화하는 데이터를 효율적으로 저장하고 조회할 수 있도록 최적화된 데이터베이스입니다.
- **Exporters**:
    - Prometheus는 기본적으로 애플리케이션에서 메트릭 데이터를 수집합니다.
    - Exporter는 특정 애플리케이션이나 시스템의 메트릭 데이터를 Prometheus가 이해할 수 있는 형식으로 변환해주는 도구입니다.
    - 예시: Node Exporter (서버의 시스템 메트릭 수집), PostgreSQL Exporter (PostgreSQL 메트릭 수집), Spring boot의 micrometer-registry-prometheus 디펜던시
- **Pushgateway**:
    - 짧은 수명의 작업(job)에서 메트릭을 수집하여 Prometheus 서버에 푸시(push)할 수 있습니다.
    - 일반적으로 지속적으로 실행되지 않는 작업에서 사용됩니다. 예를 들어 배치 작업, 스크립트 실행, 크론 작업 등이 있습니다.
- **Alertmanager**:
    - Prometheus 서버에서 발생하는 경고(alert)를 처리하고, 이메일, PagerDuty, Slack 등 다양한 방법으로 알림을 보낼 수 있습니다.
- **Grafana**:
    - Prometheus 데이터를 시각화하기 위해 자주 사용되는 대시보드 도구입니다.
    - Grafana를 사용하면 Prometheus에서 수집한 메트릭 데이터를 대시보드 형태로 시각화할 수 있습니다.

### 2.3 실습

- Prometheus 를 보다 쉽게 설치하기 위해서 Docker 를 사용합니다.
- 먼저 스프링 프로젝트 부터 생성하겠습니다. 이전 실습에 Prometheus를 추가합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F66e59ee4-32ca-4807-86f0-e8f7fb49b226%2FUntitled.png?table=block&id=98a20269-59a0-41c9-bea0-bf04e54089c4&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)


- **src/main/resources/application.properties 를 수정합니다.**

    ```bash
    spring.application.name=sample
    
    server.port=8080
    
    #모든 엔드포인트 노출 설정
    management.endpoints.web.exposure.include=* 
    
    #헬스 체크 엔드포인트 상세 정보 표시 설정
    management.endpoint.health.show-details=always # 이 설정은 /actuator/health 엔드포인트에서 헬스 체크 정보를 항상 상세히 보여주도록 설정합니다. 기본적으로, 헬스 체크 엔드포인트는 요약된 상태 정보만 제공하며, 상세 정보는 노출되지 않습니다.
    
    management.endpoint.prometheus.enabled=true
    ```


- http://localhost:8080/actuator/prometheus 에 접속하여 프로메테우스 매트릭스를 확인할 수 있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F95408b5d-b93a-4307-931e-f8d9c5fa983c%2FUntitled.png?table=block&id=36f25b90-187a-4bad-a5c2-af918298699d&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)


- **Prometheus 설정 파일 생성**
    - Prometheus가 모니터링할 타겟과 기타 설정을 정의하는 설정 파일(prometheus.yml)을 생성합니다.
    - `host.docker.internal`은 Docker에서 제공하는 특수한 DNS 이름으로, Docker 컨테이너가 호스트 머신(즉, Docker를 실행하는 컴퓨터)의 네트워크 서비스에 접근할 수 있도록 합니다. 이를 통해 컨테이너 내부에서 호스트 머신의 네트워크 주소를 참조할 수 있습니다.

        ```yaml
        global:
          scrape_interval: 15s
        
        scrape_configs:
          - job_name: 'spring-boot'
            metrics_path: '/actuator/prometheus'
            static_configs:
              - targets: ['host.docker.internal:8080']
        ```


- Prometheus 실행
    - Docker 명령어를 사용해 Prometheus컨테이너를 실행합니다.
    - -v 옵션의 앞부분은 방금전 생성한 prometheus.yml의 경로를 포함하여 작성합니다.

    ```yaml
    docker run -d --name=prometheus -p 9090:9090 -v /path/to/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
    ```

- [localhost:9090](http://localhost:9090) 에 접속해봅니다.프로메테우스 서버에 접속할 수 있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F5e374be9-add1-4a42-8b1a-5a8e0100fe65%2FUntitled.png?table=block&id=c833f99b-e7e1-40fe-ba71-29700244c71b&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)


- 상단 메뉴에서 Status > Targets에 접속하여 스프링 애플리케이션의 매트릭스를 수집하고 있는것을 확인 할 수 있습니다 (나오지 않는다면 스프링 애플리케이션을 Run하고 있는지 확인합니다.)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F4682bdec-7a92-4c72-95d5-c1f47b338941%2FUntitled.png?table=block&id=77832be7-0c44-4adf-8848-a744403efe70&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fbe08104f-c479-4c91-8aca-76dc8ee7373c%2FUntitled.png?table=block&id=f3e6ae3b-ed5f-4ea8-914c-fe4302ecf467&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)