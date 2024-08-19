---
layout: page
title: 5. DB Lock
description: >
hide_description: true
sitemap: false
---

### 5.1 DB Lock 이란?

- DB 락(Database Lock)은 데이터베이스에서 여러 트랜잭션이 동시에 같은 데이터에 접근할 때, 데이터의 무결성(일관성)을 보장하기 위해 사용되는 메커니즘입니다.
- 쉽게 말해, 한 트랜잭션이 특정 데이터에 대해 작업을 하고 있을 때 다른 트랜잭션이 그 데이터에 접근하지 못하도록 잠그는 것입니다.
- 이로써 데이터의 일관성을 유지하고, 동시에 발생할 수 있는 충돌을 방지할 수 있습니다.

### 5.2 DB Lock의 필요성

- 데이터베이스는 여러 사용자나 시스템이 동시에 데이터를 읽고 쓰는 환경에서 운영됩니다. 이런 환경에서 문제가 발생할 수 있는 대표적인 사례는 다음과 같습니다:
    - **Dirty Read (더티 리드)**: 한 트랜잭션이 데이터를 수정 중일 때 다른 트랜잭션이 그 데이터를 읽는 상황. 만약 첫 번째 트랜잭션이 롤백된다면, 두 번째 트랜잭션은 잘못된 데이터를 읽은 것이 됩니다.
        1. **트랜잭션 A**가 고객의 은행 계좌 잔액을 수정하고 1000원을 더합니다. 잔액이 5000원에서 6000원으로 변경됩니다. 하지만 트랜잭션 A는 아직 이 변경을 커밋하지 않았습니다.
        2. **트랜잭션 B**가 같은 고객의 계좌 잔액을 조회하여 6000원이라고 읽습니다. 트랜잭션 B는 이 잔액을 기반으로 다른 계산을 수행합니다.
        3. **트랜잭션 A**가 예기치 않은 오류로 인해 롤백되어, 잔액은 다시 5000원으로 되돌아갑니다.
        4. 그러나 **트랜잭션 B**는 이미 잘못된 6000원을 읽었으므로, 이로 인해 부정확한 계산이 발생할 수 있습니다.
    - **Non-repeatable Read (반복 불가능한 읽기)**: 한 트랜잭션이 데이터를 읽은 후, 다른 트랜잭션이 그 데이터를 수정하고 커밋하여 첫 번째 트랜잭션이 동일한 데이터를 다시 읽을 때 값이 달라지는 상황입니다.
        1. **트랜잭션 A**가 고객의 계좌 잔액을 읽습니다. 이때 계좌 잔액은 5000원입니다.
        2. **트랜잭션 B**가 같은 고객의 계좌 잔액을 7000원으로 수정하고 커밋합니다.
        3. **트랜잭션 A**가 동일한 고객의 계좌 잔액을 다시 읽습니다. 이번에는 잔액이 7000원으로 표시됩니다.
        4. **트랜잭션 A**는 같은 트랜잭션 내에서 동일한 데이터를 두 번 읽었지만, 그 값이 일관되지 않게 변경된 것을 확인합니다.
    - **Lost Update (업데이트 손실)**: 두 개의 트랜잭션이 동시에 같은 데이터를 수정하려고 할 때, 한 트랜잭션의 수정 내용이 다른 트랜잭션에 의해 덮어쓰여져 사라지는 상황입니다.
        1. **트랜잭션 A**가 고객의 계좌 잔액을 5000원에서 6000원으로 수정합니다. 하지만 아직 커밋하지 않았습니다.
        2. **트랜잭션 B**도 같은 고객의 계좌 잔액을 5000원에서 7000원으로 수정하고, 즉시 커밋합니다.
        3. **트랜잭션 A**가 자신의 변경 사항을 커밋합니다.
        4. 결과적으로 계좌 잔액은 6000원으로 저장됩니다. 그러나 **트랜잭션 B**가 먼저 커밋한 7000원으로의 변경은 사라져 버렸습니다. 이 상황을 업데이트 손실이라고 합니다.
- DB 락을 통해 데이터에 대한 접근을 제어하면, 위와 같은 상황에서 발생할 수 있는 데이터 무결성 문제를 예방할 수 있습니다.

### 5.3 DB 락의 종류

- **공유 락 (Shared Lock, S Lock)**
    - 공유 락은 데이터베이스에서 데이터를 읽을 때 사용됩니다. 여러 트랜잭션이 동시에 같은 데이터를 읽을 수 있지만, 공유 락이 걸린 동안에는 데이터를 수정할 수 없습니다.
    - **상황**: 고객 정보 시스템에서 여러 직원이 동시에 같은 고객의 정보를 조회할 수 있지만, 조회 중에는 그 정보를 수정할 수 없습니다.
    - **예시**:
        - **트랜잭션 A**가 고객 A의 정보를 조회합니다. 이때 고객 A의 정보에 대해 공유 락이 걸립니다.
        - **트랜잭션 B**도 동시에 고객 A의 정보를 조회합니다. 공유 락이 이미 걸려 있으므로, 트랜잭션 B는 정상적으로 고객 정보를 읽을 수 있습니다.
        - **트랜잭션 C**가 고객 A의 정보를 수정하려고 시도합니다. 하지만 공유 락 때문에 수정 작업이 차단되거나 대기 상태가 됩니다.
        - 트랜잭션 A와 B가 조회를 끝내고 공유 락이 해제된 후에야 트랜잭션 C가 고객 정보를 수정할 수 있습니다.
- **배타 락 (Exclusive Lock, X Lock)**
    - 배타 락은 데이터를 수정할 때 사용됩니다. 배타 락이 걸린 데이터는 다른 트랜잭션이 읽거나 수정할 수 없습니다. 한 트랜잭션이 배타 락을 획득하면 다른 모든 트랜잭션은 해당 데이터에 접근할 수 없습니다.
    - **상황**: 재고 관리 시스템에서 한 직원이 특정 상품의 재고를 수정하고 있을 때, 다른 직원이 그 상품의 재고를 조회하거나 수정하지 못하게 하는 상황.
    - **예시**:
        - **트랜잭션 A**가 상품 A의 재고를 100에서 150으로 수정하려고 합니다. 이때 상품 A에 대해 배타 락이 걸립니다.
        - **트랜잭션 B**가 상품 A의 재고를 조회하려고 하지만, 배타 락 때문에 대기 상태가 됩니다.
        - **트랜잭션 C**가 상품 A의 재고를 150에서 200으로 수정하려고 하지만, 마찬가지로 대기 상태가 됩니다.
        - 트랜잭션 A가 재고 수정 작업을 완료하고 커밋한 후, 락이 해제되면 트랜잭션 B와 C가 차례로 진행될 수 있습니다.
- **비관적 락 (Pessimistic Locking)**
    - 비관적 락은 데이터를 읽을 때부터 락을 걸어 다른 트랜잭션이 접근하지 못하도록 하는 방식입니다. 데이터의 충돌 가능성이 높을 때 유용합니다.
    - **상황**: 은행 시스템에서 한 사용자가 특정 계좌의 잔액을 조회하고 수정하려는 시나리오에서, 다른 사용자가 이 계좌에 접근하지 못하게 하는 방식.
    - **예시**:
        - **트랜잭션 A**가 고객 A의 계좌 잔액을 조회하고 수정하려고 합니다. 이때 비관적 락을 사용하여 잔액에 대한 락을 걸고, 다른 트랜잭션의 접근을 차단합니다.
        - **트랜잭션 B**가 같은 계좌의 잔액을 조회하려고 하지만, 비관적 락 때문에 대기 상태가 됩니다.
        - 트랜잭션 A가 계좌 잔액을 수정하고 커밋한 후, 락이 해제되면 트랜잭션 B가 잔액을 조회할 수 있습니다.
- **낙관적 락 (Optimistic Locking)**
    - 낙관적 락은 데이터를 수정하기 전까지 락을 걸지 않고, 수정 시점에만 충돌을 확인하는 방식입니다. 주로 데이터의 버전 번호를 사용하여 동시성 문제를 해결합니다.
    - **상황**: 온라인 쇼핑몰에서 여러 사용자가 동시에 동일한 상품의 정보를 수정할 수 있는 상황에서, 수정 시점에 충돌을 감지하여 해결하는 방식.
    - **예시**:
        - **트랜잭션 A**와 **트랜잭션 B**가 동시에 상품 A의 가격을 수정하려고 합니다. 상품 A의 현재 버전은 1입니다.
        - 트랜잭션 A는 상품 가격을 5만원으로 수정하고, 버전을 2로 증가시켜 저장합니다.
        - 트랜잭션 B는 상품 가격을 6만원으로 수정하려고 하지만, 저장 시점에 버전 충돌이 발생합니다. 트랜잭션 B는 버전 1이 아닌 2를 발견하므로, 예외를 발생시키거나 변경 작업을 재시도해야 합니다.

- **명명된 락 (Named Lock)**
    - **명명된 락**은 데이터베이스에서 특정 이름으로 락을 설정하여, 동시에 하나의 프로세스만 특정 리소스에 접근하도록 하는 방식입니다. 주로 특정 리소스나 작업에 대한 접근을 직관적으로 제어하기 위해 사용됩니다.
    - **상황**: PostgreSQL에서 특정 보고서를 생성하는 작업에 대해 이름 기반으로 락을 걸어, 두 명의 사용자가 동시에 같은 보고서를 생성하지 못하게 하는 상황.
    - **예시**:
        - **트랜잭션 A**가 “월간 보고서 생성”이라는 이름으로 **명명된 락**을 설정하고 보고서 생성을 시작합니다.
        - **트랜잭션 B**도 “월간 보고서 생성”을 시도하지만, 트랜잭션 A가 락을 걸고 있으므로 대기 상태가 됩니다.
        - 트랜잭션 A가 보고서 생성을 완료하고 **명명된 락**을 해제하면, 트랜잭션 B가 보고서 생성을 시작할 수 있습니다.

- **분산 락 (Distributed Lock)**
    - 분산 락은 여러 시스템이나 인스턴스에서 동시에 동일한 자원에 접근할 때, 자원의 일관성을 유지하기 위해 사용되는 락입니다. Redis와 같은 분산 시스템을 사용하여 구현됩니다.
    - **상황**: 온라인 예약 시스템에서 여러 서버 인스턴스가 동일한 좌석을 동시에 예약하지 못하도록 하는 상황.
    - **예시**:
        - **인스턴스 A**가 공연 좌석 10번에 대해 예약을 시도하고, Redis를 사용하여 분산 락을 설정합니다.
        - **인스턴스 B**도 동일한 좌석 10번을 예약하려고 시도하지만, 인스턴스 A가 락을 걸고 있으므로 예약이 실패하거나 대기 상태가 됩니다.
        - 인스턴스 A가 예약을 완료하고 락을 해제하면, 인스턴스 B는 다음 예약을 시도할 수 있습니다.

### 5.4 실습전 프로젝트 생성

- [start.spring.io](http://start.spring.io) 에서 프로젝트를 생성합니다.

  ![Untitled](https://teamsparta.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F83c75a39-3aba-4ba4-a792-7aefe4b07895%2F6fc08f11-2393-44d6-9a2b-6865a10a7f59%2FUntitled.png?table=block&id=c9d1c1d4-6888-4f50-82f3-b3c98e40ad46&spaceId=83c75a39-3aba-4ba4-a792-7aefe4b07895&width=2000&userId=&cache=v2)

- sql 로깅을 위해 아래와 같이 application.propertise

    ```yaml
    spring.application.name=locking
    
    spring.jpa.properties.hibernate.format_sql=true
    spring.jpa.properties.hibernate.use_sql_comment=true
    
    logging.level.org.hibernate.SQL=debug
    logging.level.org.hibernate.orm.jdbc.bind=TRACE
    ```


### 5.5 **비관적 락** 실습

- **비관적 락의 동작 방식**
    - **락의 개념**: 비관적 락은 데이터에 대한 접근을 제어하기 위해 사용됩니다. 데이터베이스에서 특정 행(row)이나 테이블에 대해 락을 걸어, 다른 트랜잭션이 동시에 동일한 데이터에 접근하거나 수정하지 못하도록 합니다.
    - **락의 종류**:
        - **PESSIMISTIC_READ**: 읽기 락(Shared Lock)을 설정하여 다른 트랜잭션이 해당 데이터를 읽을 수는 있지만, 수정은 할 수 없도록 합니다.
        - **PESSIMISTIC_WRITE**: 쓰기 락(Exclusive Lock)을 설정하여 다른 트랜잭션이 해당 데이터를 읽거나 수정하지 못하도록 합니다.
    - **DB 레벨에서의 락 동작**:
        - **락 설정**: 비관적 락을 사용하면 SQL 쿼리나 트랜잭션이 데이터베이스에 접근할 때 락이 설정됩니다. 예를 들어, PESSIMISTIC_WRITE 락을 설정하면, 해당 데이터에 대한 모든 읽기 및 쓰기 작업이 락이 해제될 때까지 대기하게 됩니다.
        - **락 해제**: 락은 일반적으로 트랜잭션이 종료되거나 커밋될 때 해제됩니다. 트랜잭션이 커밋되면 락이 해제되어 다른 트랜잭션이 해당 데이터에 접근할 수 있게 됩니다. 트랜잭션이 롤백되는 경우에도 락이 해제됩니다.
- 비관적 락은 주로 데이터베이스 레벨에서 동작하며, 데이터의 무결성을 보장하는 데 매우 유용합니다.
- 그러나 성능에 영향을 미칠 수 있으므로, 데이터 충돌 가능성이 높은 환경에서 신중하게 사용해야 합니다.
- 스프링 부트와 같은 애플리케이션에서 비관적 락을 설정하면, 데이터베이스가 이 락을 처리하고 관리하게 됩니다.

- 실습
    - Item.java

        ```java
        import jakarta.persistence.Entity;
        import jakarta.persistence.GeneratedValue;
        import jakarta.persistence.GenerationType;
        import jakarta.persistence.Id;
        import lombok.Data;
        
        @Data
        @Entity
        public class Item {
            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;
        
            private String name;
            private Integer quantity;
        }
        ```

    - ItemRepository.java

        ```java
        import jakarta.persistence.LockModeType;
        import org.springframework.data.jpa.repository.JpaRepository;
        import org.springframework.data.jpa.repository.Lock;
        import org.springframework.data.jpa.repository.Query;
        
        public interface ItemRepository extends JpaRepository<Item, Long> {
            @Lock(LockModeType.PESSIMISTIC_WRITE)  // 비관적 락 적용
            @Query("select i from Item i where i.id = :id")
            Item findByIdWithLock(Long id);
        }
        
        ```

    - ItemService.java

        ```java
        import lombok.RequiredArgsConstructor;
        import org.springframework.stereotype.Service;
        import org.springframework.transaction.annotation.Transactional;
        
        @Service
        @RequiredArgsConstructor
        public class ItemService {
        
            private final ItemRepository itemRepository;
        
            @Transactional
            public void updateItemQuantity(Long itemId, Integer newQuantity) {
                // 비관적 락을 사용하여 데이터를 조회합니다.
                Item item = itemRepository.findByIdWithLock(itemId);
        
                // 재고 수량을 수정합니다.
                item.setQuantity(newQuantity);
        
                // 수정된 데이터를 저장합니다.
                itemRepository.save(item);
            }
        
            @Transactional
            public Item findItemById(Long itemId) {
                // 비관적 락 없이 데이터를 조회합니다.
                return itemRepository.findById(itemId).orElse(null);
            }
        }
        
        ```

    - ItemServiceTest.java

        ```java
        import org.junit.jupiter.api.Test;
        import org.slf4j.Logger;
        import org.slf4j.LoggerFactory;
        import org.springframework.beans.factory.annotation.Autowired;
        import org.springframework.boot.test.context.SpringBootTest;
        
        @SpringBootTest
        public class ItemServiceTest {
        
            private static final Logger logger = LoggerFactory.getLogger(ItemServiceTest.class);
        
            @Autowired
            private ItemService itemService;
        
            @Autowired
            private ItemRepository itemRepository;
        
            @Test
            public void testPessimisticLocking() throws InterruptedException {
                // 초기 데이터 설정
                logger.info("초기 아이템 데이터를 설정합니다.");
                Item item = new Item();
                item.setName("Item 1");
                item.setQuantity(10);
                itemRepository.save(item);
        
                // 첫 번째 트랜잭션: 아이템 수량을 20으로 업데이트
                Thread thread1 = new Thread(() -> {
                    logger.info("스레드 1: 아이템 수량 업데이트를 시도합니다.");
                    itemService.updateItemQuantity(item.getId(), 20);
                    logger.info("스레드 1: 아이템 수량 업데이트 완료.");
                });
        
                // 두 번째 트랜잭션: 아이템 수량을 30으로 업데이트
                Thread thread2 = new Thread(() -> {
                    logger.info("스레드 2: 아이템 수량 업데이트를 시도합니다.");
                    itemService.updateItemQuantity(item.getId(), 30);
                    logger.info("스레드 2: 아이템 수량 업데이트 완료.");
                });
        
                // 두 스레드를 동시에 실행
                thread2.start();
                thread1.start();
        
                // 두 스레드가 종료될 때까지 대기
                thread1.join();
                thread2.join();
        
                // 최종 결과를 확인합니다.
                Item updatedItem = itemService.findItemById(item.getId());
                logger.info("최종 아이템 수량: {}", updatedItem.getQuantity());
            }
        }
        ```


### 5.6 낙관적 락 실습

- 낙관적 락의 동작방식
    - 낙관적 락(Optimistic Lock)은 트랜잭션 간의 충돌을 최소화하고 성능을 향상시키기 위해 사용되는 동시성 제어 메커니즘입니다.
    - 비관적 락이 데이터베이스 레벨에서 락을 걸어 다른 트랜잭션의 접근을 차단하는 방식이라면, 낙관적 락은 데이터베이스 락을 사용하지 않고, 대신 데이터가 변경되었는지 확인하여 충돌을 처리하는 방식입니다.
    - **버전 관리**:
        - 낙관적 락에서는 보통 version이라는 필드를 엔티티에 추가합니다. 이 필드는 해당 엔티티의 수정 횟수를 추적하는 역할을 합니다.
        - 트랜잭션이 엔티티를 읽을 때, 현재의 버전 번호가 함께 읽혀옵니다.
        - 트랜잭션이 엔티티를 수정하고 저장하려고 할 때, 현재 데이터베이스에 저장된 버전 번호와 트랜잭션이 처음 읽어온 버전 번호를 비교합니다.
    - **데이터 충돌 검출**:
        - 트랜잭션이 데이터를 저장할 때, 데이터베이스에 저장된 버전 번호가 트랜잭션이 처음 읽어온 버전 번호와 동일하다면, 데이터가 수정되지 않았다고 간주하고 업데이트를 수행합니다. 이때 버전 번호는 증가합니다.
        - 반면, 버전 번호가 다르면, 다른 트랜잭션이 데이터를 수정한 것으로 간주하고, 현재 트랜잭션을 롤백하거나 재시도하도록 합니다.

- 낙관적 락의 장단점
    - **장점**
        - **성능**: 비관적 락에 비해 성능이 뛰어납니다. 데이터베이스에서 락을 걸지 않으므로 병행 처리 성능이 향상됩니다.
        - **유연성**: 충돌이 발생했을 때, 비즈니스 로직에 따라 트랜잭션을 재시도하거나 롤백할 수 있습니다.
    - **단점**
        - **충돌 가능성**: 데이터가 자주 변경되는 경우, 충돌이 자주 발생할 수 있습니다. 이로 인해 여러 번의 재시도가 필요할 수 있습니다.
        - **복잡성**: 충돌을 처리하기 위한 로직이 추가로 필요할 수 있습니다.
- 낙관적 락은 데이터베이스 락을 최소화하면서도 데이터 일관성을 유지할 수 있는 효과적인 방법입니다.
- 특히, 데이터 충돌이 적고 병행 처리가 많은 시스템에서 유용하게 사용할 수 있습니다.
- 하지만 충돌이 발생했을 때의 처리 로직을 잘 설계해야 하며, 특정 상황에서는 비관적 락보다 복잡할 수 있습니다.

- 실습
    - Product.java

        ```java
        import jakarta.persistence.*;
        import lombok.Data;
        
        @Data
        @Entity
        public class Product {
            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;
        
            private String name;
            private Double price;
        
            @Version
            private Integer version;  // 버전 필드를 통해 낙관적 락을 구현
        }
        
        ```

    - ProductRepository.java

        ```java
        import org.springframework.data.jpa.repository.JpaRepository;
        
        public interface ProductRepository extends JpaRepository<Product, Long> {
        }
        
        ```

    - ProductService.java

        ```java
        import lombok.RequiredArgsConstructor;
        import org.springframework.orm.ObjectOptimisticLockingFailureException;
        import org.springframework.stereotype.Service;
        import org.springframework.transaction.annotation.Transactional;
        
        @Service
        @RequiredArgsConstructor
        public class ProductService {
        
            private final ProductRepository productRepository;
        
            @Transactional
            public void updateProductPrice(Long productId, Double newPrice) {
                try {
                    // 기존 데이터를 읽어옵니다.
                    Product product = productRepository.findById(productId)
                            .orElseThrow(() -> new RuntimeException("Product not found"));
        
                    // 가격을 수정합니다.
                    product.setPrice(newPrice);
        
                    // 저장 시 버전 충돌이 발생하면 예외가 발생합니다.
                    productRepository.save(product);
                } catch (ObjectOptimisticLockingFailureException e) {
                    // 낙관적 락 예외 처리
                    System.err.println("낙관적 락 충돌이 발생했습니다. 다른 트랜잭션이 먼저 데이터를 수정했습니다.");
                    throw e;
                }
            }
        }
        
        ```

    - ProductServiceTest.java

        ```java
        import org.junit.jupiter.api.Test;
        import org.springframework.beans.factory.annotation.Autowired;
        import org.springframework.boot.test.context.SpringBootTest;
        import org.springframework.orm.ObjectOptimisticLockingFailureException;
        
        import static org.junit.jupiter.api.Assertions.assertThrows;
        
        @SpringBootTest
        public class ProductServiceTest {
            @Autowired
            private ProductService productService;
        
            @Autowired
            private ProductRepository productRepository;
        
            @Test
            public void testOptimisticLocking() throws InterruptedException {
                // 초기 데이터 설정
                Product product = new Product();
                product.setName("Product 1");
                product.setPrice(100.0);
                productRepository.save(product);
        
                // 첫 번째 트랜잭션: 상품 가격을 200.0으로 업데이트
                Thread thread1 = new Thread(() -> {
                    productService.updateProductPrice(product.getId(), 200.0);
                });
        
                // 두 번째 트랜잭션: 상품 가격을 300.0으로 업데이트
                Thread thread2 = new Thread(() -> {
                    assertThrows(ObjectOptimisticLockingFailureException.class, () -> {
                        productService.updateProductPrice(product.getId(), 300.0);
                    });
                });
        
                // 두 스레드를 동시에 실행
                thread1.start();
                thread2.start();
        
                // 두 스레드가 종료될 때까지 대기
                thread1.join();
                thread2.join();
            }
        }
        
        ```


### 5.7 데드 락 실습

- 데이터베이스 환경에서 데드락은 두 개 이상의 트랜잭션이 서로가 점유하고 있는 자원을 기다리면서 영원히 대기 상태에 빠지는 상황을 의미합니다.
- 이 상황이 발생하면 해당 트랜잭션들은 더 이상 진행될 수 없고, 시스템 성능에 큰 영향을 미칠 수 있습니다.

- 예시
    - **트랜잭션 A**는 테이블 X의 일부 행을 잠금(Lock)하고, 이후 테이블 Y의 행을 잠금하려고 합니다.
    - **B**는 테이블 Y의 일부 행을 잠금하고, 이후 테이블 X의 행을 잠금하려고 합니다.
    - 이 경우, 트랜잭션 A와 트랜잭션 B는 서로 상대방이 보유한 잠금을 기다리면서 영원히 대기하게 되며, 이로 인해 데드락이 발생합니다.

- 실습
    - ItemService.java 추가

        ```java
        @Transactional(timeout = 1, isolation = Isolation.SERIALIZABLE)
            public void updateItemsQuantity(Long itemId1, Long itemId2) {
                // 첫 번째 아이템에 락을 건 후 업데이트
                Item item1 = itemRepository.findByIdWithLock(itemId1);
                item1.setQuantity(item1.getQuantity() + 10);
                itemRepository.save(item1);
        
                // 잠시 대기
                try { Thread.sleep(4000); } catch (InterruptedException e) { e.printStackTrace(); }
        
                // 두 번째 아이템에 락을 건 후 업데이트
                Item item2 = itemRepository.findByIdWithLock(itemId2);
                item2.setQuantity(item2.getQuantity() + 10);
                itemRepository.save(item2);
            }
        ```


- ItemDeadLockTest.java

    ```java
    import org.junit.jupiter.api.Test;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.test.context.SpringBootTest;
    import org.springframework.transaction.TransactionSystemException;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.atomic.AtomicReference;
    
    import static org.junit.jupiter.api.Assertions.assertThrows;
    
    @SpringBootTest
    public class ItemDeadLockTest {
        private static final Logger logger = LoggerFactory.getLogger(ItemDeadLockTest.class);
    
        @Autowired
        private ItemService itemService;
    
        @Autowired
        private ItemRepository itemRepository;
    
        @Test
        public void testDeadlock() throws InterruptedException {
            // 두 개의 아이템을 생성
            Item item1 = new Item();
            item1.setName("Item 1");
            item1.setQuantity(100);
            itemRepository.save(item1);
    
            Item item2 = new Item();
            item2.setName("Item 2");
            item2.setQuantity(200);
            itemRepository.save(item2);
    
            // 스레드 동기화를 위한 CountDownLatch 설정
            CountDownLatch latch = new CountDownLatch(1);
            AtomicReference<Exception> exceptionInThread = new AtomicReference<>();
    
            Thread thread1 = new Thread(() -> {
                try {
                    logger.info("스레드 1 시작");
                    latch.await();  // 다른 스레드가 준비될 때까지 대기
                    logger.info("스레드 1: 아이템 1 -> 아이템 2 업데이트 시도");
                    itemService.updateItemsQuantity(item1.getId(), item2.getId());
                    logger.info("스레드 1: 업데이트 완료");
                } catch (TransactionSystemException e) {
                    logger.error("스레드 1: 트랜잭션 오류 발생 - {}", e.getMessage());
                    exceptionInThread.set(e);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                } catch (Exception e) {
                    logger.error("스레드 1: 알 수 없는 오류 발생", e);
                    exceptionInThread.set(e);
                }
            });
    
            Thread thread2 = new Thread(() -> {
                try {
                    logger.info("스레드 2 시작");
                    logger.info("스레드 2: 아이템 2 -> 아이템 1 업데이트 시도");
                    itemService.updateItemsQuantity(item2.getId(), item1.getId());
                    logger.info("스레드 2: 업데이트 완료");
                } catch (TransactionSystemException e) {
                    logger.error("스레드 2: 트랜잭션 오류 발생 - {}", e.getMessage());
                    exceptionInThread.set(e);
                } catch (Exception e) {
                    logger.error("스레드 2: 알 수 없는 오류 발생", e);
                    exceptionInThread.set(e);
                }
            });
    
            // 두 스레드를 시작
            thread1.start();
            thread2.start();
    
            // 스레드가 준비되었음을 알리고 실행
            latch.countDown();
    
            // 두 스레드가 종료될 때까지 대기
            thread1.join();
            thread2.join();
    
            // 스레드 중 하나에서 TransactionSystemException이 발생했는지 확인
            assertThrows(Exception.class, () -> {
                if (exceptionInThread.get() != null) {
                    throw exceptionInThread.get();
                }
            });
        }
    }
    ```

- **SERIALIZABLE 격리 수준:**
    - SERIALIZABLE 격리 수준은 트랜잭션 간의 완벽한 격리를 보장하기 위해 트랜잭션들이 마치 순차적으로 처리되는 것처럼 보이도록 만듭니다.
    - 이 수준에서는 한 트랜잭션이 데이터를 읽거나 수정하고 있는 동안 다른 트랜잭션은 해당 데이터에 접근할 수 없습니다.
    - 두 스레드가 서로 다른 아이템에 대한 락을 시도하는 경우, 스레드 1이 아이템 1에 대한 락을 획득한 상태에서 아이템 2에 대한 락을 기다리고, 동시에 스레드 2가 아이템 2에 대한 락을 획득한 상태에서 아이템 1에 대한 락을 기다리는 상황이 발생할 수 있습니다. 이로 인해 두 스레드는 서로의 락이 해제될 때까지 무한 대기 상태에 빠지게 되고, 이것이 바로 데드락입니다.
- **타임아웃 설정:**
    - timeout = 1은 트랜잭션이 시작된 후 1초 내에 완료되지 않으면 타임아웃을 발생시킵니다.
    - 데드락 상황에서 두 트랜잭션이 서로의 락을 기다리게 되면, 1초 내에 트랜잭션이 완료되지 않으므로 트랜잭션 시스템이 타임아웃을 발생시키고, 이로 인해 트랜잭션이 실패하게 됩니다.
    - 이는 데드락이 발생하면 무한히 대기하지 않고, 지정된 시간 내에 트랜잭션이 완료되지 않을 경우 타임아웃 예외를 발생시켜 데드락 상태를 빠르게 탐지하고 해결하려는 것입니다.