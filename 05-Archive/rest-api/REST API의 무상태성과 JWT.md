---
created: 2024-10-21 12:25
updated: 2024-11-05 09:22
---
# REST API의 무상태성과 JWT: 현대 웹 개발의 핵심

## 1. REST API의 무상태성 (Statelessness)

REST API의 주요 특징 중 하나는 무상태성입니다. 이는 각 요청이 이전 요청과 독립적으로 처리되어야 한다는 의미입니다.

### 무상태성의 장점:
- 확장성 향상: 서버가 클라이언트의 상태를 저장하지 않아 쉽게 확장 가능
- 단순성: 각 요청이 독립적이므로 시스템 설계와 디버깅이 간단해짐
- 신뢰성: 부분 실패 시 복구가 쉬움

### 무상태성의 과제:
- 인증 정보 관리: 각 요청마다 사용자 인증 정보를 포함해야 함

## 2. JWT (JSON Web Token)의 등장 배경

JWT는 무상태 인증을 위한 해결책으로 등장했습니다.

### JWT 등장 이전의 문제점:
- 세션 기반 인증: 서버 측에서 사용자 세션을 유지해야 함 (무상태성 위배)
- 확장성 제한: 다중 서버 환경에서 세션 동기화 문제 발생

### JWT의 장점:
- 무상태성 유지: 서버가 사용자 상태를 저장할 필요 없음
- 확장성: 어떤 서버에서도 토큰 검증 가능
- 보안성: 암호화된 정보로 안전한 데이터 전송

네, Spring Security를 사용하지 않는 방식으로 JWT를 구현하는 예제를 작성해 드리겠습니다. 이 방법은 더 많은 수동 설정이 필요하지만, Spring Security의 복잡성을 피하고 싶을 때 유용할 수 있습니다.

## 3. Spring Boot에서 JWT 구현 (Spring Security 없이)

### 3.1. 의존성 추가

먼저 `pom.xml`에 JWT 라이브러리를 추가합니다:

```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
    <version>0.9.1</version>
</dependency>
```

### 3.2. JWT 유틸리티 클래스 생성

```java
import io.jsonwebtoken.Claims;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

import java.util.Date;
import java.util.function.Function;

@Component
public class JwtUtil {

    @Value("${jwt.secret}")
    private String secret;

    @Value("${jwt.expiration}")
    private Long expiration;

    public String generateToken(String username) {
        return Jwts.builder()
                .setSubject(username)
                .setIssuedAt(new Date(System.currentTimeMillis()))
                .setExpiration(new Date(System.currentTimeMillis() + expiration * 1000))
                .signWith(SignatureAlgorithm.HS512, secret)
                .compact();
    }

    public Boolean validateToken(String token, String username) {
        final String tokenUsername = extractUsername(token);
        return (tokenUsername.equals(username) && !isTokenExpired(token));
    }

    public String extractUsername(String token) {
        return extractClaim(token, Claims::getSubject);
    }

    public Date extractExpiration(String token) {
        return extractClaim(token, Claims::getExpiration);
    }

    public <T> T extractClaim(String token, Function<Claims, T> claimsResolver) {
        final Claims claims = extractAllClaims(token);
        return claimsResolver.apply(claims);
    }

    private Claims extractAllClaims(String token) {
        return Jwts.parser().setSigningKey(secret).parseClaimsJws(token).getBody();
    }

    private Boolean isTokenExpired(String token) {
        return extractExpiration(token).before(new Date());
    }
}
```

### 3.3. JWT 인터셉터 생성

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@Component
public class JwtInterceptor implements HandlerInterceptor {

    @Autowired
    private JwtUtil jwtUtil;

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        String authHeader = request.getHeader("Authorization");
        String token = null;
        String username = null;

        if (authHeader != null && authHeader.startsWith("Bearer ")) {
            token = authHeader.substring(7);
            username = jwtUtil.extractUsername(token);
        }

        if (username != null && jwtUtil.validateToken(token, username)) {
            return true;
        } else {
            response.setStatus(HttpServletResponse.SC_UNAUTHORIZED);
            return false;
        }
    }
}
```

### 3.4. 인터셉터 설정

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebMvcConfig implements WebMvcConfigurer {

    @Autowired
    private JwtInterceptor jwtInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(jwtInterceptor)
                .addPathPatterns("/api/**")
                .excludePathPatterns("/api/auth/**");
    }
}
```

### 3.5. 컨트롤러 예제

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api")
public class AuthController {

    @Autowired
    private JwtUtil jwtUtil;

    @PostMapping("/auth/login")
    public ResponseEntity<?> login(@RequestBody LoginRequest loginRequest) {
        // 여기서 사용자 인증 로직을 구현해야 합니다.
        // 예를 들어, 데이터베이스에서 사용자 정보를 확인하는 등의 작업
        
        if (isValidUser(loginRequest.getUsername(), loginRequest.getPassword())) {
            String token = jwtUtil.generateToken(loginRequest.getUsername());
            return ResponseEntity.ok(new AuthResponse(token));
        } else {
            return ResponseEntity.badRequest().body("Invalid username or password");
        }
    }

    @GetMapping("/protected")
    public ResponseEntity<?> protectedEndpoint() {
        return ResponseEntity.ok("이 엔드포인트는 보호되어 있습니다!");
    }

    private boolean isValidUser(String username, String password) {
        // 실제 구현에서는 데이터베이스 조회 등을 통해 사용자 검증
        return true; // 예시를 위해 항상 true 반환
    }
}

class LoginRequest {
    private String username;
    private String password;
    // getter와 setter 메서드
}

class AuthResponse {
    private String token;
    
    public AuthResponse(String token) {
        this.token = token;
    }
    // getter 메서드
}
```

### 3.6 코드 설명:

1. `JwtUtil`: JWT 토큰 생성, 검증, 정보 추출 등을 담당하는 유틸리티 클래스입니다.

2. `JwtInterceptor`: 모든 요청을 가로채 JWT 토큰을 검증하는 인터셉터입니다.

3. `WebMvcConfig`: 인터셉터를 Spring MVC 설정에 등록합니다. `/api/**` 경로에 대해 인터셉터를 적용하고, `/api/auth/**` 경로는 제외합니다.

4. `AuthController`: 로그인 및 보호된 엔드포인트 예제를 제공합니다.

### 3.7 사용 방법:

1. 사용자가 `/api/auth/login`에 로그인 요청을 보냅니다.
2. 서버는 사용자 정보를 확인하고 JWT 토큰을 생성하여 반환합니다.
3. 클라이언트는 이후의 요청에 `Authorization: Bearer <token>` 헤더를 포함시킵니다.
4. `JwtInterceptor`가 요청을 가로채 토큰을 검증합니다.
5. 토큰이 유효하면 요청이 처리되고, 그렇지 않으면 401 Unauthorized 응답이 반환됩니다.

이 방식은 Spring Security를 사용하지 않고도 JWT 기반의 인증을 구현할 수 있게 해줍니다. 하지만 실제 프로덕션 환경에서는 더 많은 보안 기능이 필요할 수 있으므로, 프로젝트의 요구사항에 따라 적절히 선택해야 합니다.

## 4. JWT 사용 시 주의사항

1. 토큰 저장: 클라이언트 측에서 안전하게 저장 (예: HttpOnly 쿠키)
2. 토큰 만료: 적절한 만료 시간 설정
3. 토큰 갱신: Refresh Token 사용 고려
4. 보안: HTTPS 사용 필수

## 결론

REST API의 무상태성과 JWT는 현대 웹 개발에서 중요한 개념입니다. Spring Boot와 함께 이를 구현함으로써, 확장 가능하고 안전한 애플리케이션을 개발할 수 있습니다. 지속적인 학습과 최신 보안 동향 파악이 중요합니다.

이 문서가 내부 개발자 교육에 도움이 되길 바랍니다. 추가 질문이나 더 자세한 설명이 필요하시면 언제든 문의해 주세요.