---
layout: page
title: 4. SQL Injection
description: >
hide_description: true
sitemap: false
---

### 4.1 **SQL Injection이란?**

- **SQL Injection**은 공격자가 웹 애플리케이션의 데이터베이스 쿼리에 악의적인 SQL 코드를 삽입하여 데이터베이스를 조작하거나 민감한 정보를 탈취하는 공격입니다. 이 취약점은 애플리케이션이 사용자 입력을 제대로 검증하지 않을 때 발생합니다.

### 4.2 **SQL Injection의 위험성**

- **데이터 탈취**: 공격자가 데이터베이스에서 민감한 정보를 탈취할 수 있습니다.
- **데이터 변조**: 데이터베이스의 데이터를 변경하거나 삭제할 수 있습니다.
- **권한 상승**: 공격자가 데이터베이스 관리자 권한을 얻을 수 있습니다.
- **전체 시스템 장악**: 심각한 경우 서버 전체를 장악할 수 있습니다.

### 4.3 **SQL** injection 공격 예시

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;

public class SqlInjectionExample {
    public static void main(String[] args) {
        String username = "admin'; --";
        String password = "password";
        String query = "SELECT * FROM users WHERE username = '" + username + "' AND password = '" + password + "'";

        try (Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/testdb", "root", "password");
             Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(query)) {

            if (rs.next()) {
                System.out.println("User authenticated");
            } else {
                System.out.println("Authentication failed");
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

1. 공격자는 username 필드에 admin'; --를 입력합니다.
2. SQL 쿼리는 SELECT * FROM users WHERE username = 'admin'; --' AND password = 'password'로 변경됩니다.
3. --는 SQL 주석 처리 기호로, 이후의 코드는 무시됩니다.
4. 결과적으로 쿼리는 SELECT * FROM users WHERE username = 'admin';가 되어, 공격자가 비밀번호를 입력하지 않아도 인증이 됩니다.

### 4.4 **SQL Injection 방어 기법**

- **Prepared Statements (준비된 문)**: SQL 쿼리를 미리 컴파일하여 파라미터화된 쿼리를 사용합니다.
- **Stored Procedures (저장 프로시저)**: 데이터베이스에서 미리 정의된 저장 프로시저를 호출하여 실행합니다.
- **ORM (객체 관계 매핑)**: Hibernate 같은 ORM 프레임워크를 사용하여 데이터베이스 접근을 추상화합니다.
    - 예시
        - Hibernate와 같은 ORM 프레임워크를 사용할 경우, 내부적으로 Prepared Statement를 사용하여 SQL Injection 공격을 방지합니다.

        ```java
        import org.hibernate.Session;
        import org.hibernate.SessionFactory;
        import org.hibernate.cfg.Configuration;
        
        import java.util.List;
        
        public class HibernateExample {
            public static void main(String[] args) {
                Configuration cfg = new Configuration().configure();
                SessionFactory sessionFactory = cfg.buildSessionFactory();
                Session session = sessionFactory.openSession();
        
                String username = "admin"; // 사용자 입력으로부터 받은 값
                String password = "password"; // 사용자 입력으로부터 받은 값
        
                // HQL (Hibernate Query Language) 쿼리
                String hql = "FROM User WHERE username = :username AND password = :password";
                
                List<User> users = session.createQuery(hql)
                                          .setParameter("username", username)
                                          .setParameter("password", password)
                                          .list();
        
                if (!users.isEmpty()) {
                    System.out.println("User authenticated");
                } else {
                    System.out.println("Authentication failed");
                }
        
                session.close();
                sessionFactory.close();
            }
        }
        ```

- **입력 검증 및 인코딩**: 사용자 입력을 철저히 검증하고 인코딩하여 SQL 쿼리에 직접 포함시키지 않습니다.
- **최소 권한 원칙**: 데이터베이스 사용자에게 최소한의 권한만 부여합니다.