---
layout: page
title: 4. SAGA Pattern
description: >
hide_description: true
sitemap: false
---

> 이번 챕터에서는 rabiitMQ를 사용하여 프로듀서와 컨슈머의 연관관계를 설정하고 만약, 예외처리로 인해 에러 사항이 발생했을 시
> rabiitMQ를 통해 RollBack 하는 실습을 진행 해 보겠습니다.

![SAGA](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F5f12a0c4-d06e-4f5e-b5c0-dea79f4ead17%2FUntitled.png?table=block&id=22aaac36-3792-4438-a728-7f3823270296&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=1420&userId=&cache=v2)

## 4.1 OrderApplication

### 4.1.1 build.gradle > dependencies

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

### 4.1.2 application.properties

```bash
spring.application.name=order

message.exchange=market
message.queue.product=market.product
message.queue.payment=market.payment

message.err.exchange=market.err
message.queue.err.order=market.err.order
message.queue.err.product=market.err.product

spring.rabbitmq.host=localhost
spring.rabbitmq.port=5672
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest

```

### 4.1.3 DeliveryMessage.java

```java
import lombok.*;

import java.util.UUID;

@Data
@Builder
@ToString
@AllArgsConstructor
@NoArgsConstructor
public class DeliveryMessage {
    private UUID orderId;
    private UUID paymentId;

    private String userId;

    private Integer productId;
    private Integer productQuantity;

    private Integer payAmount;

    private String errorType;
}

```

### 4.1.4 Order.java

```java
import lombok.Builder;
import lombok.Data;
import lombok.ToString;

import java.util.UUID;

@Builder
@Data
@ToString
public class Order {
    private UUID orderId;
    private String userId;
    private String orderStatus;
    private String errorType;

    public void cancelOrder(String receiveErrorType) {
        orderStatus = "CANCEL";
        errorType = receiveErrorType;
    }
}

```

### 4.1.5 OrderApplicationQueueConfig.java

```java

import org.springframework.amqp.core.Binding;
import org.springframework.amqp.core.BindingBuilder;
import org.springframework.amqp.core.Queue;
import org.springframework.amqp.core.TopicExchange;
import org.springframework.amqp.support.converter.Jackson2JsonMessageConverter;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class OrderApplicationQueueConfig {

    @Bean
    public Jackson2JsonMessageConverter producerJackson2MessageConverter() {
        return new Jackson2JsonMessageConverter();
    }

    @Value("${message.exchange}")
    private String exchange;

    @Value("${message.queue.product}")
    private String queueProduct;

    @Value("${message.queue.payment}")
    private String queuePayment;

    @Value("${message.err.exchange}")
    private String exchangeErr;

    @Value("${message.queue.err.order}")
    private String queueErrOrder;

    @Value("${message.queue.err.product}")
    private String queueErrProduct;

    @Bean public TopicExchange exchange() { return new TopicExchange(exchange); }

    @Bean public Queue queueProduct() { return new Queue(queueProduct); }
    @Bean public Queue queuePayment() { return new Queue(queuePayment); }

    @Bean public Binding bindingProduct() { return BindingBuilder.bind(queueProduct()).to(exchange()).with(queueProduct); }
    @Bean public Binding bindingPayment() { return BindingBuilder.bind(queuePayment()).to(exchange()).with(queuePayment); }

    @Bean public TopicExchange exchangeErr() { return new TopicExchange(exchangeErr); }

    @Bean public Queue queueErrOrder() { return new Queue(queueErrOrder); }
    @Bean public Queue queueErrProduct() { return new Queue(queueErrProduct); }

    @Bean public Binding bindingErrOrder() { return BindingBuilder.bind(queueErrOrder()).to(exchangeErr()).with(queueErrOrder); }
    @Bean public Binding bindingErrProduct() { return BindingBuilder.bind(queueErrProduct()).to(exchangeErr()).with(queueErrProduct); }
}

```

### 4.1.6 OrderEndpoint.java

```java

import lombok.Data;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.UUID;

@Slf4j
@RestController
@RequiredArgsConstructor
public class OrderEndpoint {

    private final OrderService orderService;

    private final RabbitTemplate rabbitTemplate;

    @GetMapping("order/{orderId}")
    public ResponseEntity<Order> getOrder(@PathVariable UUID orderId) {
        Order order = orderService.getOrder(orderId);
        return ResponseEntity.ok(order);
    }

    @PostMapping("/order")
    public ResponseEntity<Order> order(@RequestBody OrderRequestDto orderRequestDto) {
        Order order = orderService.createOrder(orderRequestDto);
        return ResponseEntity.ok(order);
    }

    @RabbitListener(queues = "${message.queue.err.order}")
    public void errOrder(DeliveryMessage message) {
        log.info("ERROR RECEIVE !!!");
        orderService.rollbackOrder(message);
    }

    @Data
    public static class OrderRequestDto {
        private String userId;
        private Integer productId;
        private Integer productQuantity;
        private Integer payAmount;

        public Order toOrder (){
            return Order.builder()
                    .orderId(UUID.randomUUID())
                    .userId(userId)
                    .orderStatus("RECEIPT")
                .build();

        }

        public DeliveryMessage toDeliveryMessage(UUID orderId){
            return DeliveryMessage.builder()
                    .orderId(orderId)
                    .productId(productId)
                    .productQuantity(productQuantity)
                    .payAmount(payAmount)
                .build();
        }
    }

}

```

### 4.1.7 OrderService.java

```java
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;

import java.util.HashMap;
import java.util.Map;
import java.util.UUID;

@Slf4j
@Service
@RequiredArgsConstructor
public class OrderService {

    @Value("${message.queue.product}")
    private String productQueue;

    private final RabbitTemplate rabbitTemplate;

    private Map<UUID, Order> orderStore = new HashMap<>();

    public Order createOrder(OrderEndpoint.OrderRequestDto orderRequestDto) {
        Order order = orderRequestDto.toOrder();
        DeliveryMessage deliveryMessage = orderRequestDto.toDeliveryMessage(order.getOrderId());

        orderStore.put(order.getOrderId(), order);

        log.info("send Message : {}",deliveryMessage.toString());

        rabbitTemplate.convertAndSend(productQueue, deliveryMessage);

        return order;

    }

    public void rollbackOrder(DeliveryMessage message) {
        Order order = orderStore.get(message.getOrderId());
        order.cancelOrder(message.getErrorType());
        log.info(order.toString());

    }

    public Order getOrder(UUID orderId) {
        return orderStore.get(orderId);
    }
}

```

## 4.2 ProductApplication

### 4.2.1 build.gradle > dependencies

```bash
dependencies {
	// Jackson 의존성
	implementation 'com.fasterxml.jackson.core:jackson-databind'
	implementation 'com.fasterxml.jackson.core:jackson-core'
	implementation 'com.fasterxml.jackson.core:jackson-annotations'

	implementation 'org.springframework.boot:spring-boot-starter-amqp'
	compileOnly 'org.projectlombok:lombok'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	testImplementation 'org.springframework.amqp:spring-rabbit-test'
	testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
}

```

### 4.2.2 application.properties

```bash
spring.application.name=product

message.exchange=market
message.queue.product=market.product
message.queue.payment=market.payment

message.err.exchange=market.err
message.queue.err.order=market.err.order
message.queue.err.product=market.err.product

spring.rabbitmq.host=localhost
spring.rabbitmq.port=5672
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest

```

### 4.2.3 DeliveryMessage.java

```java

import lombok.*;

import java.util.UUID;

@Data
@Builder
@ToString
@AllArgsConstructor
@NoArgsConstructor
public class DeliveryMessage {
    private UUID orderId;
    private UUID paymentId;

    private String userId;

    private Integer productId;
    private Integer productQuantity;

    private Integer payAmount;

    private String errorType;
}

```

### 4.2.4 ProductApplicationQueueConfig.java

```java
import org.springframework.amqp.support.converter.Jackson2JsonMessageConverter;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class ProductApplicationQueueConfig {
    @Bean
    public Jackson2JsonMessageConverter producerJackson2MessageConverter() {
        return new Jackson2JsonMessageConverter();
    }
}

```

### 4.2.5 ProductEndpoint.java

```java
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

@Slf4j
@Component
@RequiredArgsConstructor
public class ProductEndpoint {

    private final ProductService productService;

    @RabbitListener(queues = "${message.queue.product}")
    public void receiveMessage(DeliveryMessage deliveryMessage) {
        productService.reduceProductAmount(deliveryMessage);

        log.info("PRODUCT RECEIVE:{}", deliveryMessage.toString());
    }

    @RabbitListener(queues="${message.queue.err.product}")
    public void receiveErrorMessage(DeliveryMessage deliveryMessage) {
        log.info("ERROR RECEIVE !!!");
        productService.rollbackProduct(deliveryMessage);
    }
}

```

### 4.2.6 ProductService.java

```java
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;
import org.springframework.util.StringUtils;

@Slf4j
@Service
@RequiredArgsConstructor
public class ProductService {

    private final RabbitTemplate rabbitTemplate;

    @Value("${message.queue.payment}")
    private String paymentQueue;

    @Value("${message.queue.err.order}")
    private String orderErrorQueue;

    public void reduceProductAmount(DeliveryMessage deliveryMessage) {

        Integer productId = deliveryMessage.getProductId();
        Integer productQuantity = deliveryMessage.getProductQuantity();

        if (productId != 1 || productQuantity > 1) {
            this.rollbackProduct(deliveryMessage);
            return;
        }

        rabbitTemplate.convertAndSend(paymentQueue,deliveryMessage);
    }

    public void rollbackProduct(DeliveryMessage deliveryMessage){
        log.info("PRODUCT ROLLBACK!!!");
        if(!StringUtils.hasText(deliveryMessage.getErrorType())){
            deliveryMessage.setErrorType("PRODUCT ERROR");
        }
        rabbitTemplate.convertAndSend(orderErrorQueue, deliveryMessage);
    }
}

```

## 4.3 PaymentApplication

### 4.3.1 build.gradle > dependencies

```bash
dependencies {
	// Jackson 의존성
	implementation 'com.fasterxml.jackson.core:jackson-databind'
	implementation 'com.fasterxml.jackson.core:jackson-core'
	implementation 'com.fasterxml.jackson.core:jackson-annotations'

	implementation 'org.springframework.boot:spring-boot-starter-amqp'
	compileOnly 'org.projectlombok:lombok'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	testImplementation 'org.springframework.amqp:spring-rabbit-test'
	testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
}

```

### 4.3.2 application.properties

```bash
spring.application.name=payment

message.exchange=market
message.queue.product=market.product
message.queue.payment=market.payment

message.err.exchange=market.err
message.queue.err.order=market.err.order
message.queue.err.product=market.err.product

spring.rabbitmq.host=localhost
spring.rabbitmq.port=5672
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
```

### 4.3.3 DeliveryMessage.java

```java
import lombok.*;

import java.util.UUID;

@Data
@Builder
@ToString
@AllArgsConstructor
@NoArgsConstructor
public class DeliveryMessage {
    private UUID orderId;
    private UUID paymentId;

    private String userId;

    private Integer productId;
    private Integer productQuantity;

    private Integer payAmount;

    private String errorType;
}

```

### 4.3.4 Payment.java

```java
import lombok.Builder;
import lombok.Data;

import java.util.UUID;

@Data
@Builder
public class Payment {
    private UUID paymentId;
    private String userId;

    private String payAmount;

    private String payStatus;
}

```

### 4.3.5 PaymentApplicationQueueConfig.java

```java

import org.springframework.amqp.support.converter.Jackson2JsonMessageConverter;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class PaymentApplicationQueueConfig {
    @Bean
    public Jackson2JsonMessageConverter producerJackson2MessageConverter() {
        return new Jackson2JsonMessageConverter();
    }
}

```

### 4.3.6 PaymentEndpoint.java

```java
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

@Slf4j
@Component
@RequiredArgsConstructor
public class PaymentEndpoint {

    private final PaymentService paymentService;

    @RabbitListener(queues = "${message.queue.payment}")
    public void receiveMessage(DeliveryMessage deliveryMessage) {
        log.info("PAYMENT RECEIVE : {}", deliveryMessage.toString());
        paymentService.createPayment(deliveryMessage);

    }
}

```

### 4.3.7 PaymentService.java

```java
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;

import java.util.UUID;

@Slf4j
@Service
@RequiredArgsConstructor
public class PaymentService {

    private final RabbitTemplate rabbitTemplate;

    @Value("${message.queue.err.product}")
    private String productErrorQueue;

    public void createPayment(DeliveryMessage deliveryMessage) {
        Payment payment = Payment.builder()
                .paymentId(UUID.randomUUID())
                .userId(deliveryMessage.getUserId())
                .payStatus("SUCCESS").build();

        Integer payAmount = deliveryMessage.getPayAmount();

        if (payAmount >= 10000) {
            log.error("Payment amount exceeds limit: {}", payAmount);
            payment.setPayStatus("CANCEL");
            deliveryMessage.setErrorType("PAYMENT_LIMIT_EXCEEDED");
            this.rollbackPayment(deliveryMessage);
        }
    }

    public void rollbackPayment(DeliveryMessage deliveryMessage) {
        log.info("PAYMENT ROLLBACK !!!");
        rabbitTemplate.convertAndSend(productErrorQueue, deliveryMessage);
    }
}
```