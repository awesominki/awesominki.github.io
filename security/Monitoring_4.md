---
layout: page
title: 4. 그라파나에서 Slack으로 Alert 보내기
description: >
hide_description: true
sitemap: false
---

### 4.1 슬랙 앱 생성

- 먼저 슬랙에 가입해서 워크스페이스를 생성해주세요~!
- https://api.slack.com/apps 에 접속해서 Create an App을 클릭합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F75639a2f-2386-481c-9a27-adf5db2a9c59%2FUntitled.png?table=block&id=52a224a0-13df-4540-ae1e-feeb22b8d9ac&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- 팝업이 뜨면 From scratch 를 클릭합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F1c194730-ace5-4590-8315-789585e283ec%2FUntitled.png?table=block&id=4e0f7a01-b5e4-42ec-b456-0ed2424be2d4&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- 앱이름을 입력하고 생성한 워크스페이스를 선택합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fad6feda0-a364-4a05-b8cb-8b39dcb9f345%2FUntitled.png?table=block&id=6da4e6d7-dcd3-48ce-bc07-90c7365ec549&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

- **OAuth & Permissions 메뉴에 접속합니다.**

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fe0376c5b-07ce-4b5e-a7ba-ba26adaf66c3%2FUntitled.png?table=block&id=490be7e6-7daf-49ad-8997-3270fb0677cb&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

- 스크롤 하여 Scopes 항목으로 갑니다. Bot Token Scopes에 “chat:write”를 추가합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fc7756e3d-1fea-4967-8451-740a3f7a4edb%2FUntitled.png?table=block&id=16955991-888e-4462-8a5b-3bfd199c9cea&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

- 그후 스크롤을 올라와서  Install to Workspace 를 클릭합니다. 그리고 허용을 클릭합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F091732d2-50ba-4c7f-8be3-3f9cae66ee4f%2FUntitled.png?table=block&id=379d354d-bf7a-4f96-9057-bcc3513d82ba&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fd9c790b4-c9d8-42c3-8fed-326bf3b35e1a%2FUntitled.png?table=block&id=41fe223b-01c5-4e1f-b5af-8a92d4e18773&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

- Incoming Webhooks 메뉴에 접속합니다. Activate Incoming Webhooks 를 활성화 한 후, 하단에 Add New Webhook to Workspace를 클릭합니다. 다음 화면에서 전달 받고 싶은 슬랙 채널을 선택합니다.
  그리고 생성된 Webhook URL을 복사해 둡니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F2a430a8c-87c1-4fde-840b-8dcf59687f70%2FUntitled.png?table=block&id=88ffa918-965d-4239-8348-e0fdb1c9b61c&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F5e8a22cd-f6ff-4ba1-ae3a-119e9888fa3b%2FUntitled.png?table=block&id=8a7c3b81-2177-4513-b4c2-d597fb103718&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fb9202bc4-c9b0-4e46-90e7-a829980b57cc%2FUntitled.png?table=block&id=18eb9c44-f47c-4134-a662-90b88b3bdfd3&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- 슬랙 앱에서 해당 채널에 가서 생성한 App을  “@”를 사용하여 추가해줍니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F8d20c966-dda2-446b-a9a5-e74a96ace6ea%2FUntitled.png?table=block&id=f7ca37c2-3d96-4f1f-9c8b-44501c7067ab&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)


### 4.2 그라파나 Alert 설정

- 그라파나에서 사이드메뉴에 Alerting > Contact points 에 접속하여 Add contact point 버튼을 클릭합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F35a4cac0-d461-4430-951e-71241e6462d6%2FUntitled.png?table=block&id=3781bd8f-d67d-4cab-b299-04277e49b9fe&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fa0361ef4-cbe5-4bf1-b546-4fe3af3b1e30%2FUntitled.png?table=block&id=452ef120-b5d2-415f-b58f-703a7341132a&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

- Name 을 입력한 후, Integration 을 Slack을 선택합니다. 그후 Webhook URL에 아까 앱에서 복사한 URL을 입력합니다. 테스트 버튼을 클릭하면 테스트 메시지가 슬랙 채널로 오는 것을 확인할 수 있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Ff3e1aa8e-4f79-42c8-acbb-ca73275f4783%2FUntitled.png?table=block&id=cddc9eb8-24b2-4817-8e38-3615450c73d8&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fd999c5a4-d0d1-4f25-88cf-200bfcf58f29%2FUntitled.png?table=block&id=12e594f1-f46b-4798-b6a9-b5567e999d8e&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

- 그라파나 사이드 메뉴에서 Alerting > Notification policies 로 들어갑니다. 그 후 Default policy의 edit 버튼을 클릭합니다. 이후 나온 Edit창에서 Default contact point 를 이전에 생성한  contact point로 선택합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F3e70ce81-2a63-4c19-982b-0a401f538bf8%2FUntitled.png?table=block&id=9e673509-d06c-487d-9f8c-99342ac95d61&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F5c28276f-2bd0-47b6-a0d8-4c274607a16b%2FUntitled.png?table=block&id=ab3e0284-2243-44a7-8cd0-2233c43bf0c3&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F24545732-70b6-4fa8-994c-6785d22dc45a%2FUntitled.png?table=block&id=8111150b-5379-440a-9aa1-0c966d8346a9&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

- Alerting > Alert rules 를 클릭하여 “New alert rule”을 클릭합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Ff1c08fe8-1618-45f2-8332-a7fa40b5312b%2FUntitled.png?table=block&id=c9b4e8d9-0902-4b42-b5fb-d6d1b503ff1b&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Ffc4d495e-3963-43d5-a401-7a3e2361f5f8%2FUntitled.png?table=block&id=cc8f638d-f0e8-4be6-966f-31cca5a31e27&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- 알림 이름을 입력합니다. Define query and alert condition 에서 matric을 UP을 선택하고 Label filter 에서 Job , spring-boot 를 선택합니다.

  Expression 의 Threshold에서 IS BLOW를 선택 숫자는 1을 입력합니다. 이를 통해 만약 애플리케이션이 정지되면 알람이 발송되게 됩니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F71af8cca-2b11-43fc-bff8-837234b0682b%2FUntitled.png?table=block&id=daa1a645-8928-44a8-8337-da22e3cc5c38&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

- 스크롤 하여 내려가면 Set evaluation behavior 섹션을 볼 수 있습니다.

  Folder 및 Evaluation group을 선택또는 새로 생성합니다.

  pending period, Evaluation internal 은 빠른 확인을 위해 1m으로 설정합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fbee6b04f-7247-4ce7-b948-703a68985ba6%2FUntitled.png?table=block&id=399ffcbc-e69e-42bb-b712-7c5065e8aa45&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F9eb215a8-fa60-4b24-bf85-5623f85db433%2FUntitled.png?table=block&id=79fd3d75-b73b-4411-bf67-168be1c78063&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- 좀더 스크롤해 내려가면 Confifure labels and notifications 메뉴가 있습니다. 해당 메뉴의 contact point 을 이전에 설정한 slack으로 설정합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F5cbb1d97-5a88-4a1c-bb39-bb840b5855d1%2FUntitled.png?table=block&id=670d4585-def9-4a68-9106-c461d1e9df79&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)


### 4.3 그라파나 Alert 설정 확인

- Alert rules에 생성한 Alert이 노출 됩니다. 상태는 Normal인 것을 확인할 수 있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F78d488f8-404a-452e-8c32-fa02d66accd5%2FUntitled.png?table=block&id=62f4b8ec-8470-47d4-adb4-735ba30977fa&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

- 스플링 애플리케이션을 정지 시키면 Normal 이었던 상태가 Pending > Firing으로 변경됩니다.

  그후 잠시 기다려면 슬랙 채널로 Firing 알람이 오는것을 확인할 수 있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F1090a6e6-060d-4a5e-8512-b2493cc85316%2FUntitled.png?table=block&id=c45b9002-06be-4254-939b-2f7a320b6167&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F015a97f2-d57f-48e8-814e-aadc269f1e3f%2FUntitled.png?table=block&id=8170bc57-c6c1-456b-af00-366de858dee9&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F869912a4-9bc4-470a-af6b-2b1e51eccfe5%2FUntitled.png?table=block&id=c1bbb5fa-761e-4cb4-85bf-f1d022b4b05a&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

- 다시 스프링 애플리케이션을 실행한후 기다리면 슬랙 채널로 Resolved 알람이 오는것을 확인할수 있습니다.

  (해당 알람은 도착하는데 시간이 걸립니다!)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fb2f3ffe2-2de5-421b-ba99-a9259c3c3c1c%2FUntitled.png?table=block&id=0b7d54e7-bc45-4817-8270-4ac9702eb9ae&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)