---
layout: page
title: 2. AmazonECS
description: >
hide_description: true
sitemap: false
---

## 2.1 Amazon ECS 개요

### 2.1.1 Amazon ECS란?

- AWS Elastic Container Service는 docker 애플리케이션을 쉽게 배포하고 운영 할 수 있도록 지원하는 완전관리형 Container Orchestration 서비스
- Kubernetes와 같은 container Orchestration 서비스
- Kubernetes 보다 사용하기 쉽고, 비용적으로도 저렴하기 때문에 소/중 규모의 프로젝트에 널리 사용
- serverless로 구성할 수 있는데, 이렇게 하면 인스턴스(가성서버)를 구성하고 관리할 필요도 없음

### 2.1.1 구조

- ECS는 크게 ECR, ECS Cluster, ECS Service, ECS Task로 이루어집니다.
    - ECR: Docker image 저장소
    - ECS Cluster: 컨테이너를 실행하기 위한 Cluster로 여러 인스턴스로 이루어집니다. 이 인스턴스에 Docker container가 분산 실행됩니다. Serverless로 할 경우에는 인스턴스도 필요가 없습니다.
    - ECS Server: Docker 애플리케이션의 실행 그룹입니다.
    - ECS Task: ECS Server에 실제로 실행되는 docker container들을 Task 라고 합니다.
    - 로드밸런서와, 모니터링, Auto Scaling 등의 요소도 있음. ECS를 이용하면 이런 요소들까지 자동으로 관리

      ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fae4b5629-2294-41de-885b-05bb56a80583%2FUntitled.png?table=block&id=1edfc72c-ee61-4181-a9b6-c33043b7d65c&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1300&userId=&cache=v2)

- Task definition
    - ECS의 최소 실행단위는 “Task”
    - “Service”는 Task가 두개 이상 모인것
    - Task는 docker 애플리케이션
    - 어느 포트에서 몇개의 도커 컨테이너를 어떤 이미지로 실행할지 애플리케이션 실행 정보가 필요함.
    - 이 정보를 저장하고 있는것이 Task definition

      ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Faaf5e2b4-bcd5-4383-90fb-c2929afbc6db%2FUntitled.png?table=block&id=96852ad4-2076-48ed-890a-444610da760a&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)
