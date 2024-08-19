---
layout: page
title: 9. 설정 버전 관리 (Configuration Versioning)
description: >
hide_description: true
sitemap: false
---

### 9.1 설정 버전 관리란?

- 애플리케이션의 설정 데이터를 시간이나 버전별로 관리하는 방법을 의미합니다. 최신 설정 데이터를 항상 사용할 수 있도록 각 설정에 버전(또는 날짜)을 부여하고, 이를 기반으로 최신 설정을 조회하고 적용하는 방식입니다.

### 9.2 설정 버전 관리의 장점

- **빠른 롤백**
    - 설정 버전 관리를 통해 이전 버전의 설정 데이터를 보관하면, 장애가 발생했을 때 문제가 있는 최신 설정을 신속하게 이전 버전으로 롤백할 수 있습니다. 이는 서비스의 가동 중단 시간을 최소화하고, 문제를 빠르게 해결하는 데 큰 도움이 됩니다.
- **변경 이력 추적**
    - 각 설정이 버전별로 관리되기 때문에, 어떤 설정이 언제 변경되었는지 쉽게 추적할 수 있습니다. 이는 문제의 원인을 분석할 때 유용하며, 특정 설정 변경이 문제를 유발했는지 확인할 수 있습니다.
- **테스트와 배포 용이성**
    - 새로운 설정을 적용하기 전에 테스트 환경에서 해당 설정을 미리 적용해보고, 문제가 없다고 판단되면 실제 운영 환경에 적용할 수 있습니다. 이를 통해 설정 변경으로 인한 장애 발생 가능성을 줄일 수 있습니다.
- **신속한 복구**
    - 장애 발생 시 최신 설정을 빠르게 적용하거나, 문제가 된 설정을 즉시 변경할 수 있습니다. 또한, 설정의 버전 관리 덕분에 복구 작업이 체계적으로 이루어질 수 있습니다.
- **일관된 설정 관리**
    - 설정 데이터를 체계적으로 관리함으로써, 장애 발생 시에도 일관된 설정을 유지할 수 있습니다. 이로 인해 설정 오류로 인한 장애를 예방할 수 있습니다.
- **가시성 향상**
    - 설정 변경 사항이 명확하게 기록되고 관리되므로, 모든 팀원이 설정 상태를 명확히 파악할 수 있습니다. 이는 장애 대응을 위한 의사소통과 협업을 개선합니다.

### 9.3 설정 버전 관리의 단점

- **복잡성 증가**: 여러 버전의 설정을 관리하는 것이 복잡성을 초래할 수 있습니다. 특히 버전 수가 많아질수록 관리가 어려워질 수 있습니다.
- **저장 공간 부담**: 여러 버전의 설정 데이터를 저장하는 데 필요한 저장 공간이 늘어나며, 시간이 지남에 따라 상당한 용량을 차지할 수 있습니다.
- **인적 오류 가능성**: 잘못된 버전을 관리하거나 롤백할 때 실수가 발생할 수 있어 복구 과정을 복잡하게 만들 수 있습니다.

### 9.4 예시

- 설정 엔티티 생성

    ```java
    @Entity
    public class CoreSetting {
        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        private Long id;
    
        @Column(columnDefinition = "jsonb")
        private String configData;
    
        private LocalDateTime version;
    
        @CreationTimestamp
        private LocalDateTime createdAt;
    
        // Getters and Setters
    ```

- 데이터 저장 예시

    ```java
    CoreSetting coreSetting = new CoreSetting();
    coreSetting.setSettings("{\"theme\": \"dark\", \"notifications\": \"enabled\"}");
    coreSetting.setVersion(LocalDateTime.now());
    coreSettingRepository.save(coreSetting);
    ```

- 리포지토리 예시

    ```java
    public interface CoreSettingRepository extends JpaRepository<CoreSetting, Long> {
    
        @Query(value = "SELECT * FROM CoreSetting ORDER BY version DESC LIMIT 1", nativeQuery = true)
        Optional<CoreSetting> findLatestCoreSetting();
    }
    ```

- 사용

    ```java
    Optional<CoreSetting> latestCoreSetting = coreSettingRepository.findLatestCoreSetting();
    latestCoreSetting.ifPresent(setting -> {
        // 최신 설정 데이터 사용
    });
    ```