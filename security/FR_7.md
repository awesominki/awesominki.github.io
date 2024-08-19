---
layout: page
title: 7. 메모리 릭
description: >
hide_description: true
sitemap: false
---

### 7.1 메모리 릭이란?

- 메모리 릭(Memory Leak)은 프로세스가 더 이상 필요하지 않은 메모리를 할당한 후 이를 해제하지 않음으로써, 해당 메모리가 지속적으로 점유된 상태로 남아있는 현상을 말합니다.
- 이러한 메모리 릭은 메모리 사용량을 지속적으로 증가시켜, 결국에는 시스템의 메모리를 고갈시켜 성능 저하 또는 애플리케이션의 충돌을 유발할 수 있습니다.

### 7.2 **메모리 릭의 일반적인 원인**

- **이벤트 리스너나 콜백의 등록 후 해제되지 않음**: 이벤트 리스너를 등록한 후 이를 해제하지 않으면, 해당 객체가 계속 메모리에 남아 있게 되어 메모리 릭이 발생할 수 있습니다.
- **전역 변수 사용**: 전역 변수로 객체를 참조하게 되면, 해당 객체가 프로그램의 종료 시점까지 해제되지 않을 수 있습니다.
- **캐시 관리 실패**: 캐시된 객체가 필요 없을 때도 계속해서 메모리에 유지되면 메모리 릭이 발생할 수 있습니다.
- **잘못된 데이터 구조 관리**: 예를 들어, 객체를 저장한 후 필요하지 않게 되었을 때 제거하지 않으면, 메모리 릭이 발생할 수 있습니다.

### 7.3 **스프링(Spring)에서의 메모리 릭**

- **스프링 빈의 라이프사이클 관리 문제**
    - 스프링에서는 기본적으로 싱글톤(Singleton) 스코프의 빈(Bean)을 사용합니다. 이 빈은 애플리케이션 컨텍스트가 살아있는 동안 메모리에 유지됩니다. 만약 싱글톤 빈이 내부적으로 많은 메모리를 점유하는 객체를 가지고 있다면, 이 객체들이 계속 메모리에 남아 메모리 릭을 유발할 수 있습니다.
    - 예를 들어, 캐시나 컬렉션에 데이터를 추가한 후 이를 명시적으로 제거하지 않으면, 메모리가 점점 증가할 수 있습니다.
- **@Autowired 주입된 객체의 잘못된 관리**
    - @Autowired로 주입된 객체가 예상치 못한 참조 순환을 일으키거나, 의도적으로 제거되지 않는다면 메모리 릭을 일으킬 수 있습니다. 이는 주로 개발자가 객체의 라이프사이클을 명확하게 이해하지 못했을 때 발생합니다.

- **ThreadLocal의 잘못된 사용**
    - ThreadLocal을 사용하여 스레드별로 데이터를 저장하는 경우, 스레드가 종료되면 해당 스레드와 관련된 모든 데이터를 제거해야 합니다. 그러나 이를 명시적으로 제거하지 않으면 메모리 릭이 발생할 수 있습니다.
    - 특히 스프링에서는 요청 스코프의 빈을 ThreadLocal로 처리하는 경우가 있는데, 요청이 종료될 때 이 데이터를 적절히 정리하지 않으면 메모리 릭이 발생할 수 있습니다.

- **이벤트 리스너의 잘못된 관리**
    - 스프링 애플리케이션에서 이벤트 리스너를 등록한 후, 이 리스너가 더 이상 필요하지 않은 경우 제거해야 합니다. 그렇지 않으면 리스너가 계속해서 메모리를 점유하게 되어 메모리 릭을 유발할 수 있습니다.

### 7.4 **ThreadLocal을 사용한 메모리 릭 예시**

- MemoryLeakTest (테스트에서는 에러가 발생하지 않습니다!!!)

    ```java
    
    import org.junit.jupiter.api.Test;
    import org.springframework.stereotype.Service;
    import org.springframework.test.context.junit.jupiter.SpringJUnitConfig;
    
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    
    @SpringJUnitConfig
    public class MemoryLeakTest {
        private static final ThreadLocal<String> threadLocal = new ThreadLocal<>();
    
        @Service
        public static class MemoryLeakService {
            public void process(String value) {
                threadLocal.set(value); // ThreadLocal에 값 저장
                // 일부 처리 로직
            }
    
            public void clear() {
                threadLocal.remove(); // ThreadLocal에서 값 제거
            }
        }
    
        @Test
        public void testMemoryLeak() {
            MemoryLeakService service = new MemoryLeakService();
            ExecutorService executorService = Executors.newFixedThreadPool(10);
    
            for (int i = 0; i < 100; i++) {
                int finalI = i;
                executorService.submit(() -> {
                    service.process("Test " + finalI);
                    // 여기서 threadLocal.remove()를 호출하지 않으면 메모리 릭 발생 가능
                    // service.clear(); // 이 코드를 주석 처리하여 메모리 릭 발생
                });
            }
    
            executorService.shutdown();
        }
    
    }
    
    ```