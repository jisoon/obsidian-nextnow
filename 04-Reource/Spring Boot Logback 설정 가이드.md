---
tags:
  - spring
  - logger
  - 가이드
  - 넥스트나우
created: 2024-10-29 12:16
modified: 2024-10-29 12:16
related:
  - "[[MDC(Mapped Diagnostic Context) 상세 가이드]]"
  - "[[NextNow API 개발 가이드 구축]]"
updated: 2024-12-17 09:27
---
## Index

- [1. 기본 설정](#1.%20%EA%B8%B0%EB%B3%B8%20%EC%84%A4%EC%A0%95)
	- [1.1 의존성 설정 (build.gradle)](#1.1%20%EC%9D%98%EC%A1%B4%EC%84%B1%20%EC%84%A4%EC%A0%95%20(build.gradle))
	- [1.2 logback-spring.xml 기본 구조](#1.2%20logback-spring.xml%20%EA%B8%B0%EB%B3%B8%20%EA%B5%AC%EC%A1%B0)
- [2. 로깅 구현](#2.%20%EB%A1%9C%EA%B9%85%20%EA%B5%AC%ED%98%84)
	- [2.1 @Slf4j 사용](#2.1%20@Slf4j%20%EC%82%AC%EC%9A%A9)
	- [2.2 MDC 설정](#2.2%20MDC%20%EC%84%A4%EC%A0%95)
	- [2.3 Marker 설정](#2.3%20Marker%20%EC%84%A4%EC%A0%95)
- [3. 고급 설정](#3.%20%EA%B3%A0%EA%B8%89%20%EC%84%A4%EC%A0%95)
	- [3.1 로그 패턴 커스터마이징](#3.1%20%EB%A1%9C%EA%B7%B8%20%ED%8C%A8%ED%84%B4%20%EC%BB%A4%EC%8A%A4%ED%84%B0%EB%A7%88%EC%9D%B4%EC%A7%95)
	- [3.2 로그 레벨 설정](#3.2%20%EB%A1%9C%EA%B7%B8%20%EB%A0%88%EB%B2%A8%20%EC%84%A4%EC%A0%95)
	- [3.3 비동기 로깅 설정](#3.3%20%EB%B9%84%EB%8F%99%EA%B8%B0%20%EB%A1%9C%EA%B9%85%20%EC%84%A4%EC%A0%95)
- [4. 실무 활용 예제](#4.%20%EC%8B%A4%EB%AC%B4%20%ED%99%9C%EC%9A%A9%20%EC%98%88%EC%A0%9C)
	- [4.1 실무 추천 통합 패턴](#4.1%20%EC%8B%A4%EB%AC%B4%20%EC%B6%94%EC%B2%9C%20%ED%86%B5%ED%95%A9%20%ED%8C%A8%ED%84%B4)
		- [개발 환경 (콘솔)](#%EA%B0%9C%EB%B0%9C%20%ED%99%98%EA%B2%BD%20(%EC%BD%98%EC%86%94))
		- [운영 환경 (파일)](#%EC%9A%B4%EC%98%81%20%ED%99%98%EA%B2%BD%20(%ED%8C%8C%EC%9D%BC))
	- [4.2 통합 로깅 구현](#4.2%20%ED%86%B5%ED%95%A9%20%EB%A1%9C%EA%B9%85%20%EA%B5%AC%ED%98%84)
	- [4.3 성능 측정 AOP](#4.3%20%EC%84%B1%EB%8A%A5%20%EC%B8%A1%EC%A0%95%20AOP)
	- [4.4 에러 처리](#4.4%20%EC%97%90%EB%9F%AC%20%EC%B2%98%EB%A6%AC)
- [5. 성능 최적화 팁](#5.%20%EC%84%B1%EB%8A%A5%20%EC%B5%9C%EC%A0%81%ED%99%94%20%ED%8C%81)
	- [5.1 로그 레벨 체크](#5.1%20%EB%A1%9C%EA%B7%B8%20%EB%A0%88%EB%B2%A8%20%EC%B2%B4%ED%81%AC)
	- [5.2 비동기 로깅 활용](#5.2%20%EB%B9%84%EB%8F%99%EA%B8%B0%20%EB%A1%9C%EA%B9%85%20%ED%99%9C%EC%9A%A9)
	- [5.3 로그 버퍼 설정](#5.3%20%EB%A1%9C%EA%B7%B8%20%EB%B2%84%ED%8D%BC%20%EC%84%A4%EC%A0%95)
- [6. 로그 레벨별 사용 가이드](#6.%20%EB%A1%9C%EA%B7%B8%20%EB%A0%88%EB%B2%A8%EB%B3%84%20%EC%82%AC%EC%9A%A9%20%EA%B0%80%EC%9D%B4%EB%93%9C)
	- [6.1 ERROR](#6.1%20ERROR)
	- [6.2 WARN](#6.2%20WARN)
	- [6.3 INFO](#6.3%20INFO)
	- [6.4 DEBUG](#6.4%20DEBUG)
	- [6.5 TRACE](#6.5%20TRACE)
- [7. 보안 고려사항](#7.%20%EB%B3%B4%EC%95%88%20%EA%B3%A0%EB%A0%A4%EC%82%AC%ED%95%AD)
	- [7.1 민감 정보 마스킹](#7.1%20%EB%AF%BC%EA%B0%90%20%EC%A0%95%EB%B3%B4%20%EB%A7%88%EC%8A%A4%ED%82%B9)


## 1. 기본 설정

### 1.1 의존성 설정 (build.gradle)
```gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
}
```

### 1.2 logback-spring.xml 기본 구조
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!-- 속성 설정 -->
    <property name="LOG_PATH" value="./logs"/>
    <property name="LOG_FILE_NAME" value="application"/>
    <property name="LOG_PATTERN" value="[%d{yyyy-MM-dd HH:mm:ss.SSS}] [%-5level] [%thread] [%logger{0}] [%marker] [%X{requestId}] [%C{1}:%L] - %msg%n"/>

    <!-- 콘솔 애펜더 -->
    <appender name="Console" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>${LOG_PATTERN}</pattern>
        </encoder>
    </appender>

    <!-- 파일 애펜더 -->
    <appender name="File" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOG_PATH}/${LOG_FILE_NAME}.log</file>
        <encoder>
            <pattern>${LOG_PATTERN}</pattern>
        </encoder>
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <fileNamePattern>${LOG_PATH}/${LOG_FILE_NAME}.%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <maxFileSize>100MB</maxFileSize>
            <maxHistory>30</maxHistory>
            <totalSizeCap>3GB</totalSizeCap>
        </rollingPolicy>
    </appender>

    <!-- 프로파일별 설정 -->
    <springProfile name="local">
        <root level="DEBUG">
            <appender-ref ref="Console"/>
        </root>
    </springProfile>

    <springProfile name="prod">
        <root level="INFO">
            <appender-ref ref="File"/>
        </root>
    </springProfile>
</configuration>
```

## 2. 로깅 구현

### 2.1 @Slf4j 사용
```java
@Slf4j(topic = "BusinessLog")
@RestController
public class UserController {
    
    @GetMapping("/users/{id}")
    public ResponseEntity<User> getUser(@PathVariable Long id) {
        log.debug("User lookup request for id: {}", id);
        // 비즈니스 로직
        log.info("User found successfully: {}", id);
        return ResponseEntity.ok(user);
    }
}
```

### 2.2 MDC 설정
```java
@Component
public class MDCLoggingFilter extends OncePerRequestFilter {
    
    @Override
    protected void doFilterInternal(HttpServletRequest request, 
                                  HttpServletResponse response, 
                                  FilterChain filterChain) throws ServletException, IOException {
        try {
            setupMDC(request);
            filterChain.doFilter(request, response);
        } finally {
            MDC.clear();
        }
    }
    
    private void setupMDC(HttpServletRequest request) {
        MDC.put("requestId", UUID.randomUUID().toString());
        MDC.put("clientIp", request.getRemoteAddr());
        MDC.put("method", request.getMethod());
        MDC.put("uri", request.getRequestURI());
        // 인증된 사용자 정보가 있다면
        SecurityContext context = SecurityContextHolder.getContext();
        if (context.getAuthentication() != null) {
            MDC.put("username", context.getAuthentication().getName());
        }
    }
}
```

### 2.3 Marker 설정
```java
@Slf4j
@Service
public class UserService {
    private static final Marker AUDIT = MarkerFactory.getMarker("AUDIT");
    private static final Marker SECURITY = MarkerFactory.getMarker("SECURITY");
    
    public User createUser(UserDto dto) {
        log.info(AUDIT, "Creating new user: {}", dto.getEmail());
        try {
            User user = userRepository.save(dto.toEntity());
            log.info(SECURITY, "User created: {}", user.getId());
            return user;
        } catch (Exception e) {
            log.error(SECURITY, "User creation failed", e);
            throw e;
        }
    }
}
```

## 3. 고급 설정

### 3.1 로그 패턴 커스터마이징
```xml
<!-- 개발 환경용 상세 패턴 -->
<property name="CONSOLE_LOG_PATTERN" 
    value="%d{HH:mm:ss.SSS} %highlight(%-5level) %magenta([%thread]) %cyan(%logger{0}) [%method:%line] %yellow([%X{requestId}]) - %msg%n"/>

<!-- 운영 환경용 표준 패턴 -->
<property name="FILE_LOG_PATTERN" 
    value="[%d{yyyy-MM-dd HH:mm:ss.SSS}] [%-5level] [%thread] [%logger{0}] [%marker] [%X{requestId}] [%C{1}:%L] - %msg%n"/>
```

### 3.2 로그 레벨 설정
```xml
<logger name="com.example.api" level="DEBUG"/>
<logger name="com.example.core" level="INFO"/>
<logger name="org.springframework" level="WARN"/>
```

### 3.3 비동기 로깅 설정
```xml
<appender name="AsyncFile" class="ch.qos.logback.classic.AsyncAppender">
    <appender-ref ref="File"/>
    <queueSize>512</queueSize>
    <discardingThreshold>0</discardingThreshold>
    <includeCallerData>true</includeCallerData>
</appender>
```

## 4. 실무 활용 예제
### 4.1 실무 추천 통합 패턴

#### 개발 환경 (콘솔)
```xml
<Pattern>
%d{HH:mm:ss.SSS} %highlight(%-5level) %magenta([%thread]) %cyan(%logger{0}) [%C{1}.%M:%L] %yellow([%X{reqId}]) - %msg%n%throwable
</Pattern>
```

#### 운영 환경 (파일)
```xml
<Pattern>
[%d{yyyy-MM-dd HH:mm:ss.SSS}] [%-5level] [%thread] [%logger{0}] [%C{1}:%L] [%X{reqId}] [%X{method}] [%X{uri}] - %msg%n%throwable
</Pattern>
```


### 4.2 통합 로깅 구현
```java
@Slf4j
@RestController
public class OrderController {
    private static final Marker TRANSACTION = MarkerFactory.getMarker("TRANSACTION");
    
    @PostMapping("/orders")
    public ResponseEntity<Order> createOrder(@RequestBody OrderDto orderDto) {
        String orderId = UUID.randomUUID().toString();
        MDC.put("orderId", orderId);
        
        try {
            log.info(TRANSACTION, "Order creation started: {}", orderDto);
            // 비즈니스 로직
            log.info(TRANSACTION, "Order created successfully");
            return ResponseEntity.ok(order);
        } catch (Exception e) {
            log.error(TRANSACTION, "Order creation failed: {}", e.getMessage(), e);
            throw e;
        } finally {
            MDC.remove("orderId");
        }
    }
}
```

### 4.3 성능 측정 AOP
```java
@Aspect
@Component
@Slf4j
public class PerformanceLoggingAspect {
    private static final Marker PERFORMANCE = MarkerFactory.getMarker("PERFORMANCE");
    
    @Around("@annotation(LogPerformance)")
    public Object logPerformance(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();
        try {
            return joinPoint.proceed();
        } finally {
            long end = System.currentTimeMillis();
            log.info(PERFORMANCE, "Method: {}.{} took {}ms", 
                    joinPoint.getSignature().getDeclaringType().getSimpleName(),
                    joinPoint.getSignature().getName(),
                    end - start);
        }
    }
}
```

### 4.4 에러 처리
```java
@ControllerAdvice
@Slf4j
public class GlobalExceptionHandler {
    private static final Marker ERROR = MarkerFactory.getMarker("ERROR");
    
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleException(Exception e, HttpServletRequest request) {
        String errorId = UUID.randomUUID().toString();
        MDC.put("errorId", errorId);
        
        log.error(ERROR, "Unhandled exception occurred: {}", e.getMessage(), e);
        
        ErrorResponse response = ErrorResponse.builder()
            .errorId(errorId)
            .message("An unexpected error occurred")
            .path(request.getRequestURI())
            .timestamp(LocalDateTime.now())
            .build();
            
        MDC.remove("errorId");
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(response);
    }
}
```


## 5. 성능 최적화 팁

### 5.1 로그 레벨 체크
```java
if (log.isDebugEnabled()) {
    log.debug("Complex calculation result: {}", expensiveOperation());
}
```

### 5.2 비동기 로깅 활용
```xml
<configuration>
    <appender name="AsyncConsole" class="ch.qos.logback.classic.AsyncAppender">
        <appender-ref ref="Console"/>
        <queueSize>512</queueSize>
        <discardingThreshold>0</discardingThreshold>
        <includeCallerData>true</includeCallerData>
    </appender>
    
    <root level="INFO">
        <appender-ref ref="AsyncConsole"/>
    </root>
</configuration>
```

### 5.3 로그 버퍼 설정
```xml
<appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <immediateFlush>false</immediateFlush>
    <bufferSize>8192</bufferSize>
</appender>
```


## 6. 로그 레벨별 사용 가이드

### 6.1 ERROR
- 시스템 동작이 불가능한 경우
- 즉각적인 조치가 필요한 경우
```java
log.error("결제 처리 실패: orderNo={}, error={}", orderNo, e.getMessage(), e);
```

### 6.2 WARN
- 시스템은 동작하지만 잠재적 문제가 있는 경우
- 예상치 못한 상황이지만 자동 복구 가능한 경우
```java
log.warn("재시도 처리 발생: requestId={}, retryCount={}", requestId, retryCount);
```

### 6.3 INFO
- 주요 비즈니스 로직 완료
- 시스템 시작/종료
```java
log.info("주문 처리 완료: orderNo={}, amount={}", orderNo, amount);
```

### 6.4 DEBUG
- 개발 중 문제 해결을 위한 상세 정보
```java
log.debug("API 요청 파라미터: {}", requestDto);
```

### 6.5 TRACE
- 가장 상세한 정보
- 메소드 진입/종료, 루프 반복 등
```java
log.trace("메소드 시작: paralmeters={}", Arrays.toString(args));
```

## 7. 보안 고려사항

### 7.1 민감 정보 마스킹
```java
public class MaskingConverter extends MessageConverter {
    private static final String MASK = "********";
    private static final Pattern CARD_PATTERN = Pattern.compile("\\d{4}-?\\d{4}-?\\d{4}-?\\d{4}");
    
    @Override
    public String convert(ILoggingEvent event) {
        String message = event.getFormattedMessage();
        message = CARD_PATTERN.matcher(message).replaceAll(MASK);
        return message;
    }
}
```
