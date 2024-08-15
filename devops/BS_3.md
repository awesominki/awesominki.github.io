---
layout: page
title: 3. Kafka
description: >
hide_description: true
sitemap: false
---

## **3.1 Kafka란**

- **Kafka**는 분산 스트리밍 플랫폼으로, 주로 실시간 데이터 피드의 빅 데이터 처리를 목적으로 사용됩니다.
- Kafka는 메시지 큐와 유사하지만, 대용량 데이터 스트림을 저장하고 실시간으로 분석하거나 처리하는 데 중점을 둡니다.

### **3.1.1 Kafka의 역할**

- **실시간 데이터 처리**: 대용량 데이터를 실시간으로 처리하고 분석합니다.
- **데이터 통합**: 다양한 소스에서 데이터를 수집하고 이를 통합하여 분석합니다.
- **내결함성**: 데이터 손실 없이 안정적으로 데이터를 저장하고 전송합니다.

### **3.1.2 장단점**

- 장점
    - **신뢰성(Reliability)**
        - **데이터 복제**: 데이터를 여러 브로커에 복제하여 저장하므로, 단일 브로커 장애 시에도 데이터 손실을 방지할 수 있습니다.
        - **확인 메커니즘**: 데이터가 소비자에게 성공적으로 전달되었는지 확인하는 기능을 제공합니다.
    - **유연성(Flexibility)**
        - **다양한 소비자 패턴**: 여러 소비자가 동시에 데이터를 구독할 수 있습니다.
        - **프로토콜 지원**: 기본적으로 Kafka의 프로토콜을 사용하지만, 다양한 클라이언트를 통해 다른 언어에서도 사용할 수 있습니다.
    - **확장성(Scalability)**
        - **분산 시스템**: 클러스터링을 통해 여러 노드에서 데이터를 분산 처리할 수 있습니다.
        - **수평 확장**: 브로커와 파티션을 추가하여 쉽게 확장할 수 있습니다.
    - **성능(Performance)**
        - **높은 처리량**: 대용량 데이터를 실시간으로 빠르게 처리할 수 있습니다.
        - **저지연**: 데이터 전송의 지연을 최소화하여 실시간 처리가 가능합니다.
    - **관리 및 모니터링(Manageability and Monitoring)**
        - **관리 도구**: 다양한 관리 도구를 통해 클러스터를 모니터링하고 관리할 수 있습니다.
        - **플러그인 시스템**: 다양한 플러그인을 통해 기능을 확장할 수 있습니다.
- 단점
    - **설정 및 운영 복잡성(Setup and Operational Complexity)**
        - **복잡한 설정**: 초기 설정이 다소 복잡할 수 있으며, 클러스터링 및 분산 환경에서는 더 많은 설정이 필요합니다.
        - **운영 관리**: 대규모 환경에서 운영하고 관리하는 데 추가적인 노력이 필요할 수 있습니다.
    - **성능 문제(Performance Issues)**
        - **브로커 오버헤드**: 높은 트래픽 상황에서는 브로커의 오버헤드가 발생할 수 있습니다.
        - **대규모 메시지 처리**: 매우 대규모의 메시지를 처리할 때 성능 저하가 발생할 수 있으며, 이러한 경우 적절한 클러스터링 및 최적화가 필요합니다.
    - **운영 비용(Operational Costs)**
        - **리소스 소비**: 메모리와 CPU 자원을 많이 소비할 수 있어, 충분한 리소스를 제공해야 원활하게 운영될 수 있습니다.
        - **모니터링 및 유지보수**: 지속적인 모니터링과 유지보수가 필요하며, 이를 위해 추가적인 인력과 비용이 발생할 수 있습니다.
    - **러닝 커브(Learning Curve)**
        - **학습 필요성**: 개념과 설정을 이해하는 데 시간이 걸릴 수 있으며 다소 난이도가 있습니다.

## **3.2 Kafka의 기본 구성 요소**

### **3.2.1 메시지(Message)**

- 메시지는 Kafka를 통해 전달되는 데이터 단위입니다. 예를 들어, 로그 데이터나 이벤트 데이터가 메시지가 될 수 있습니다.
- 메시지는 키(key), 값(value), 타임스탬프(timestamp), 그리고 몇 가지 메타데이터로 구성됩니다.

### **3.2.2 프로듀서(Producer)**

- 메시지를 생성하고 Kafka에 보내는 역할을 합니다. 예를 들어, 웹 애플리케이션이 로그 데이터를 Kafka에 보내는 경우 프로듀서가 됩니다.
- 프로듀서는 특정 토픽(topic)에 메시지를 보냅니다.

### **3.2.3 토픽(Topic)**

- 메시지를 저장하는 장소입니다. 메시지는 토픽에 저장되었다가 소비자에게 전달됩니다.
- 토픽은 여러 파티션(partition)으로 나누어질 수 있으며, 파티션은 메시지를 순서대로 저장합니다. 파티션을 통해 병렬 처리가 가능합니다.
- 예: “user-activity”라는 토픽에 사용자의 활동 로그를 저장할 수 있습니다.

### **3.2.4 파티션(Partition)**

- 파티션은 토픽을 물리적으로 나눈 단위로, 각 파티션은 독립적으로 메시지를 저장하고 관리합니다.
- 각 파티션은 메시지를 순서대로 저장하며, 파티션 내의 메시지는 고유한 오프셋(offset)으로 식별됩니다.
- 파티션을 통해 데이터를 병렬로 처리할 수 있으며, 클러스터 내의 여러 브로커에 분산시켜 저장할 수 있습니다.

### **3.2.5 키(Key)**

- 키는 메시지를 특정 파티션에 할당하는 데 사용되는 값입니다.
- 동일한 키를 가진 메시지는 항상 동일한 파티션에 저장됩니다.
- 예를 들어, 특정 사용자 ID를 키로 사용하여 해당 사용자의 모든 이벤트가 동일한 파티션에 저장되도록 할 수 있습니다.

### **3.2.5 컨슈머(Consumer)**

- 토픽에서 메시지를 가져와 처리하는 역할을 합니다.
- 컨슈머는 특정 컨슈머 그룹(consumer group)에 속하며, 같은 그룹에 속한 컨슈머들은 토픽의 파티션을 분산 처리합니다.
- 기본적으로 컨슈머는 스티키 파티셔닝(Sticky Partitioning)을 사용합니다. 이는 특정 컨슈머가 특정 파티션에 붙어서 계속해서 데이터를 처리하는 방식으로, 이는 데이터 지역성을 높여 캐시 히트율을 증가시키고 전반적인 처리 성능을 향상시킵니다.

### **3.2.6 브로커(Broker)**

- Kafka 클러스터의 각 서버를 의미하며, 메시지를 저장하고 전송하는 역할을 합니다.
- 하나의 Kafka 클러스터는 여러 브로커로 구성될 수 있으며, 각 브로커는 하나 이상의 토픽 파티션을 관리합니다.

### **3.2.7 주키퍼(Zookeeper)**

- Kafka 클러스터를 관리하고 조정하는 데 사용되는 분산 코디네이션 서비스입니다.
- 주키퍼는 브로커의 메타데이터를 저장하고, 브로커 간의 상호작용을 조정합니다.

## **3.3 Kafka와 RabbitMQ의 차이점**

### **3.3.1 설계 철학**

- **RabbitMQ**: 전통적인 메시지 브로커로, 메시지의 안정적 전달과 큐잉에 중점을 둡니다.
- **Kafka**: 분산 스트리밍 플랫폼으로, 대규모 실시간 데이터 스트림의 저장과 분석에 중점을 둡니다.

### **3.3.2 메시지 모델**

- **RabbitMQ**: 큐(queue)를 중심으로 메시지를 전달합니다. 메시지는 큐에 저장되고, 큐에서 하나 이상의 컨슈머에게 전달됩니다.
- **Kafka**: 토픽(topic)을 중심으로 메시지를 저장합니다. 메시지는 토픽의 파티션에 저장되고, 컨슈머는 이 파티션에서 메시지를 읽습니다.

### **3.3.3 메시지 지속성**

- **RabbitMQ**: 메시지를 메모리나 디스크에 저장할 수 있으며, 일반적으로 단기 저장을 목표로 합니다.
- **Kafka**: 메시지를 디스크에 저장하며, 장기 저장을 목표로 합니다. 데이터 로그는 설정된 기간 동안 보존됩니다.

### 3.3.4 사용

- **RabbitMQ**: 작업 큐, 요청/응답 패턴, 비동기 작업 처리 등 전통적인 메시지 큐 사용 사례에 적합합니다.
- **Kafka**: 실시간 데이터 스트리밍, 로그 수집 및 분석, 이벤트 소싱 등 대규모 데이터 스트림 처리에 적합합니다.

## 3.4 실습

### 3.4.1 Kafka 설치

- 도커 컴포즈를 사용하여 kafka 컨테이너를 생성합니다. docker-compose.yml 파일을 생성합니다.

    ```bash
    version: '3.8'
    services:
      zookeeper:
        image: wurstmeister/zookeeper:3.4.6
        platform: linux/amd64
        ports:
          - "2181:2181"
        environment:
          ZOOKEEPER_CLIENT_PORT: 2181
          ZOOKEEPER_TICK_TIME: 2000
    
      kafka:
        image: wurstmeister/kafka:latest
        platform: linux/amd64
        ports:
          - "9092:9092"
        environment:
          KAFKA_ADVERTISED_LISTENERS: INSIDE://kafka:29092,OUTSIDE://localhost:9092
          KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: INSIDE:PLAINTEXT,OUTSIDE:PLAINTEXT
          KAFKA_LISTENERS: INSIDE://0.0.0.0:29092,OUTSIDE://0.0.0.0:9092
          KAFKA_INTER_BROKER_LISTENER_NAME: INSIDE
          KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
        volumes:
          - /var/run/docker.sock:/var/run/docker.sock
    
      kafka-ui:
        image: provectuslabs/kafka-ui:latest
        platform: linux/amd64
        ports:
          - "8080:8080"
        environment:
          KAFKA_CLUSTERS_0_NAME: local
          KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS: kafka:29092
          KAFKA_CLUSTERS_0_ZOOKEEPER: zookeeper:2181
          KAFKA_CLUSTERS_0_READONLY: "false"
    
    ```

- docker-compose.yml 파일이 있는 경로에서 도커 컴포즈를 실행합니다.
    - 버전 관련으로 에러가 발생할 경우 zookeeper의 image를 wurstmeister/zookeeper:latest로 변경

    ```bash
    docker compose up -d
    ```

- [localhost:8080](http://localhost:8080) 에 접속하면 kafka UI 에 접속할 수 있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Ff4c0a17f-12b4-45f3-8be6-7c9d979ec33a%2FUntitled.png?table=block&id=796b132a-c2dd-4e87-abea-9226f267fa6a&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)


### 3.4.2 Producer Application

- start.spring.io 에 접속하여 프로젝트를 생성합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F2bd5faf6-12b1-47fa-9246-4f0929c8afab%2FUntitled.png?table=block&id=7bc42f4c-1b36-46cd-8478-4e2141ef3423&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)


- 프로젝트를 생성하면 build.gradle의 디펜던시는 아래와 같습니다.

    ```bash
    dependencies {
    	implementation 'org.springframework.boot:spring-boot-starter-web'
    	implementation 'org.springframework.kafka:spring-kafka'
    	compileOnly 'org.projectlombok:lombok'
    	annotationProcessor 'org.projectlombok:lombok'
    	testImplementation 'org.springframework.boot:spring-boot-starter-test'
    	testImplementation 'org.springframework.kafka:spring-kafka-test'
    	testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
    }
    ```


- application.properties

    ```bash
    spring.application.name=producer
    server.port=8090
    
    spring.kafka.bootstrap-servers=localhost:9092
    spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
    spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer
    ```

- ProducerApplicationKafkaConfig.java

    ```java
    import org.apache.kafka.clients.producer.ProducerConfig;
    import org.apache.kafka.common.serialization.StringSerializer;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.kafka.core.DefaultKafkaProducerFactory;
    import org.springframework.kafka.core.KafkaTemplate;
    import org.springframework.kafka.core.ProducerFactory;
    
    import java.util.HashMap;
    import java.util.Map;
    
    @Configuration
    public class ProducerApplicationKafkaConfig {
        @Bean
        public ProducerFactory<String, String> producerFactory() {
            Map<String, Object> configProps = new HashMap<>();
            configProps.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
            configProps.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
            configProps.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
            return new DefaultKafkaProducerFactory<>(configProps);
        }
    
        @Bean
        public KafkaTemplate<String, String> kafkaTemplate() {
            return new KafkaTemplate<>(producerFactory());
        }
    }
    ```


- ProducerController.java

    ```java
    import lombok.RequiredArgsConstructor;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RequestParam;
    import org.springframework.web.bind.annotation.RestController;
    
    @RestController
    @RequiredArgsConstructor
    public class ProducerController {
    
        private final ProducerService producerService;
    
        @GetMapping("/send")
        public String sendMessage(@RequestParam("topic") String topic,
                                  @RequestParam("key") String key,
                                  @RequestParam("message") String message) {
            producerService.sendMessage(topic, key, message);
            return "Message sent to Kafka topic";
        }
    }
    ```


- ProducerService.java

    ```java
    import lombok.RequiredArgsConstructor;
    import org.springframework.kafka.core.KafkaTemplate;
    import org.springframework.stereotype.Service;
    
    @Service
    @RequiredArgsConstructor
    public class ProducerService {
    
        private final KafkaTemplate<String, String> kafkaTemplate;
    
        public void sendMessage(String topic , String key, String message) {
            for (int i = 0; i < 10; i++) {
    
                kafkaTemplate.send(topic, key, message + " " + i);
            }
    
        }
    }
    ```


### 3.4.3 Consumer Application

![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F914e5cbd-82b7-4169-ab1b-46ab51993ce8%2FUntitled.png?table=block&id=7168e0fd-4966-42fc-8cc6-5247999861f5&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- 프로젝트를 생성하면 build.gradle의 디펜던시는 아래와 같습니다.

    ```bash
    dependencies {
    	implementation 'org.springframework.boot:spring-boot-starter-web'
    	implementation 'org.springframework.kafka:spring-kafka'
    	compileOnly 'org.projectlombok:lombok'
    	annotationProcessor 'org.projectlombok:lombok'
    	testImplementation 'org.springframework.boot:spring-boot-starter-test'
    	testImplementation 'org.springframework.kafka:spring-kafka-test'
    	testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
    }
    ```


- application.properties

    ```bash
    spring.application.name=consumer
    server.port=8091
    
    spring.kafka.bootstrap-servers=localhost:9092
    spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.StringDeserializer
    spring.kafka.consumer.value-deserializer=org.apache.kafka.common.serialization.StringDeserializer
    ```

- ConsumerApplicationKafkaConfig.java

    ```java
    import org.apache.kafka.clients.consumer.ConsumerConfig;
    import org.apache.kafka.common.serialization.StringDeserializer;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.kafka.annotation.EnableKafka;
    import org.springframework.kafka.config.ConcurrentKafkaListenerContainerFactory;
    import org.springframework.kafka.core.ConsumerFactory;
    import org.springframework.kafka.core.DefaultKafkaConsumerFactory;
    
    import java.util.HashMap;
    import java.util.Map;
    
    // 이 클래스는 Kafka 컨슈머 설정을 위한 Spring 설정 클래스입니다.
    @EnableKafka // Kafka 리스너를 활성화하는 어노테이션입니다.
    @Configuration // Spring 설정 클래스로 선언하는 어노테이션입니다.
    public class ConsumerApplicationKafkaConfig {
    
        // Kafka 컨슈머 팩토리를 생성하는 빈을 정의합니다.
        // ConsumerFactory는 Kafka 컨슈머 인스턴스를 생성하는 데 사용됩니다.
        // 각 컨슈머는 이 팩토리를 통해 생성된 설정을 기반으로 작동합니다.
        @Bean
        public ConsumerFactory<String, String> consumerFactory() {
            // 컨슈머 팩토리 설정을 위한 맵을 생성합니다.
            Map<String, Object> configProps = new HashMap<>();
            // Kafka 브로커의 주소를 설정합니다.
            configProps.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
            // 메시지 키의 디시리얼라이저 클래스를 설정합니다.
            configProps.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
            // 메시지 값의 디시리얼라이저 클래스를 설정합니다.
            configProps.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
            // 설정된 프로퍼티로 DefaultKafkaConsumerFactory를 생성하여 반환합니다.
            return new DefaultKafkaConsumerFactory<>(configProps);
        }
    
        // Kafka 리스너 컨테이너 팩토리를 생성하는 빈을 정의합니다.
        // ConcurrentKafkaListenerContainerFactory는 Kafka 메시지를 비동기적으로 수신하는 리스너 컨테이너를 생성하는 데 사용됩니다.
        // 이 팩토리는 @KafkaListener 어노테이션이 붙은 메서드들을 실행할 컨테이너를 제공합니다.
        @Bean
        public ConcurrentKafkaListenerContainerFactory<String, String> kafkaListenerContainerFactory() {
            // ConcurrentKafkaListenerContainerFactory를 생성합니다.
            ConcurrentKafkaListenerContainerFactory<String, String> factory = new ConcurrentKafkaListenerContainerFactory<>();
            // 컨슈머 팩토리를 리스너 컨테이너 팩토리에 설정합니다.
            factory.setConsumerFactory(consumerFactory());
            // 설정된 리스너 컨테이너 팩토리를 반환합니다.
            return factory;
        }
    }
    ```


- ConsumerService.java

    ```java
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.kafka.annotation.KafkaListener;
    import org.springframework.stereotype.Service;
    
    @Slf4j
    @Service
    public class ConsumerService {
    
        // 이 메서드는 Kafka에서 메시지를 소비하는 리스너 메서드입니다.
        // @KafkaListener 어노테이션은 이 메서드를 Kafka 리스너로 설정합니다.
        @KafkaListener(groupId = "group_a", topics = "topic1")
        // Kafka 토픽 "test-topic"에서 메시지를 수신하면 이 메서드가 호출됩니다.
        // groupId는 컨슈머 그룹을 지정하여 동일한 그룹에 속한 다른 컨슈머와 메시지를 분배받습니다.
        public void consumeFromGroupA(String message) {
            log.info("Group A consumed message from topic1: " + message);
        }
    
        // 동일한 토픽을 다른 그룹 ID로 소비하는 또 다른 리스너 메서드입니다.
        @KafkaListener(groupId = "group_b", topics = "topic1")
        public void consumeFromGroupB(String message) {
            log.info("Group B consumed message from topic1: " + message);
        }
    
        // 다른 토픽을 다른 그룹 ID로 소비하는 리스너 메서드입니다.
        @KafkaListener(groupId = "group_c", topics = "topic2")
        public void consumeFromTopicC(String message) {
            log.info("Group C consumed message from topic2: " + message);
        }
    
        // 다른 토픽을 다른 그룹 ID로 소비하는 리스너 메서드입니다.
        @KafkaListener(groupId = "group_c", topics = "topic3")
        public void consumeFromTopicD(String message) {
            log.info("Group C consumed message from topic3: " + message);
        }
    
        @KafkaListener(groupId = "group_d", topics = "topic4")
        public void consumeFromPartition0(String message) {
            log.info("Group D consumed message from topic4: " + message);
        }
    }
    ```


### 3.4.3 확인

- 두 애플리케이션을 실행하고 Kafka  ui 를 확인합니다. Topic 탭과 Comsumers 탭을 확인합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Ff162d6f0-293f-4f54-9560-a187325d71e8%2FUntitled.png?table=block&id=b005b42b-bcd6-45ec-97dc-c2d8fd853cd3&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fde1720e1-ab32-4e7b-a7eb-56f0ba7eed16%2FUntitled.png?table=block&id=3cfd1aa4-66d1-4645-a045-a1a413905df9&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)


- topic 을 test-topic 으로 지정하고 요청해봅니다.

  kafka ui > Topics > Messages 에 접속하면 방금 요청한 토픽으로 발행된 메시지를 볼 수 있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fe2224cc9-820c-4bc8-a68a-fbc52dfcf350%2FUntitled.png?table=block&id=b5935794-8110-452a-85fa-61775da2a727&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F3c2c530c-286b-4629-8850-2805904235f3%2FUntitled.png?table=block&id=65969080-9d68-4dcc-8b43-c3a510d29fd0&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)


- topic 을 topic1 으로 지정하고 요청해봅니다.

  컨슈머 애플리케이션의 로그를 보면 GroupA 와 GroupB가 메시지를 수신한 것을 볼 수 있습니다.

  따라서 같은 토픽을 가지고 그룹이 다르면 메시지를 각 그룹마다 수신한다는 것을 알 수 있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F6b382b40-e1cc-4259-9699-54d19b22dd9b%2FUntitled.png?table=block&id=caa0800e-7057-402c-89c5-a3a7ed977fd8&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F0b0abe45-cd82-426f-827a-60d782b2d029%2FUntitled.png?table=block&id=50a80a82-9376-4dda-b5f9-4f1737e47364&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- topic 을 topic2 으로 지정하고 요청해봅니다.

  컨슈머 애플리케이션의 로그를 보면 GroupC이고  topic 이 2 인 리스너가 메시지를 수신한 것을 볼 수 있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Faad1b860-4ff1-4d75-a9d7-b07690c1e474%2FUntitled.png?table=block&id=93bff21e-ebf1-40dd-9716-a8645fd2895b&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Fbb225229-05e5-4c19-9c80-a3d5ba177c40%2FUntitled.png?table=block&id=9d062764-313d-4b4d-bf77-65abda25a22b&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)


- topic 을 topic3 으로 지정하고 요청해봅니다.

  컨슈머 애플리케이션의 로그를 보면 GroupC이고 topic 이 3 인 리스너가 메시지를 수신한 것을 볼 수 있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F0ed8e989-3921-4ddd-8e29-501f7483424f%2FUntitled.png?table=block&id=66c8edbd-7e9c-46a6-b6a8-97ff5b3db4c5&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F145d3697-243f-4e00-a69b-41ed5cd13d3b%2FUntitled.png?table=block&id=cc589a2b-4342-4a4a-ae8a-9cc9ddcf4db7&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

- topic 을 topic4 으로 지정하고 요청해봅니다.

  컨슈머 애플리케이션의 로그를 보면 GroupD이고 topic 이 4 인 리스너가 메시지를 수신한 것을 볼 수 있습니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2Ff50de932-9694-4c5d-b278-dc770daffb9e%2FUntitled.png?table=block&id=9283603f-54a7-4df7-be7c-b1d556a813a5&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1360&userId=&cache=v2)

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F0e5b7d91-d0ab-4756-976e-971150f3e1e2%2FUntitled.png?table=block&id=ff5a6850-6ae5-4e59-8e8a-0ef3f30cb6d4&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)