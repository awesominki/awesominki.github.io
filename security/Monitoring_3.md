---
layout: page
title: 3. Grafana
description: >
hide_description: true
sitemap: false
---

### 3.1 그라파나란?

- Grafana는 오픈소스 데이터 시각화 및 모니터링 도구입니다.
- 다양한 데이터 소스를 지원하여 데이터를 시각화하고 분석할 수 있도록 돕습니다.
- Grafana는 대시보드를 생성하고, 데이터를 그래프나 차트 형태로 표현하며, 알림 기능을 제공하여 모니터링을 강화할 수 있습니다.

### 3.2 그라파나 주요기능

- **대시보드 생성**:
    - Grafana는 사용자가 데이터를 시각화할 수 있는 대시보드를 생성할 수 있도록 합니다. 여러 가지 그래프, 차트, 게이지 등을 사용하여 데이터를 시각적으로 표현할 수 있습니다.
- **다양한 데이터 소스 지원**:
    - Prometheus, InfluxDB, Graphite, Elasticsearch, MySQL, PostgreSQL 등 다양한 데이터 소스를 지원합니다. 이를 통해 여러 시스템과 애플리케이션의 데이터를 통합하여 시각화할 수 있습니다.
- **알림 기능**:
    - 조건을 설정하여 조건이 충족되면 이메일, Slack, PagerDuty 등 다양한 채널을 통해 알림을 보낼 수 있습니다. 이를 통해 시스템 상태를 실시간으로 모니터링하고 문제가 발생했을 때 즉시 대응할 수 있습니다.
- **플러그인 지원**:
    - Grafana는 플러그인 아키텍처를 지원하여, 다양한 플러그인을 통해 기능을 확장할 수 있습니다. 예를 들어, 새로운 데이터 소스나 시각화 유형을 추가할 수 있습니다.
- **사용자 관리**:
    - 사용자를 관리하고, 대시보드와 데이터 소스에 대한 접근 권한을 설정할 수 있습니다. 이를 통해 팀 내에서 협업을 강화하고 데이터 보안을 유지할 수 있습니다.

### 3.3 그라파나 설치하기

- Docker 를 사용하여 그라파나 컨테이너를 실행합니다.

    ```yaml
    docker run -d --name=grafana -p 3000:3000 grafana/grafana
    ```

- [localhost:3000](http://localhost:3000)에 접속하여 로그인을 진행합니다. 기본계정의 아이디와 비밀번호는 admin/admin 입니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F6b444e17-eb8d-4d32-bcca-3db9ac606dcc%2FUntitled.png?table=block&id=756bf2a4-16b7-461f-86a7-b9385817fc89&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

- 대시보드에서 DATA SOURCE 를 클릭한 후, Prometheus를 선택합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F2089498f-d664-439c-bd20-bfd0b1194de7%2FUntitled.png?table=block&id=6e6ddc4f-16be-4e02-a982-470fe0e908c8&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F566c6d09-1ab8-472f-b0dc-b24de9f74033%2FUntitled.png?table=block&id=8f50b0ca-aaac-4878-808c-ad5b89526a95&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- Name을 입력한후, Contact에 Prometheus Server URL 을 입력합니다. 여기서는 host.docker.internal을 사용했습니다(스프링 애플리케이션이 호스트 PC에서 실행되어 접근하기 위함)

  그후, Save & test를 클릭하여 저장합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F822b0e30-4034-4be5-91ac-e7e79539072e%2FUntitled.png?table=block&id=e66f1925-a371-45d6-90a4-67e09862b2f5&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F6a0d6779-b4f4-4788-beeb-2cf546066584%2FUntitled.png?table=block&id=7c2cc2b5-7479-4b91-b5d2-f30cef01c085&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

- 대시보드 메뉴에 접속하여 Create Dashboard 버튼을 클릭합니다. 그후 import dashboard 를 클릭합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fb1702705-4b0b-4232-bbb6-cb03f819e4c4%2FUntitled.png?table=block&id=7c1f239e-00ea-4de5-9738-57f0dad32943&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F85cde8af-ec01-42c0-91ef-53a9bad5d818%2FUntitled.png?table=block&id=f132c68d-cc7b-47e9-9a5d-497a07ffb953&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- 임포트 창에서 19004을 입력하고 Load 버튼을 클릭합니다.19004는 그라파나에서 제공하는 spring boot 3용 대시보드입니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F9ab7d881-011e-4cb8-a6e7-7e43591956a7%2FUntitled.png?table=block&id=14983030-3c4b-4da8-830a-5e12a1e11a3b&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- prometheus 를 선택하고 Import 를 클릭하면 대시보드가 생성됩니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fac55e2ad-2ea5-4cec-add4-7530ea146f3d%2FUntitled.png?table=block&id=99e62713-bef1-460a-8d03-7570c6ac322f&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fc9b666c8-da74-4e2c-87c5-d3c8669a1d4f%2FUntitled.png?table=block&id=370ba870-5543-4b5e-b2a7-337b6936366e&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)