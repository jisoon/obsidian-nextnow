---
created: 2024-10-23 00:25
updated: 2024-10-23 01:21
---
# Spring Boot Admin 구성 가이드

## 목차
1. [개요](#1-개요)
2. [Admin 서버 구성](#2-admin-서버-구성)
3. [Admin 클라이언트 구성](#3-admin-클라이언트-구성)
4. [스케줄러 모니터링](#4-스케줄러-모니터링)
5. [알림 설정](#5-알림-설정)
6. [보안 설정](#6-보안-설정)
7. [운영 가이드](#7-운영-가이드)

## 1. 개요

Spring Boot Admin은 Spring Boot 애플리케이션을 모니터링하고 관리하기 위한 웹 애플리케이션입니다. 이 가이드에서는 Admin 서버 구성부터 클라이언트 연동, 알림 설정, 보안 설정까지 전반적인 구성 방법을 설명합니다.

### 주요 기능
- 애플리케이션 상태 모니터링
- 메트릭스 수집 및 시각화
- 로그 레벨 동적 변경
- 스케줄러 모니터링
- Slack 알림 통합
- 보안 설정

## 2. Admin 서버 구성

### 2.1 의존성 설정

```xml
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-server</artifactId>
    <version>3.2.1</version>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

### 2.2 메인 애플리케이션 클래스

```java
@EnableAdminServer
@SpringBootApplication
public class AdminServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(AdminServerApplication.class, args);
    }
}
```

### 2.3 기본 설정 (application.yml)

```yaml
spring:
  boot:
    admin:
      ui:
        title: "스프링 부트 어드민 서버"

server:
  port: 8080
```

### 2.4 SecurityConfig
```Java
@Configuration  
public class SecurityConfig {  
  
    @Bean  
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {  
        return http  
            .csrf(csrf -> csrf.disable())  
            .authorizeHttpRequests(auth -> auth  
                .requestMatchers("/**").permitAll()  
                .anyRequest().authenticated()  
            )  
            .formLogin(form -> form  
                .loginPage("/login")  
                .defaultSuccessUrl("/")  
            )  
            .httpBasic(basic -> basic.disable())  // Basic 인증 활성화  
            .build();  
    }  
  
    @Bean  
    public UserDetailsService userDetailsService() {  
        UserDetails admin = User.builder()  
            .username("admin")  
            .password(passwordEncoder().encode("admin123"))  
            .roles("ADMIN")  
            .build();  
  
        return new InMemoryUserDetailsManager(admin);  
    }  
  
    @Bean  
    public PasswordEncoder passwordEncoder() {  
        return new BCryptPasswordEncoder();  
    }  
}
```

## 3. Admin 클라이언트 구성

### 3.1 의존성 설정

```xml
<!-- https://mvnrepository.com/artifact/de.codecentric/spring-boot-admin-starter-client -->
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-client</artifactId>
    <version>3.3.4</version>
</dependency>

```

### 3.2 클라이언트 설정 (application.yml)

```yaml
spring:
  boot:
    admin:
      client:
        url: http://localhost:8080
        instance:
          name: ${spring.application.name}
        username: admin
        password: admin123
  application:
    name: 샘플-애플리케이션

management:
  endpoints:
    web:
      exposure:
        include: "*"
  endpoint:
    health:
      show-details: always
```

## 4. 스케줄러 모니터링

### 4.1 스케줄러 설정

```java
@Configuration
@EnableScheduling
public class SchedulerConfig {
    
    @Bean
    public TaskScheduler taskScheduler() {
        ThreadPoolTaskScheduler scheduler = new ThreadPoolTaskScheduler();
        scheduler.setPoolSize(10);
        scheduler.setThreadNamePrefix("scheduled-task-");
        return scheduler;
    }
}
```

### 4.2 메트릭스 설정

```java
@Configuration
public class MetricsConfig {
    
    @Bean
    public MeterBinder schedulerMetrics(TaskScheduler taskScheduler) {
        if (taskScheduler instanceof ThreadPoolTaskScheduler) {
            return new ThreadPoolTaskSchedulerMetrics(
                (ThreadPoolTaskScheduler) taskScheduler, 
                "scheduled.tasks"
            );
        }
        return null;
    }
}
```

## 5. 알림 설정

### 5.1 Slack 알림 설정

```yaml
spring:
  boot:
    admin:
      notify:
        slack:
          enabled: true
          webhook-url: https://hooks.slack.com/services/YOUR/WEBHOOK/URL
          channel: #모니터링
          icon: ':warning:'
          username: Spring Boot Admin
```

### 5.2 알림 구성 클래스

```java
@Configuration
public class NotifierConfiguration {
    
    @Bean
    @ConditionalOnMissingBean
    public SlackNotifier slackNotifier(InstanceRepository repository, 
                                     NotifierProperties properties) {
        return new SlackNotifier(repository) {
            @Override
            protected MessageBuilder createMessage(InstanceEvent event) {
                MessageBuilder message = super.createMessage(event);
                customizeMessage(message, event);
                return message;
            }
        };
    }
}
```

## 6. 운영 가이드

### 6.1 모니터링 지표
- CPU 사용량
- 메모리 사용량
- 스레드 상태
- 가비지 컬렉션 통계
- HTTP 요청 통계
- 스케줄러 실행 현황

### 6.2 권장 보안 설정
1. 강력한 비밀번호 정책 적용
2. HTTPS 사용
3. 접근 IP 제한
4. 세션 타임아웃 설정
5. 실패한 로그인 시도 제한

### 6.3 운영 체크리스트
- [ ] 모든 엔드포인트 보안 설정 확인
- [ ] 알림 설정 테스트
- [ ] 메트릭스 수집 확인
- [ ] 로그 레벨 설정 확인
- [ ] 백업 설정 확인

### 6.4 문제 해결 가이드
1. 연결 문제
   - 네트워크 연결 확인
   - 방화벽 설정 확인
   - 인증 정보 확인

2. 성능 문제
   - JVM 힙 메모리 설정 확인
   - 데이터베이스 연결 풀 설정 확인
   - 스레드 풀 설정 확인

3. 보안 문제
   - SSL 인증서 유효성 확인
   - 접근 로그 분석
   - 보안 업데이트 적용

### 6.5 성능 최적화 팁
1. 적절한 메트릭스 수집 주기 설정
2. 불필요한 엔드포인트 비활성화
3. 로그 레벨 최적화
4. 캐시 설정 최적화

## 참고 자료
- [Spring Boot Admin 공식 문서](https://codecentric.github.io/spring-boot-admin/current/)
- [Spring Boot Actuator 문서](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html)
- [Spring Security 문서](https://docs.spring.io/spring-security/reference/index.html)