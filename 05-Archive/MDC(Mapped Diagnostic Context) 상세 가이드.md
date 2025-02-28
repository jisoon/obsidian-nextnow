---
tags:
  - logger
  - 가이드
created: 2024-10-29 12:29
modified: 2024-10-29 12:29
related:
  - "[[Spring Boot Logback 설정 가이드]]"
  - "[[NextNow API 개발 가이드 구축]]"
updated: 2024-11-18 10:42
---

## Index

- [1. MDC란?](#1.%20MDC%EB%9E%80?)
	- [1.1 주요 특징](#1.1%20%EC%A3%BC%EC%9A%94%20%ED%8A%B9%EC%A7%95)
- [2. 기본 사용법](#2.%20%EA%B8%B0%EB%B3%B8%20%EC%82%AC%EC%9A%A9%EB%B2%95)
	- [2.1 MDC 설정과 사용](#2.1%20MDC%20%EC%84%A4%EC%A0%95%EA%B3%BC%20%EC%82%AC%EC%9A%A9)
	- [2.2 Logback 패턴 설정](#2.2%20Logback%20%ED%8C%A8%ED%84%B4%20%EC%84%A4%EC%A0%95)
- [3. 실무 활용 사례](#3.%20%EC%8B%A4%EB%AC%B4%20%ED%99%9C%EC%9A%A9%20%EC%82%AC%EB%A1%80)
	- [3.1 HTTP 요청 추적](#3.1%20HTTP%20%EC%9A%94%EC%B2%AD%20%EC%B6%94%EC%A0%81)
	- [3.2 사용자 인증 정보 로깅](#3.2%20%EC%82%AC%EC%9A%A9%EC%9E%90%20%EC%9D%B8%EC%A6%9D%20%EC%A0%95%EB%B3%B4%20%EB%A1%9C%EA%B9%85)
	- [3.3 트랜잭션 추적](#3.3%20%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98%20%EC%B6%94%EC%A0%81)
- [4. 비동기 처리에서의 MDC 사용](#4.%20%EB%B9%84%EB%8F%99%EA%B8%B0%20%EC%B2%98%EB%A6%AC%EC%97%90%EC%84%9C%EC%9D%98%20MDC%20%EC%82%AC%EC%9A%A9)
	- [4.1 @Async 메소드에서 MDC 복사](#4.1%20@Async%20%EB%A9%94%EC%86%8C%EB%93%9C%EC%97%90%EC%84%9C%20MDC%20%EB%B3%B5%EC%82%AC)
	- [4.2 CompletableFuture에서의 사용](#4.2%20CompletableFuture%EC%97%90%EC%84%9C%EC%9D%98%20%EC%82%AC%EC%9A%A9)
- [5. 주의사항 및 Best Practices](#5.%20%EC%A3%BC%EC%9D%98%EC%82%AC%ED%95%AD%20%EB%B0%8F%20Best%20Practices)
	- [5.1 메모리 누수 방지](#5.1%20%EB%A9%94%EB%AA%A8%EB%A6%AC%20%EB%88%84%EC%88%98%20%EB%B0%A9%EC%A7%80)
	- [5.2 적절한 키 관리](#5.2%20%EC%A0%81%EC%A0%88%ED%95%9C%20%ED%82%A4%20%EA%B4%80%EB%A6%AC)
	- [5.3 값 길이 제한](#5.3%20%EA%B0%92%20%EA%B8%B8%EC%9D%B4%20%EC%A0%9C%ED%95%9C)
- [6. 로그 출력 예시](#6.%20%EB%A1%9C%EA%B7%B8%20%EC%B6%9C%EB%A0%A5%20%EC%98%88%EC%8B%9C)
	- [6.1 단일 요청 추적](#6.1%20%EB%8B%A8%EC%9D%BC%20%EC%9A%94%EC%B2%AD%20%EC%B6%94%EC%A0%81)
	- [6.2 에러 상황 추적](#6.2%20%EC%97%90%EB%9F%AC%20%EC%83%81%ED%99%A9%20%EC%B6%94%EC%A0%81)
- [7. 성능 고려사항](#7.%20%EC%84%B1%EB%8A%A5%20%EA%B3%A0%EB%A0%A4%EC%82%AC%ED%95%AD)
	- [7.1 MDC 사용 최적화](#7.1%20MDC%20%EC%82%AC%EC%9A%A9%20%EC%B5%9C%EC%A0%81%ED%99%94)
	- [7.2 컨텍스트 맵 재사용](#7.2%20%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8%20%EB%A7%B5%20%EC%9E%AC%EC%82%AC%EC%9A%A9)


## 1. MDC란?
MDC는 로깅 프레임워크에서 제공하는 쓰레드 로컬 변수를 사용하여 문맥 정보를 로그에 추가하는 기능입니다.

### 1.1 주요 특징
- 쓰레드별로 독립적인 로깅 컨텍스트 제공
- key-value 형태로 데이터 저장
- 쓰레드 로컬 사용으로 쓰레드 안전성 보장
- 하나의 요청 처리 과정에서 일관된 정보 로깅 가능

## 2. 기본 사용법

### 2.1 MDC 설정과 사용
```java
import org.slf4j.MDC;

public class UserService {
    public void processUser(String userId) {
        MDC.put("userId", userId);
        try {
            log.info("Processing user data");  // 로그에 자동으로 userId 포함
            // 비즈니스 로직
        } finally {
            MDC.clear();  // 반드시 clear 해주어야 함
        }
    }
}
```

### 2.2 Logback 패턴 설정
```xml
<Pattern>
    [%d{yyyy-MM-dd HH:mm:ss.SSS}] [%-5level] [%X{userId}] [%thread] %logger - %msg%n
</Pattern>
```

## 3. 실무 활용 사례

### 3.1 HTTP 요청 추적
```java
@Component
public class MDCLoggingFilter extends OncePerRequestFilter {
    
    @Override
    protected void doFilterInternal(HttpServletRequest request, 
                                  HttpServletResponse response, 
                                  FilterChain filterChain) throws ServletException, IOException {
        String requestId = UUID.randomUUID().toString();
        String userAgent = request.getHeader("User-Agent");
        String requestURI = request.getRequestURI();
        
        MDC.put("requestId", requestId);
        MDC.put("userAgent", userAgent);
        MDC.put("requestURI", requestURI);
        MDC.put("clientIP", getClientIP(request));
        
        try {
            filterChain.doFilter(request, response);
        } finally {
            MDC.clear();  // 매우 중요!
        }
    }
    
    private String getClientIP(HttpServletRequest request) {
        String ip = request.getHeader("X-Forwarded-For");
        if (ip == null) {
            ip = request.getRemoteAddr();
        }
        return ip;
    }
}
```

### 3.2 사용자 인증 정보 로깅
```java
@Component
public class SecurityLoggingInterceptor implements HandlerInterceptor {
    
    @Override
    public boolean preHandle(HttpServletRequest request, 
                           HttpServletResponse response, 
                           Object handler) {
        Authentication auth = SecurityContextHolder.getContext().getAuthentication();
        if (auth != null && auth.isAuthenticated()) {
            MDC.put("username", auth.getName());
            MDC.put("roles", auth.getAuthorities().toString());
        }
        return true;
    }
    
    @Override
    public void afterCompletion(HttpServletRequest request, 
                              HttpServletResponse response, 
                              Object handler, 
                              Exception ex) {
        MDC.clear();
    }
}
```

### 3.3 트랜잭션 추적
```java
@Aspect
@Component
public class TransactionLoggingAspect {
    
    @Around("@annotation(org.springframework.transaction.annotation.Transactional)")
    public Object logTransaction(ProceedingJoinPoint joinPoint) throws Throwable {
        String transactionId = UUID.randomUUID().toString();
        MDC.put("transactionId", transactionId);
        
        try {
            log.info("Transaction started");
            Object result = joinPoint.proceed();
            log.info("Transaction completed successfully");
            return result;
        } catch (Exception e) {
            log.error("Transaction failed", e);
            throw e;
        } finally {
            MDC.remove("transactionId");  // 특정 키만 제거
        }
    }
}
```

## 4. 비동기 처리에서의 MDC 사용

### 4.1 @Async 메소드에서 MDC 복사
```java
@Component
public class MDCTaskDecorator implements TaskDecorator {
    @Override
    public Runnable decorate(Runnable runnable) {
        // 현재 스레드의 MDC 컨텍스트 복사
        Map<String, String> contextMap = MDC.getCopyOfContextMap();
        
        return () -> {
            try {
                // 새 스레드에 MDC 컨텍스트 설정
                if (contextMap != null) {
                    MDC.setContextMap(contextMap);
                }
                runnable.run();
            } finally {
                MDC.clear();
            }
        };
    }
}

@Configuration
public class AsyncConfig implements AsyncConfigurer {
    @Override
    public Executor getAsyncExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setTaskDecorator(new MDCTaskDecorator());
        // 다른 설정들...
        executor.initialize();
        return executor;
    }
}
```

### 4.2 CompletableFuture에서의 사용
```java
@Service
public class AsyncService {
    public CompletableFuture<String> processAsync(String data) {
        Map<String, String> contextMap = MDC.getCopyOfContextMap();
        
        return CompletableFuture.supplyAsync(() -> {
            try {
                if (contextMap != null) {
                    MDC.setContextMap(contextMap);
                }
                log.info("Processing data asynchronously");
                return "Processed: " + data;
            } finally {
                MDC.clear();
            }
        });
    }
}
```

## 5. 주의사항 및 Best Practices

### 5.1 메모리 누수 방지
```java
public void process() {
    try {
        MDC.put("key", "value");
        // 처리 로직
    } finally {
        MDC.clear();  // 또는 MDC.remove("key")
    }
}
```

### 5.2 적절한 키 관리
```java
public class MDCKeys {
    public static final String REQUEST_ID = "requestId";
    public static final String USER_ID = "userId";
    public static final String SESSION_ID = "sessionId";
    // ... 다른 키들
}
```

### 5.3 값 길이 제한
```java
public void setMDCValue(String key, String value) {
    if (value != null && value.length() > 32) {
        value = value.substring(0, 32) + "...";
    }
    MDC.put(key, value);
}
```

## 6. 로그 출력 예시

### 6.1 단일 요청 추적
```
[2024-10-29 14:23:45.678] [INFO] [req-123e4567] [user-789] [/api/users] - Request started
[2024-10-29 14:23:45.789] [INFO] [req-123e4567] [user-789] [/api/users] - Processing user data
[2024-10-29 14:23:45.890] [INFO] [req-123e4567] [user-789] [/api/users] - Request completed
```

### 6.2 에러 상황 추적
```
[2024-10-29 14:23:45.678] [ERROR] [req-123e4567] [user-789] [/api/users] - Error processing request
java.lang.Exception: Some error
    at com.example.Service.method(Service.java:123)
    ...
```

## 7. 성능 고려사항

### 7.1 MDC 사용 최적화
```java
// 로그 레벨 체크와 함께 사용
if (log.isDebugEnabled()) {
    MDC.put("details", generateExpensiveDetails());
    log.debug("Detailed processing information");
    MDC.remove("details");
}
```

### 7.2 컨텍스트 맵 재사용
```java
@Component
public class MDCContext {
    private static final Map<String, String> COMMON_CONTEXT;
    
    static {
        COMMON_CONTEXT = new HashMap<>();
        COMMON_CONTEXT.put("applicationName", "MyApp");
        COMMON_CONTEXT.put("environment", "PROD");
    }
    
    public static void setCommonContext() {
        MDC.setContextMap(new HashMap<>(COMMON_CONTEXT));
    }
}
```

이러한 MDC 사용 패턴을 통해 구조화된 로깅을 구현할 수 있으며, 특히 마이크로서비스 환경에서 요청 추적과 문제 해결에 매우 유용합니다.
