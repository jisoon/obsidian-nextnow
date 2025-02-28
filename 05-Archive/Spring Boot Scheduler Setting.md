---
tags: 
created: 2024-10-23 20:18
modified: 2024-10-23 20:18
related: []
updated: 2024-10-23 20:19
---
# Spring Boot Scheduler 가이드

## Index

- [1. 개요](#1.%20%EA%B0%9C%EC%9A%94)
	- [주요 기능](#%EC%A3%BC%EC%9A%94%20%EA%B8%B0%EB%8A%A5)
- [2. 기본 설정](#2.%20%EA%B8%B0%EB%B3%B8%20%EC%84%A4%EC%A0%95)
	- [2.1 의존성 추가](#2.1%20%EC%9D%98%EC%A1%B4%EC%84%B1%20%EC%B6%94%EA%B0%80)
	- [2.2 스케줄링 활성화](#2.2%20%EC%8A%A4%EC%BC%80%EC%A4%84%EB%A7%81%20%ED%99%9C%EC%84%B1%ED%99%94)
	- [2.3 기본 설정 (application.yml)](#2.3%20%EA%B8%B0%EB%B3%B8%20%EC%84%A4%EC%A0%95%20(application.yml))
- [3. 스케줄러 설정 방법](#3.%20%EC%8A%A4%EC%BC%80%EC%A4%84%EB%9F%AC%20%EC%84%A4%EC%A0%95%20%EB%B0%A9%EB%B2%95)
	- [3.1 스케줄러 설정 클래스](#3.1%20%EC%8A%A4%EC%BC%80%EC%A4%84%EB%9F%AC%20%EC%84%A4%EC%A0%95%20%ED%81%B4%EB%9E%98%EC%8A%A4)
	- [3.2 스케줄링 어노테이션 옵션](#3.2%20%EC%8A%A4%EC%BC%80%EC%A4%84%EB%A7%81%20%EC%96%B4%EB%85%B8%ED%85%8C%EC%9D%B4%EC%85%98%20%EC%98%B5%EC%85%98)
- [4. Cron 표현식 가이드](#4.%20Cron%20%ED%91%9C%ED%98%84%EC%8B%9D%20%EA%B0%80%EC%9D%B4%EB%93%9C)
	- [4.1 기본 구조](#4.1%20%EA%B8%B0%EB%B3%B8%20%EA%B5%AC%EC%A1%B0)
	- [4.2 특수문자](#4.2%20%ED%8A%B9%EC%88%98%EB%AC%B8%EC%9E%90)
	- [4.3 자주 사용되는 예시](#4.3%20%EC%9E%90%EC%A3%BC%20%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94%20%EC%98%88%EC%8B%9C)
- [5. 실전 예제](#5.%20%EC%8B%A4%EC%A0%84%20%EC%98%88%EC%A0%9C)
	- [5.1 기본 스케줄링 작업](#5.1%20%EA%B8%B0%EB%B3%B8%20%EC%8A%A4%EC%BC%80%EC%A4%84%EB%A7%81%20%EC%9E%91%EC%97%85)
	- [5.2 동적 스케줄러](#5.2%20%EB%8F%99%EC%A0%81%20%EC%8A%A4%EC%BC%80%EC%A4%84%EB%9F%AC)
- [6. 모니터링](#6.%20%EB%AA%A8%EB%8B%88%ED%84%B0%EB%A7%81)
	- [6.1 Spring Boot Admin 설정](#6.1%20Spring%20Boot%20Admin%20%EC%84%A4%EC%A0%95)
	- [6.2 메트릭 수집](#6.2%20%EB%A9%94%ED%8A%B8%EB%A6%AD%20%EC%88%98%EC%A7%91)
- [7. 운영 시 주의사항](#7.%20%EC%9A%B4%EC%98%81%20%EC%8B%9C%20%EC%A3%BC%EC%9D%98%EC%82%AC%ED%95%AD)
	- [7.1 스레드 풀 관리](#7.1%20%EC%8A%A4%EB%A0%88%EB%93%9C%20%ED%92%80%20%EA%B4%80%EB%A6%AC)
	- [7.2 예외 처리](#7.2%20%EC%98%88%EC%99%B8%20%EC%B2%98%EB%A6%AC)
	- [7.3 모범 사례](#7.3%20%EB%AA%A8%EB%B2%94%20%EC%82%AC%EB%A1%80)
	- [7.4 성능 최적화](#7.4%20%EC%84%B1%EB%8A%A5%20%EC%B5%9C%EC%A0%81%ED%99%94)
- [참고 문서](#%EC%B0%B8%EA%B3%A0%20%EB%AC%B8%EC%84%9C)


## 1. 개요
Spring Boot Scheduler는 애플리케이션에서 주기적인 작업을 실행할 수 있게 해주는 기능입니다. 
배치 작업, 정기적인 데이터 처리, 알림 발송 등 다양한 용도로 활용될 수 있습니다.

### 주요 기능
- 고정 주기 실행
- Cron 기반 실행
- 동적 스케줄링
- 에러 처리
- 멀티스레드 지원

## 2. 기본 설정

### 2.1 의존성 추가
```gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter'
}
```

### 2.2 스케줄링 활성화
```java
@EnableScheduling
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

### 2.3 기본 설정 (application.yml)
```yaml
spring:
  task:
    scheduling:
      pool:
        size: 5
      thread-name-prefix: scheduled-task-
      shutdown:
        await-termination: true
        await-termination-period: 60s
```

## 3. 스케줄러 설정 방법

### 3.1 스케줄러 설정 클래스
```java
@Configuration
@EnableScheduling
public class SchedulerConfig implements SchedulingConfigurer {
    
    @Value("${scheduler.pool-size:10}")
    private int poolSize;
    
    @Override
    public void configureTasks(ScheduledTaskRegistrar taskRegistrar) {
        ThreadPoolTaskScheduler scheduler = new ThreadPoolTaskScheduler();
        scheduler.setPoolSize(poolSize);
        scheduler.setThreadNamePrefix("custom-scheduler-");
        scheduler.initialize();
        
        taskRegistrar.setTaskScheduler(scheduler);
    }
}
```

### 3.2 스케줄링 어노테이션 옵션
- `@Scheduled(fixedRate = 5000)`: 이전 작업 시작부터 5초 간격
- `@Scheduled(fixedDelay = 5000)`: 이전 작업 종료 후 5초 후
- `@Scheduled(initialDelay = 5000)`: 초기 지연시간 설정
- `@Scheduled(cron = "0 0 9 * * ?")`: Cron 표현식 사용

## 4. Cron 표현식 가이드

### 4.1 기본 구조
```
초 분 시 일 월 요일
```

### 4.2 특수문자
- `*`: 모든 값
- `?`: 특정 값 없음
- `-`: 범위
- `,`: 여러 값
- `/`: 증분
- `L`: 마지막
- `W`: 가장 가까운 평일

### 4.3 자주 사용되는 예시
```
"0 0 * * * *"    // 매 시 정각
"0 0 9 * * *"    // 매일 오전 9시
"0 0 9 * * MON"  // 매주 월요일 오전 9시
"0 0 9 1 * *"    // 매월 1일 오전 9시
"0 0/30 * * * *" // 30분 간격
```

## 5. 실전 예제

### 5.1 기본 스케줄링 작업
```java
@Component
@Slf4j
public class ScheduledTasks {
    
    @Scheduled(fixedRate = 5000)
    public void reportCurrentTime() {
        log.info("현재 시간: {}", LocalDateTime.now());
    }
    
    @Scheduled(cron = "0 0 9 * * ?")
    public void dailyTask() {
        log.info("일일 작업 실행");
    }
}
```

### 5.2 동적 스케줄러
```java
@Component
@Slf4j
public class DynamicScheduler {
    
    private final ScheduledTaskRegistrar taskRegistrar;
    private final Map<String, ScheduledFuture<?>> scheduledTasks = new ConcurrentHashMap<>();
    
    public void addCronTask(String taskId, Runnable task, String cronExpression) {
        ScheduledFuture<?> scheduledTask = taskRegistrar.getScheduler().schedule(
            task,
            new CronTrigger(cronExpression)
        );
        scheduledTasks.put(taskId, scheduledTask);
    }
    
    public void cancelTask(String taskId) {
        ScheduledFuture<?> scheduledTask = scheduledTasks.get(taskId);
        if (scheduledTask != null) {
            scheduledTask.cancel(false);
            scheduledTasks.remove(taskId);
        }
    }
}
```

## 6. 모니터링

### 6.1 Spring Boot Admin 설정
```yaml
spring:
  boot:
    admin:
      client:
        url: http://localhost:8080
management:
  endpoints:
    web:
      exposure:
        include: "*"
```

### 6.2 메트릭 수집
```java
@Scheduled(fixedRate = 5000)
@Timed("scheduler.task.time")
public void monitoredTask() {
    // 작업 내용
}
```

## 7. 운영 시 주의사항

### 7.1 스레드 풀 관리
- 동시 실행되는 작업 수를 고려하여 풀 크기 설정
- 장시간 실행 작업은 별도 스레드 풀 구성 검토

### 7.2 예외 처리
```java
@Bean
public ErrorHandler schedulerErrorHandler() {
    return throwable -> log.error("스케줄러 오류: ", throwable);
}
```

### 7.3 모범 사례
1. 중요 작업은 `fixedDelay` 사용하여 작업 간 간격 보장
2. 시간에 민감한 작업은 `fixedRate` 사용
3. 운영 환경별 설정 분리
4. 적절한 로깅 설정
5. 모니터링 구축

### 7.4 성능 최적화
- 작업 실행 시간 모니터링
- 메모리 사용량 관리
- 리소스 사용량 모니터링
- 중복 실행 방지

## 참고 문서
- [Spring Boot 공식 문서](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.task-execution-and-scheduling)
- [Spring Framework 스케줄링 문서](https://docs.spring.io/spring-framework/docs/current/reference/html/integration.html#scheduling)
