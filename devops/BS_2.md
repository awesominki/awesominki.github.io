---
layout: page
title: 2. RabbitMQ
description: >
hide_description: true
sitemap: false
---

## 2.1 RabbitMQ란

- **RabbitMQ**는 메시지 브로커입니다. 메시지 브로커는 데이터(메시지)를 송신자(프로듀서)로부터 수신자(컨슈머)에게 전달하는 중간 매개체 역할을 합니다.
- RabbitMQ는 이러한 메시지를 큐(queue)에 저장하고, 필요할 때 적절한 수신자에게 전달합니다.

### 2.1.1 **RabbitMQ의 역할**

- **비동기 처리**: 데이터를 비동기적으로 처리하여 시스템의 응답성을 높입니다.
- **부하 분산**: 여러 소비자에게 메시지를 분산시켜 시스템의 부하를 균형 있게 분산합니다.
- **내결함성**: 메시지를 안전하게 저장하여 시스템 장애 시 데이터 손실을 방지합니다.

### 2.1.2 장단점

- 장점
    - **신뢰성(Reliability)**
        - **메시지 지속성**: RabbitMQ는 메시지를 디스크에 저장하여 시스템 장애 발생 시에도 메시지가 손실되지 않도록 합니다.
        - **확인 메커니즘**: 메시지가 성공적으로 소비자에게 전달되었는지 확인하는 ACK(acknowledgment) 메커니즘을 지원합니다.
    - **유연성(Flexibility)**
        - **다양한 메시지 패턴**: RabbitMQ는 여러 가지 메시지 전달 패턴(단일 소비자, 다중 소비자, 라운드 로빈, 팬아웃, 주제 기반 등)을 지원합니다.
        - **프로토콜 지원**: 기본적으로 AMQP(Advanced Message Queuing Protocol)를 사용하지만, STOMP, MQTT 등 다양한 프로토콜도 지원합니다.
    - **확장성(Scalability)**
        - **클러스터링**: RabbitMQ는 클러스터링을 통해 여러 노드로 구성된 환경에서 높은 가용성과 부하 분산을 제공합니다.
        - **분산 아키텍처**: 페더레이션(Federation) 등을 사용하여 여러 RabbitMQ 인스턴스 간에 메시지를 전달함으로써 분산된 메시징 시스템을 구축할 수 있습니다.
    - **관리 및 모니터링(Manageability and Monitoring)**
        - **관리 인터페이스**: 웹 기반 관리 인터페이스를 통해 큐, 익스체인지, 바인딩 등을 쉽게 관리할 수 있습니다.
        - **플러그인 시스템**: 다양한 플러그인을 통해 기능을 확장할 수 있습니다(예: 관리 플러그인, 모니터링 플러그인 등).
    - **성능(Performance)**
        - **높은 처리량**: 적절히 구성된 RabbitMQ는 높은 메시지 처리량을 제공하여 대규모 애플리케이션에서도 효과적으로 사용할 수 있습니다.
- 단점
    - **설정 및 운영 복잡성(Setup and Operational Complexity)**
        - **복잡한 설정**: RabbitMQ의 초기 설정이 다소 복잡할 수 있으며, 클러스터링 및 분산 환경에서는 더욱 많은 설정이 필요합니다.
        - **운영 관리**: 대규모 환경에서 RabbitMQ를 운영하고 관리하는 데 있어서 추가적인 노력이 필요할 수 있습니다.
    - **성능 문제(Performance Issues)**
        - **메시지 브로커 오버헤드**: RabbitMQ는 모든 메시지를 중앙 브로커를 통해 전달하기 때문에, 높은 트래픽 상황에서는 브로커의 오버헤드가 발생할 수 있습니다.
        - **대규모 메시지 처리**: 매우 대규모의 메시지를 처리할 때 성능 저하가 발생할 수 있으며, 이러한 경우에는 적절한 클러스터링 및 최적화가 필요합니다.
    - **운영 비용(Operational Costs)**
        - **리소스 소비**: RabbitMQ는 메모리와 CPU 자원을 많이 소비할 수 있어, 충분한 리소스를 제공해야 원활하게 운영될 수 있습니다.
        - **모니터링 및 유지보수**: 지속적인 모니터링과 유지보수가 필요하며, 이를 위해 추가적인 인력과 비용이 발생할 수 있습니다.
    - **제한된 메시지 크기(Limited Message Size)**
        - **메시지 크기 제한**: RabbitMQ는 매우 큰 메시지 처리에 제한이 있을 수 있으며, 대용량 파일 전송에는 적합하지 않을 수 있습니다.
    - **러닝 커브(Learning Curve)**
        - **학습 필요성**: RabbitMQ의 개념과 설정을 이해하는 데 시간이 걸릴 수 있습니다.


## 2.2 **RabbitMQ의 기본 구성 요소**

### 2.2.1 **메시지(Message)**

- 메시지는 RabbitMQ를 통해 전달되는 데이터 단위입니다. 예를 들어, 사용자 등록 정보나 주문 내역이 메시지가 될 수 있습니다.

### 2.2.2 **프로듀서(Producer)**

- 메시지를 생성하고 RabbitMQ에 보내는 역할을 합니다. 예를 들어, 웹 애플리케이션이 사용자 등록 정보를 RabbitMQ에 보내는 경우 프로듀서가 됩니다.

### **2.2.3 큐(Queue)**

- 메시지를 저장하는 장소입니다. 메시지는 큐에 저장되었다가 소비자에게 전달됩니다. 큐는 FIFO(First In, First Out) 방식으로 메시지를 처리합니다.

### **2.2.4 컨슈머(Consumer)**

- 큐에서 메시지를 가져와 처리하는 역할을 합니다. 예를 들어, 이메일 발송 서비스가 큐에서 사용자 등록 정보를 가져와 환영 이메일을 보내는 경우 컨슈머가 됩니다.

### **2.2.5 익스체인지(Exchange)**

- 메시지를 적절한 큐로 라우팅하는 역할을 합니다. 프로듀서는 메시지를 직접 큐에 보내지 않고, 익스체인지에 보내며, 익스체인지는 메시지를 적절한 큐로 전달합니다.

## 2.3 **RabbitMQ와 AMQP**

- RabbitMQ는 **AMQP**(Advanced Message Queuing Protocol)를 사용합니다.
- AMQP는 메시지 브로커를 위한 프로토콜로, 메시지의 생성, 전송, 큐잉, 라우팅 등을 표준화하여 메시지 브로커가 상호 운용될 수 있게 합니다.
- 여기서 프로토콜(Protocol)은 컴퓨터 네트워크에서 데이터를 주고받기 위한 일련의 규칙과 절차를 말합니다. 쉽게 말해, 컴퓨터나 장치들이 서로 통신할 때 어떻게 소통해야 하는지를 정해 놓은 약속입니다.

### **3.3.1 AMQP의 주요 개념**

- **메시지(Message)**: 전송되는 데이터 단위입니다.
- **큐(Queue)**: 메시지를 저장하고 전달하는 구조입니다.
- **익스체인지(Exchange)**: 메시지를 큐로 라우팅하는 역할을 합니다.
- **바인딩(Binding)**: 익스체인지와 큐를 연결하는 설정입니다. 바인딩을 통해 메시지가 어느 큐로 전달될지 정의합니다.

### 3.3.2 익스체인지 유형

- 메시지 브로커가 메시지를 교환기에서 큐로 라우팅하는 방식입니다.
- 익스체인지는 다양한 방식으로 메시지를 라우팅할 수 있으며, 주로 메시지의 라우팅 키와 바인딩 키 또는 패턴을 기반으로 작동합니다.
1. **Direct Exchange**
    - 라우팅 키가 정확히 일치하는 큐로 메시지를 전달합니다.
    - 예를 들어, 라우팅 키가 error인 메시지는 error라는 바인딩 키를 가진 큐로 전달됩니다.
2. **Topic Exchange**
    - 라우팅 키의 패턴을 사용하여 메시지를 라우팅합니다. 패턴에는 와일드카드 * (단어 하나)와 # (0개 이상의 단어)가 사용됩니다.
    - 예를 들어, 라우팅 키가 quick.orange.rabbit인 메시지는 바인딩 키가 *.orange.*인 큐로 전달됩니다.
3. **Fanout Exchange**
    - 라우팅 키를 무시하고 교환기에 바인딩된 모든 큐로 메시지를 브로드캐스트합니다.
    - 모든 바인딩된 큐로 메시지가 전달됩니다.
4. **Headers Exchange**
    - 라우팅 키 대신 메시지의 헤더를 기반으로 메시지를 라우팅합니다.
    - 헤더 값과 바인딩된 헤더 값이 일치하는 큐로 메시지를 전달합니다.

## 2.4 실습

![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fe81db69b-5f92-4cbf-be27-b64a8323a863%2FUntitled.png?table=block&id=1dc212cc-9f93-48d0-ab4e-e0294eebab9a&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

### 2.4.1 RabbitMQ

- 도커를 사용하여 RabbitMQ를 설치합니다.

    ```bash
    docker run -d --name rabbitmq -p5672:5672 -p 15672:15672 --restart=unless-stopped rabbitmq:management
    ```

- localhost:15672에 접속하면 로그인 페이지가 보입니다. guest/guest를 입력하여 접속하면 대시보드를 볼 수 있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fbffd4130-ebbf-48c9-8b76-626551f8a864%2FUntitled.png?table=block&id=e436ddd8-7a3b-409e-b684-bfa016bd4500&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=770&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F4ce87c90-0fa1-4121-b5c6-43bdd7f10264%2FUntitled.png?table=block&id=b9cf4977-6990-4612-bef6-5ac0bc4a0b62&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)


### 2.4.2 Order Application

- [start.spring.io](http://start.spring.io) 에 접속하여 프로젝트를 생성합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Ff5ace620-e74e-4464-a3ae-78ea8d89076a%2FUntitled.png?table=block&id=d20297fb-9d3c-4a4e-8b74-b86f40946f0c&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- 프로젝트를 생성하면 build.gradle의 디펜던시는 아래와 같습니다.

    ```bash
    dependencies {
    	implementation 'org.springframework.boot:spring-boot-starter-amqp'
    	implementation 'org.springframework.boot:spring-boot-starter-web'
    	compileOnly 'org.projectlombok:lombok'
    	annotationProcessor 'org.projectlombok:lombok'
    	testImplementation 'org.springframework.boot:spring-boot-starter-test'
    	testImplementation 'org.springframework.amqp:spring-rabbit-test'
    	testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
    }
    ```

- application.properties

    ```bash
    spring.application.name=order
    
    message.exchange=market
    message.queue.product=market.product
    message.queue.payment=market.payment
    
    spring.rabbitmq.host=localhost
    spring.rabbitmq.port=5672
    spring.rabbitmq.username=guest
    ```

- OrderApplicationQueueConfig.java

    ```java
    import org.springframework.amqp.core.Binding;
    import org.springframework.amqp.core.BindingBuilder;
    import org.springframework.amqp.core.Queue;
    import org.springframework.amqp.core.TopicExchange;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    
    @Configuration
    public class OrderApplicationQueueConfig {
    
        @Value("${message.exchange}")
        private String exchange;
    
        @Value("${message.queue.product}")
        private String queueProduct;
    
        @Value("${message.queue.payment}")
        private String queuePayment;
    
        @Bean public TopicExchange exchange() { return new TopicExchange(exchange); }
    
        @Bean public Queue queueProduct() { return new Queue(queueProduct); }
        @Bean public Queue queuePayment() { return new Queue(queuePayment); }
    
        @Bean public Binding bindingProduct() { return BindingBuilder.bind(queueProduct()).to(exchange()).with(queueProduct); }
        @Bean public Binding bindingPayment() { return BindingBuilder.bind(queuePayment()).to(exchange()).with(queuePayment); }
    }
    ```

- OrderController.java

    ```java
    import lombok.RequiredArgsConstructor;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.PathVariable;
    import org.springframework.web.bind.annotation.RestController;
    
    @RestController
    @RequiredArgsConstructor
    public class OrderController {
    
        private final OrderService orderService;
    
        @GetMapping("/order/{id}")
        public String order(@PathVariable String id) {
            orderService.createOrder(id);
            return "Order complete";
        }
    }
    ```


- OrderService.java

    ```java
    import lombok.RequiredArgsConstructor;
    import org.springframework.amqp.rabbit.core.RabbitTemplate;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.stereotype.Service;
    
    @Service
    @RequiredArgsConstructor
    public class OrderService {
    
        @Value("${message.queue.product}")
        private String productQueue;
    
        @Value("${message.queue.payment}")
        private String paymentQueue;
    
        private final RabbitTemplate rabbitTemplate;
    
        public void createOrder(String orderId) {
            rabbitTemplate.convertAndSend(productQueue, orderId);
            rabbitTemplate.convertAndSend(paymentQueue, orderId);
        }
    
    }
    ```

- 애플리케이션을 런하여 /order/1 로 요청을 보냅니다. 그후 http://localhost:15672로 접속하여 RabbitMQ의 Exchange와 Queue 를 확인 할 수 있습니다.
  또한 Queue 에서는 현재 발행된 메시지가 Total 에 쌓여 있는것을 확인 할 수 있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fbeb7811d-38ee-4bf5-8cfd-b94ba7a0f69b%2FUntitled.png?table=block&id=85069e84-eacb-4b9d-a454-ab9ae64d8fb7&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=790&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F9cac14a9-ce52-4de3-b737-0f7dafe1e429%2FUntitled.png?table=block&id=e4bcdcae-d9c0-4722-b39e-d3f20d2b78d4&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F01be2cea-00d7-4817-929d-699fb5ba4865%2FUntitled.png?table=block&id=16b88cf0-7cc7-4c96-978e-5f6f337f99ca&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)


- Queue and Stream 페이지에서 큐 이름을 클릭하여 상세페이지로 이동한 수 스크롤하여 Get Messages섹션으로 가서 Get Message를 클릭하면 현재 큐에 쌓여있는 메시지를 조회 할 수 있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F7a72ca46-2089-4b33-8826-8b8151a29a0c%2FUntitled.png?table=block&id=d33fb1f8-766c-4548-b0d6-6b70d7a25093&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1270&userId=&cache=v2)


### 2.4.3 Payment Application

- [start.spring.io](http://start.spring.io) 에 접속하여 프로젝트를 생성합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fefa6b44d-9ba3-4108-a252-0d4a8a15e267%2FUntitled.png?table=block&id=45634dfe-32bb-4488-b463-2941a02b8e3b&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

- 프로젝트를 생성하면 build.gradle의 디펜던시는 아래와 같습니다.

    ```bash
    dependencies {
    	implementation 'org.springframework.boot:spring-boot-starter-amqp'
    	compileOnly 'org.projectlombok:lombok'
    	annotationProcessor 'org.projectlombok:lombok'
    	testImplementation 'org.springframework.boot:spring-boot-starter-test'
    	testImplementation 'org.springframework.amqp:spring-rabbit-test'
    	testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
    }
    ```

- application.properties

    ```bash
    spring.application.name=payment
    
    message.queue.payment=market.payment
    
    spring.rabbitmq.host=localhost
    spring.rabbitmq.port=5672
    spring.rabbitmq.username=guest
    spring.rabbitmq.password=guest
    
    ```

- PaymentEndpoint.java

    ```java
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.amqp.rabbit.annotation.RabbitListener;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.stereotype.Component;
    
    @Slf4j
    @Component
    public class PaymentEndpoint {
    
        @Value("${spring.application.name}")
        private String appName;
    
        @RabbitListener(queues = "${message.queue.payment}")
        public void receiveMessage(String orderId) {
            log.info("receive orderId:{}, appName : {}", orderId, appName);
        }
    }
    ```


- 애플리케이션을 실행하면 receiveMessage 의 로그가 찍히는것을 확인 할 수 있습니다. Order 프로젝트에서 발행한 메시지가 Payment Consumer가 실행되자마자 소모 된 것입니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F480a66c4-b624-4316-af7d-cc5d135d6d43%2FUntitled.png?table=block&id=d6804a69-fe41-49d3-9a2d-b35499e61236&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F8ba47dc4-4cf1-4dc5-ac49-ea0b263ec1ba%2FUntitled.png?table=block&id=2f532aa3-6626-4ecc-b441-1ac9180b6ed1&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)


### 2.4.3 Product Application

- [start.spring.io](http://start.spring.io) 에 접속하여 프로젝트를 생성합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fa3e0b03e-52b2-4101-95a8-6b86fa6713a9%2FUntitled.png?table=block&id=b05c103a-cd5c-4da5-950f-d4676af0c412&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)


- 프로젝트를 생성하면 build.gradle의 디펜던시는 아래와 같습니다.

    ```bash
    dependencies {
    	implementation 'org.springframework.boot:spring-boot-starter-amqp'
    	compileOnly 'org.projectlombok:lombok'
    	annotationProcessor 'org.projectlombok:lombok'
    	testImplementation 'org.springframework.boot:spring-boot-starter-test'
    	testImplementation 'org.springframework.amqp:spring-rabbit-test'
    	testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
    }
    ```

- application.properties

    ```bash
    spring.application.name=product
    
    message.queue.product=market.product
    
    spring.rabbitmq.host=localhost
    spring.rabbitmq.port=5672
    spring.rabbitmq.username=guest
    spring.rabbitmq.password=guest
    ```

- ProductEndpoint.java

    ```java
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.amqp.rabbit.annotation.RabbitListener;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.stereotype.Component;
    
    @Slf4j
    @Component
    public class ProductEndpoint {
    
        @Value("${spring.application.name}")
        private String appName;
    
        @RabbitListener(queues = "${message.queue.product}")
        public void receiveMessage(String orderId) {
            log.info("receive orderId:{}, appName : {}", orderId, appName);
        }
    }
    ```

- 컨슈머가 라운드로빈으로 메시지를 전달 받는것을 확인해가 위해서 Intellij 에서 구성편집에 들어가 두개의 Product를 생성합니다. [application.name](http://application.name) 옵션을 통해 2를 구분할 수 있도록 합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F5b201a66-f04e-41b7-93fb-14ba52abf68f%2FUntitled.png?table=block&id=11e9e729-bf73-4a2d-8c37-8972870346d5&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fcf251391-e73d-4837-84e6-b8cd0622ff46%2FUntitled.png?table=block&id=64a2f23d-e5d7-4efc-b3a5-98014ddd6764&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- order에서 요청 할때마다 Product Application이 번갈아 가면서 메시지를 수신 받는것을 확인 할 수 있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Ff82040da-800c-41a5-a4d3-80e421cad4d2%2FUntitled.png?table=block&id=b3fe432a-5393-4ae3-91d4-00ea0ddc4d99&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fa203f45f-1113-4fc4-bfe3-2f010c0002ad%2FUntitled.png?table=block&id=54572dc8-b6ed-4886-93be-95d0a3aa19bd&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)