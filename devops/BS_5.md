---
layout: page
title: 5. Jmeter로 TPS 받기
description: >
hide_description: true
sitemap: false
---

### 5.1 Jmeter 설치 및 실행

```java
#### 일반 맥
brew install jmeter
open /usr/local/bin/jmeter

####실리콘 맥
#https://jmeter.apache.org/download_jmeter.cgi 사이트에서 Binaries의 압축파일을 다운받아 압축을 해제 합니다.
#그후 해당 폴더안에 bin 폴더에서 jmeter.sh 를 실행합니다.

cd apache-jmeter-5.6.2
./bin/jmeter.sh
```

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F55922166-3024-45b8-a35f-ca049c946780%2FUntitled.png?table=block&id=ae3f2dc3-e30c-4d32-be38-02ff2996ade5&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)


### 5.2 Jmeter 테스트하기

- 테스트계획 > 추가 > 쓰레드들 > 쓰레드 그룹을 추가합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fc144d129-a083-46c6-b746-68fe3863e2a2%2Fcc0df425-18c3-463f-8294-907b87743285.png?table=block&id=cfabfa79-b4b7-4e20-b845-9508a86d6142&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1140&userId=&cache=v2)

- 쓰레드 속성을 입력하여 테스트할 시나리오를 작성합니다. 아래의 이미지는 100명의 사용자가 각 10번씩 요청하여 1000번의 요청이 이루어지는 테스트 시나리오입니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fbe7382ab-0683-4eb1-bd14-80cc72ee1ab2%2FUntitled.png?table=block&id=26c7d46e-8a6a-42f0-a864-9ec7bcaeb36b&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

- 쓰레드 그룹 > 추가 > 표본추출기 > HTTP 요청을 클립합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F13632bc6-d68c-4d26-86b7-36face06ef73%2FUntitled.png?table=block&id=a30caa16-5bbf-4a7a-87da-4e35573835ba&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

- HTTP 요청의 항목을 입력합니다. 지금까지 PostMan으로 테스트 한것을 기반으로 입력하면 되겠습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F1e930533-6268-4057-a4cf-788ba1d811ae%2FUntitled.png?table=block&id=23811cec-f005-4c38-aa2b-318a8d5f3e65&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fd18b5981-a861-4830-be34-30aa7bd5eb98%2FUntitled.png?table=block&id=e10faf50-4338-4678-ab93-80bee666aad7&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- HTTP 요청 > 추가 > 설정 엘리먼트 > HTTP 헤더 관리자 를 추가합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fea470836-b403-4f57-a4dc-5b2c953106d9%2FUntitled.png?table=block&id=94fbfe57-3208-4cc8-9540-ae4dc8a8cec0&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- 헤더관리자의 속성을 추가합니다. Talend 요청을 참고합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fbf6415d4-acbd-4092-9281-3201f36c6e84%2FUntitled.png?table=block&id=bb3cd23d-73f3-4d5f-9a29-92a1307a3367&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F31915ff0-d9cc-4382-ae91-0059f985aa68%2FUntitled.png?table=block&id=22582d7e-9e7b-405f-9f19-8d282ef5647c&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- 쓰레드 그룹 > 추가 > 리스너 > 결과들의 트리 보기 및 요약 보고서를 추가합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fcdde7cb4-c104-4027-bd2f-30844221e47f%2FUntitled.png?table=block&id=163acaf2-9dbf-496e-98e4-4201ecad70a4&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- 상단의 재생버튼을 클릭하면 테스트를 진행 할 수 있습니다. 테스트를 재실행하기전에는 클리어 버튼을 클릭하여 테스트를 초기화 할 수 있습니다.

  결과들의 트리보기에서는 테스트 요청에 대한 응답 결과를 볼 수 있습니다.

  요약 보고서에서는 처리량을 통해 TPS를 확인 할 수 있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fc3d640ea-f906-4bc2-b04c-c34d83d05cb2%2FUntitled.png?table=block&id=5a5f05e7-2434-48a4-b998-564fdbd56317&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F8d29635d-e68d-48ac-8d64-abef9dfecd87%2FUntitled.png?table=block&id=401f3b3e-314a-4cf2-87c1-98024df3052f&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)