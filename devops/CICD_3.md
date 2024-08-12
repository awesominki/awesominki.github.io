---
layout: page
title: 3. CI/CD 구축 실습
description: >
hide_description: true
sitemap: false
---

# 

## 3.1 개요

- Gitlab을 사용하여 CI/CD 를 실습합니다.
- Gitlab 파이프 라인에서 build를 수행하여 AWS ECR에 도커 이미지를 등록하고 파이프라인의 deploy에서 ECS에 어플리케이션을 배포 합니다.

## 3.2 Gitlab

### 3.2.1 소개

- GitLab을 이용해서 CI/CD 환경을 구축하고 CI 과정을 자동화 합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F2f1922eb-118d-4b77-8be3-88ac8aaa4663%2FUntitled.png?table=block&id=0541a64d-aa0e-4308-967d-dae73f52659e&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1860&userId=&cache=v2)

    - GitLab 프로젝트를 만들고 여기에 개발한 코드를 PUSH 합니다.
    - 그러면 GitLab은 코드 Push 가 된 것을 확인하고 Docker Image를 생성 ECR에 등록합니다
    - 이 Docker Image로 부터 docker container를 ECS에 실행하게 됩니다

### 3.2.2 GitLab 가입 및 그룹, 프로젝트 생성

- GitLab 사이트에 가입

  https://about.gitlab.com/ 페이지에서 gitlab 서비스에 가입을 합니다.

- GitLab에 그룹을 생성하고 블랭크 프로젝트를 생성

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F33ef36a7-1283-4678-8313-07a4d4aee2c1%2FUntitled.png?table=block&id=f7fbcb55-ef31-49cd-93dc-05290511abe2&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=860&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Ff0036eb0-07d8-4ad0-abfc-4edcc6b57345%2FUntitled.png?table=block&id=78684e14-559a-4bc9-832f-b83e82c5c918&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=860&userId=&cache=v2)


### 3.2.3 spring 프로젝트 생성 및 준비(GtiLab 연동)

- 스프링 프로젝트를 생성하여 IDE 에서 열고 GitLab과 연동합니다.
    - 스프링 프로젝트 생성

      ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F5db0a1ed-54c8-42a0-8911-3bb6e4e4933e%2FUntitled.png?table=block&id=8f0c16d1-4d12-4cda-ba80-d1238f870943&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=860&userId=&cache=v2)

    - Gitlab 프로젝트에 들어가서 Code 버튼을 클릭하여 Clone with HTTPS 안에 텍스트를 복사

      ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F14b662c5-5861-4e05-8c1f-a9b404817d1c%2FUntitled.png?table=block&id=2ac53146-efa2-4039-9347-842b00975678&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=860&userId=&cache=v2)

    - 프로젝트 연동 원격정의 URL에 위의 주소를 붙여 넣습니다.

      ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fb35d3929-1ac6-432e-83f8-9cef00331af1%2FUntitled.png?table=block&id=8d9ca805-0397-442b-bf86-5b42232c3bd6&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=860&userId=&cache=v2)

      ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fd8500081-e36a-4683-9128-3e9469cadb3b%2FUntitled.png?table=block&id=e0823d07-c9ea-4dcf-827e-0434c1a66377&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=860&userId=&cache=v2)

      ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fdda539ac-0e0a-4273-abf6-ad811305b3b5%2FUntitled.png?table=block&id=7c1eac81-0a85-4d9a-90a6-0ef2221108cd&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=860&userId=&cache=v2)

      ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F47401d1f-4223-4d31-8792-97ad2918ef54%2FUntitled.png?table=block&id=a03c6ac4-6585-48c5-9f26-3c7e199ab500&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=860&userId=&cache=v2)

    - 프로젝트를 풀합니다. 만약 풀을 클릭하여 나온 창에 main 이라는 브랜치가 안나온다면 IDE를 종료하고 다시 실행합니다.

      ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fe5e61488-b185-4363-9ca3-86466767adf5%2FUntitled.png?table=block&id=cfd77309-b66b-4880-80fe-7d420210d3fa&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=860&userId=&cache=v2)

      ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F69cc8ad0-a705-4ca6-b811-f60c324fe939%2FUntitled.png?table=block&id=ff69dcb0-48dd-4385-9e75-f54787c5c117&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=860&userId=&cache=v2)

    - 프로젝트에 컨트롤러를 생성합니다. (src/main/java/com/spring/sample/SampleController.java)

        ```docker
        package com.spring.sample;
        
        import org.springframework.web.bind.annotation.GetMapping;
        import org.springframework.web.bind.annotation.RestController;
        
        @RestController
        public class SampleController {
        
            @GetMapping("/sample")
            public String sample() {
                return "Hello World";
            }
        }
        
        ```

    - application.properties를 수정합니다. (src/main/resources/application.properties)

        ```docker
        spring.application.name=sample
        
        server.port=8080
        ```

    - 루트에 Dockerfile을 생성합니다. (Dockerfile)

        ```docker
        FROM openjdk:17-jdk-slim
        
        VOLUME /tmp
        
        ARG JAR_FILE=build/libs/*.jar
        
        COPY ${JAR_FILE} app.jar
        
        ENTRYPOINT ["java","-jar","/app.jar"]
        ```



## 3.3 AWS 보안 그룹 추가

>📌 외부에서 컨테이너에 접속 할 수 있도록 포트을 열어줍니다.


- EC2 페이지에 접근 하여 보안 그룹을 클릭합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Ffde327d5-a958-4599-a9fc-2d59bf739ae1%2FUntitled.png?table=block&id=13b58e3f-41d3-44d2-b597-232990f25268&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

- 생성되어 있는 보안 그룹의 아이디를 클릭합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F35a4aa81-297a-4c17-9eab-ab299d41f77a%2FUntitled.png?table=block&id=0950ee45-d613-4a22-b3d6-7069568d452f&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

- 인바운드 규칙 편집을 클릭하고 8080포트와 80 포트를 추가합니다.
  두 포트 모두 0.0.0.0/0 을 입력해줍니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F0e36077d-225a-4774-89ee-208ddf311cca%2FUntitled.png?table=block&id=965c2e9f-17f6-44bf-96cf-f9f2f100cc57&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F39f94c1a-7da4-4395-b408-4ebbf5307749%2FUntitled.png?table=block&id=3b9462e9-6088-467d-be1d-f2a21e95c176&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)


## 3.4 AWS ECR

>📌 Amazon ECR은 AWS에서 제공하는 완전 관리형 docker image 저장소 입니다. 
Amazon의 ECS, EKS, Elastic Beanstalk 등 여러 서비스와 통합될 수 있습니다. 
배포하기 위해서는 일단 이미지를 ECR에 등록을 해야 합니다.


- AWS 에 가입후 ECR 대시보드에 접속합니다. 그후 리포지토리 생성을 클릭합니다. 식별하기 쉽도록 리포지토리 이름을 깃랩의 그룹/리포지토리 와 일치시킵니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F625a8573-bd62-42d4-bb00-640f986d98cf%2FUntitled.png?table=block&id=65dfe38b-8dd6-47b9-87bd-92c001655118&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=660&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fb0b159fc-5519-46d0-9e08-db4e44da68bb%2FUntitled.png?table=block&id=e20459f3-61c5-49b4-b405-9eb7911bc1b5&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- 생성한 리포지토리를 클릭하여 푸쉬 명령 보기를 클릭합니다. 이 명령어들은 깃랩 CI/CD에서 사용될 것입니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F1b468145-8e07-432e-a37f-91a4552d0875%2FUntitled.png?table=block&id=5e28726e-197d-480e-86fb-8413033295b2&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fa6faa726-5baa-4da2-8812-e56805b59230%2FUntitled.png?table=block&id=c474601a-21ef-45c3-9f4d-82fadd5e718b&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)


## 3.5 AWS ECS

>📌 Amazon ECS (Elastic Container Service)는 Amazon Web Services (AWS)에서 제공하는 완전 관리형 컨테이너 오케스트레이션 서비스입니다. 이는 Docker 컨테이너를 쉽게 실행, 정지, 관리할 수 있게 도와줍니다. ECS는 다음과 같은 주요 기능과 이점을 제공합니다: 
배포하기 위해서는 일단 이미지를 ECR에 등록을 해야 합니다.


- ECS 대시보드에 접속하여 클러스터 생성을 클릭합니다. 이름만 설정하고 인프라는 **서버리스** 선택 후 생성합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F83d23018-d3e9-46ca-ad3c-8c0606fe52d3%2FUntitled.png?table=block&id=e8751a67-91ed-4970-8f5a-e4e7d98554d9&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- 태스크 정의 메뉴에서 새 태스크 정의 생성을 클릭합니다. 그리고 패밀리 이름을 입력해줍니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F1e3ed1e5-5067-421d-9adc-39b28430b24c%2FUntitled.png?table=block&id=4c81fb4f-d776-4572-8cc7-d9eed1f5c212&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

- 스크롤 하여 컨테이너에 정보를 입력합니다. 이미지 URL에는 ECR의 리포지토리 URL을 입력합니다.
  포트에 80포트를 8080 으로 수정합니다.
  그 후 생성을 클릭합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fabbfffa0-d7aa-45ec-9a3f-9843ef77cb11%2FUntitled.png?table=block&id=faf93a2c-5756-44cc-9e3f-9d91196f039d&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fa5471bae-1e70-4413-9ef5-dbc50e7c241e%2FUntitled.png?table=block&id=58ebf076-16db-4228-a300-87dabd27d46c&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)


- 클러스터 메뉴로 돌아가서 생성된 클러스터를 클릭한 후, 서비스에서 생성을 클릭합니다.

  배포 구성에서 패밀리를 앞서 생성했던 태스크를 선택합니다. 서비스 이름도 입력해줍니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F527a73a6-1881-4451-bb08-339cf9e1db21%2FUntitled.png?table=block&id=8cf71fff-25f1-4c07-a15a-75b57a4d8e9e&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F735b9346-e294-41e7-8663-9496b72db36d%2FUntitled.png?table=block&id=24085be4-c4d7-415d-98b0-896fbb632373&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)


- 스크롤 하여 아래있는 로드 밸런싱에서 어플리케이션 로드 밸런싱을 선택합니다. 정보는 아래와 같이 입력합니다. 포트는 80 임을 확인합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F1d47cf53-806f-4341-9c69-caa6dad290c1%2FUntitled.png?table=block&id=62d47c98-5271-48eb-aa7c-d3c0521fdadf&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)


## 3.6 AWS IAM

- AWS IAM 에 접속하여 사용자 생성을 클릭합니다.
  직접 정책 연결을 클릭하여 [AdministratorAccess](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-northeast-2#/policies/details/arn%3Aaws%3Aiam%3A%3Aaws%3Apolicy%2FAdministratorAccess) 권한을 부여하여 사용자를 생성합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F3f15d148-4d68-4aac-af4b-a6e7a0993041%2FUntitled.png?table=block&id=2dad48cb-8f26-4155-82c5-c727001afa20&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fea790717-4694-4e33-9cfd-f28fd6f7e504%2FUntitled.png?table=block&id=c0c0c9b3-3c11-4234-a527-b0a969c86227&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- 생성한 사용자 상세페이지에 접속하여 액세스 키 만들기를 클릭합니다. 그후 기타를 클릭합니다.
  액세스 키가 만들어지면 .csv 파일 다운로드를 클릭하여 파일을 잘 가지고 있습니다 파일에는 엑세스에 관련된 키가 저장되어 있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F7c48e4c5-2792-4a37-aedb-a8e20e7af2a8%2FUntitled.png?table=block&id=a464993d-e14d-453e-a442-e336df353251&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Ff8c737c5-4f2d-4308-9c28-05b78688f6fe%2FUntitled.png?table=block&id=36bca3e6-16bf-46dd-8ba7-6cde85c3d0d1&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)


## 3.7 깃랩 ci 파일 작성 및 푸쉬

- 깃랩 프로젝트 리포지토리 메뉴에서 Settings > CI/CD > Variables 를 클릭하고 2개 변수를 추가합니다.

  AWS_ACCESS_KEY_ID

  AWS_SECRET_ACCESS_KEY
  이 두 키의 값은 아까 AWS IAM 에서 다운받은 csv 에 있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fcc5b8313-be82-42dc-b05d-32f2832f849d%2FUntitled.png?table=block&id=c9772cab-190b-457d-955b-5fd9dd580c35&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

- 프로젝트 루트 폴더에 .gitlab-ci.yml을 생성합니다.
  docker build 안에 스크립트는 ECR에서 푸쉬 명령 보기를 참고하여 작성합니다.

  deploy 안에 있는 항목은 ECS의 생성 항목들 이름을 참고합니다.

    ```docker
    services:
      - docker:stable-dind
    stages:
      - build jar
      - build and push docker image
      - deploy
    variables:
      APPLICATION_NAME: "01"
      TAG_NAME: "latest"
      DOCKER_IMAGE: "group-18934028/project-01"
    build:
      image: openjdk:17-jdk-slim
      stage: build jar
      script:
        - chmod +x gradlew
        - ./gradlew clean build
      artifacts:
        paths:
          - build/libs/*.jar
    docker build:
      image: docker:latest
      stage: build and push docker image
      rules:
        - if: $CI_COMMIT_BRANCH == "main" || $CI_COMMIT_REF_NAME == "main"
          variables:
            TAG_NAME: "latest"
        - if: $CI_COMMIT_BRANCH == "develop" || $CI_COMMIT_REF_NAME == "develop"
          variables:
            TAG_NAME: "develop"
    
      script:
        - apk add --update --no-cache curl py3-pip py3-virtualenv
        - python3 -m venv /tmp/venv
        - source /tmp/venv/bin/activate
        - pip install awscli
        - aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
        - aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY
        - aws configure set region ap-northeast-2
        - docker build -t $DOCKER_IMAGE .
        - docker tag $DOCKER_IMAGE:latest 730335597998.dkr.ecr.ap-northeast-2.amazonaws.com/$DOCKER_IMAGE:$TAG_NAME
        - aws ecr get-login-password --region ap-northeast-2 | docker login --username AWS --password-stdin 730335597998.dkr.ecr.ap-northeast-2.amazonaws.com
        - docker push 730335597998.dkr.ecr.ap-northeast-2.amazonaws.com/$DOCKER_IMAGE:$TAG_NAME
    
    deploy:
      image: python:3.9-slim
      stage: deploy
      script:
        - python3 -m venv /tmp/venv
        - source /tmp/venv/bin/activate
        - pip install awscli
        - aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
        - aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY
        - aws configure set region ap-northeast-2
        - aws ecs update-service --cluster project-cluster --service project-01-service --task-definition project-01-task:1 --force-new-deployment
    
    ```

- IDE에서 프로젝트를 커밋, 푸쉬하여 반영합니다.
  줄 구분 기호 경고가 나오면 그대로 커밋을 입력합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F7606ad02-f0d6-4a98-903e-446731b8de78%2FUntitled.png?table=block&id=6fdd573f-89e3-4b72-ac3d-1c88004d8555&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=670&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fcfb1fc88-8474-4435-a094-347631419174%2FUntitled.png?table=block&id=21cf3c30-21ff-4a18-8321-2b3d17f4aebf&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=670&userId=&cache=v2)

- GitLab 의 파이프라인을 확인합니다.
  스테이지가 모두 성공하지 않으면 실패한 스테이지를 클릭하여 오류 내용을 확인합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F6064c4ad-b0f4-48e5-8467-3e2e194323b4%2FUntitled.png?table=block&id=4c014a1b-1767-4696-a140-8afb7c99be8a&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)


## 3.8 확인하기

- ECS 대시보드에서 클러스터를 클릭하고 서비스 상세 페이지 까지 접속합니다. 그후 로드 밸런서 보기 버튼을 클릭합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F768239ab-ebc7-4dbc-999b-d98a2c9c41ab%2FUntitled.png?table=block&id=f9bd35ec-3586-4515-8740-116164033755&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

- 로드 밸런서 페이지에서 DNS 이름을 복사하여 웹에 입력하고 스프링 프로젝트의 엔드 포인트에 접근하여 결과를 확인합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F976eaad9-387b-4492-b75a-61514af12a1e%2FUntitled.png?table=block&id=c82e0e7b-4a23-475b-8342-8f814b63d9b4&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F16ca7361-5f35-4136-b290-df7dbe8dc608%2FUntitled.png?table=block&id=39c89a96-d2cc-4b0b-af81-38c80013b9a5&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)


## 3.9 추가 정보 깃허브 액션

>📌 깃허브 액션에서도 같은 작업을 해보도록 하죠!!!


- 인텔리제이 설정에서 깃허브를 로그인후 메뉴에서 VCS 또는 Git 메뉴에서 Github에 프로젝트 공유를 클릭하여 깃허브에리포지토리를 생성합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F6a53e165-c6af-403a-8cd9-3fcff1bde8a5%2FUntitled.png?table=block&id=9d3965b8-4d5e-4d9a-a91e-e7fd2303be00&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F029d7aa1-136f-495b-887b-7c4b021523ae%2FUntitled.png?table=block&id=bdfce040-ed84-4f1d-941d-d19884173ede&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- Actions 페이지에서 Ecs를 검색하고 “Configure”를 클릭합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fa1d8fd11-4b43-4aca-9230-19454cc30c09%2FUntitled.png?table=block&id=11b90985-32c3-4587-9328-ab2da823ee03&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- 이후 나오는 페이지의 내용을 아래의 내용으로 대체 합니다.
  **내용을 대체할 때, env 영역의 정보를 나의 AWS 정보로 대체하여 입력합니다.**
  그후 “Commit changes”버튼을 클릭합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F039511d8-f755-4147-a652-1f15e08e776b%2FUntitled.png?table=block&id=dec12c85-4332-494d-9779-d216815bdfc7&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

    ```yaml
    # This workflow will build and push a new container image to Amazon ECR,
    # and then will deploy a new task definition to Amazon ECS, when there is a push to the "main" branch.
    #
    # To use this workflow, you will need to complete the following set-up steps:
    #
    # 1. Create an ECR repository to store your images.
    #    For example: `aws ecr create-repository --repository-name my-ecr-repo --region us-east-2`.
    #    Replace the value of the `ECR_REPOSITORY` environment variable in the workflow below with your repository's name.
    #    Replace the value of the `AWS_REGION` environment variable in the workflow below with your repository's region.
    #
    # 2. Create an ECS task definition, an ECS cluster, and an ECS service.
    #    For example, follow the Getting Started guide on the ECS console:
    #      https://us-east-2.console.aws.amazon.com/ecs/home?region=us-east-2#/firstRun
    #    Replace the value of the `ECS_SERVICE` environment variable in the workflow below with the name you set for the Amazon ECS service.
    #    Replace the value of the `ECS_CLUSTER` environment variable in the workflow below with the name you set for the cluster.
    #
    # 3. Store your ECS task definition as a JSON file in your repository.
    #    The format should follow the output of `aws ecs register-task-definition --generate-cli-skeleton`.
    #    Replace the value of the `ECS_TASK_DEFINITION` environment variable in the workflow below with the path to the JSON file.
    #    Replace the value of the `CONTAINER_NAME` environment variable in the workflow below with the name of the container
    #    in the `containerDefinitions` section of the task definition.
    #
    # 4. Store an IAM user access key in GitHub Actions secrets named `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`.
    #    See the documentation for each action used below for the recommended IAM policies for this IAM user,
    #    and best practices on handling the access key credentials.
    
    name: Deploy to Amazon ECS
    
    on:
      push:
        branches: [ "main" ]
    
    env:
      AWS_REGION: ap-southeast-2                  # set this to your preferred AWS region, e.g. us-west-1
      ECR_REPOSITORY: group-18934028/test-01         # set this to your Amazon ECR repository name
      ECS_SERVICE: test-01-service                 # set this to your Amazon ECS service name
      ECS_CLUSTER: test-01-cluster                # set this to your Amazon ECS cluster name
      ECS_TASK_DEFINITION: .github/workflows/project-01-task-revision1.json # set this to the path to your Amazon ECS task definition
                                                   # file, e.g. .aws/task-definition.json
      CONTAINER_NAME: test-01-container           # set this to the name of the container in the
                                                   # containerDefinitions section of your task definition
    
    permissions:
      contents: read
    
    jobs:
      build:
        name: Build Jar
        runs-on: ubuntu-latest
    
        steps:
        - name: Checkout
          uses: actions/checkout@v4
    
        - name: Set up JDK 17
          uses: actions/setup-java@v3
          with:
            java-version: '17'
            distribution: 'temurin'
    
        - name: Grant execute permission for gradlew
          run: chmod +x gradlew
    
        - name: Build with Gradle
          run: ./gradlew clean build
    
        - name: Upload Build Artifacts
          uses: actions/upload-artifact@v3
          with:
            name: build-libs
            path: build/libs/*.jar
    
            
      deploy:
        name: Deploy
        runs-on: ubuntu-latest
        needs: build
        environment: production
    
        steps:
        - name: Checkout
          uses: actions/checkout@v4
    
        - name: Download Build Artifacts
          uses: actions/download-artifact@v3
          with:
            name: build-libs
            path: build/libs
    
        - name: Configure AWS credentials
          uses: aws-actions/configure-aws-credentials@v1
          with:
            aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
            aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
            aws-region: ${{ env.AWS_REGION }}
    
        - name: Login to Amazon ECR
          id: login-ecr
          uses: aws-actions/amazon-ecr-login@v1
    
        - name: Build, tag, and push image to Amazon ECR
          id: build-image
          env:
            ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
            IMAGE_TAG: ${{ github.sha }}
          run: |
            # Build a docker container and
            # push it to ECR so that it can
            # be deployed to ECS.
            docker build -t $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG .
            docker push $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG
            echo "image=$ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG" >> $GITHUB_OUTPUT
    
        - name: Fill in the new image ID in the Amazon ECS task definition
          id: task-def
          uses: aws-actions/amazon-ecs-render-task-definition@v1
          with:
            task-definition: ${{ env.ECS_TASK_DEFINITION }}
            container-name: ${{ env.CONTAINER_NAME }}
            image: ${{ steps.build-image.outputs.image }}
    
        - name: Deploy Amazon ECS task definition
          uses: aws-actions/amazon-ecs-deploy-task-definition@v1
          with:
            task-definition: ${{ steps.task-def.outputs.task-definition }}
            service: ${{ env.ECS_SERVICE }}
            cluster: ${{ env.ECS_CLUSTER }}
            wait-for-service-stability: true
    ```

- 깃허브 프로젝트 루트에서 Code버튼을 클릭하고 Codespaces 탭에서 Create codespace on main을 클릭하여 웹 IDE를 엽니다.(이 내용은 인텔리제이에서 해도 무방합니다.)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F7cd252bd-e65c-470e-858c-49f79827272c%2FUntitled.png?table=block&id=86e4cdf8-d283-4cee-aa9f-6e3b8e22f56c&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- .github/workflows 폴더에 project-01-task-revision1.json이라는 파일을 만듭니다.
  AWS의 테스크 정의 상세 페이지에서 JSON 탭을 클릭하여 클립보드로 복사 버튼을 클릭하여 내용을 복사합니다.

  project-01-task-revision1.json 파일에 복사한 내용을 붙여넣기 하고 커밋, 푸쉬 합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F77003d57-1110-4315-9d66-59ef5de15911%2FUntitled.png?table=block&id=e65b610f-bd73-4e66-9e9c-d8131ea1178d&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F8f1879f6-6443-48dc-bc30-afb38e4c5461%2FUntitled.png?table=block&id=68ff23ef-bb28-4893-95e5-4e67ce4fc789&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fa796a620-6c93-43ec-ae76-c502f4fe18a0%2FUntitled.png?table=block&id=41479e64-4f88-4a22-9e38-15223831dfbd&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fa5221087-e644-4386-82e2-762235fddffb%2FUntitled.png?table=block&id=b3404713-8982-4ad2-8fd6-e7b8ace2c0a6&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

- 깃허브 리포지토리의 Settngs > Secrity > Secrets and variables > Actions의 페이지에서 Secrets 탭에서 새로운 시크릿을 입력합니다.

  AWS IAM 에서 생성한 엑세스 키의 값을 넣어주며 2개를 생성합니다.

  `AWS_ACCESS_KEY_ID` , `AWS_SECRET_ACCESS_KEY`

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F185ff89b-3868-485b-b6f5-e8cd84f67c75%2FUntitled.png?table=block&id=44c8298a-439a-40f8-91a5-d315231fe55a&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fd8ef6663-0254-4d5f-9361-178f0bb30401%2FUntitled.png?table=block&id=899a1a72-4e74-4aa6-8bb5-76d037ed9d2b&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- 이제 Actions 탭의 workflow 를 실행하고 Gitlab에서와 같이 웹에서 로드 밸런서의 DNS 이름으로 접속하여 배포를 확인합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F312e52ff-46b9-4ece-bb86-93f60ee78cd2%2FUntitled.png?table=block&id=bb325a1b-679b-4ce5-b1fb-3fc279c57f39&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F976eaad9-387b-4492-b75a-61514af12a1e%2FUntitled.png?table=block&id=2bdc05da-326a-4cdc-91b3-4da1e22a753d&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F16ca7361-5f35-4136-b290-df7dbe8dc608%2FUntitled.png?table=block&id=3bb93949-1398-4809-8bb0-137b7c56b1f0&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)