---
created: 2024-10-21 12:29
updated: 2024-10-24 13:50
tags:
  - JWT
related:
  - "[[JWT의 탄생 배경]]"
---
## Index

- [1. JWT의 구조](#1.%20JWT%EC%9D%98%20%EA%B5%AC%EC%A1%B0)
	- [1.1 헤더 (Header)](#1.1%20%ED%97%A4%EB%8D%94%20(Header))
	- [1.2 페이로드 (Payload)](#1.2%20%ED%8E%98%EC%9D%B4%EB%A1%9C%EB%93%9C%20(Payload))
	- [1.3 서명 (Signature)](#1.3%20%EC%84%9C%EB%AA%85%20(Signature))
- [2. JWT의 장단점](#2.%20JWT%EC%9D%98%20%EC%9E%A5%EB%8B%A8%EC%A0%90)
	- [장점:](#%EC%9E%A5%EC%A0%90:)
	- [단점:](#%EB%8B%A8%EC%A0%90:)
- [3. JWT 사용 시나리오](#3.%20JWT%20%EC%82%AC%EC%9A%A9%20%EC%8B%9C%EB%82%98%EB%A6%AC%EC%98%A4)
- [4. JWT 구현 시 고려사항](#4.%20JWT%20%EA%B5%AC%ED%98%84%20%EC%8B%9C%20%EA%B3%A0%EB%A0%A4%EC%82%AC%ED%95%AD)
	- [4.1 토큰 저장](#4.1%20%ED%86%A0%ED%81%B0%20%EC%A0%80%EC%9E%A5)
	- [4.2 토큰 갱신](#4.2%20%ED%86%A0%ED%81%B0%20%EA%B0%B1%EC%8B%A0)
	- [4.3 토큰 해지](#4.3%20%ED%86%A0%ED%81%B0%20%ED%95%B4%EC%A7%80)
- [5. Spring Boot에서의 JWT 보안 강화](#5.%20Spring%20Boot%EC%97%90%EC%84%9C%EC%9D%98%20JWT%20%EB%B3%B4%EC%95%88%20%EA%B0%95%ED%99%94)
	- [5.1 비밀키 관리](#5.1%20%EB%B9%84%EB%B0%80%ED%82%A4%20%EA%B4%80%EB%A6%AC)
	- [5.2 CSRF 보호](#5.2%20CSRF%20%EB%B3%B4%ED%98%B8)
	- [5.3 요청 제한 (Rate Limiting)](#5.3%20%EC%9A%94%EC%B2%AD%20%EC%A0%9C%ED%95%9C%20(Rate%20Limiting))
- [결론](#%EA%B2%B0%EB%A1%A0)

## 1. JWT의 구조

JWT는 세 부분으로 구성되며, 각 부분은 점(.)으로 구분됩니다:

1. 헤더 (Header)
2. 페이로드 (Payload)
3. 서명 (Signature)

예: xxxxx.yyyyy.zzzzz

### 1.1 헤더 (Header)

- 토큰 유형(typ)과 해시 알고리즘(alg)을 지정합니다.
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

### 1.2 페이로드 (Payload)

- 클레임(claim)이라 불리는 엔티티와 추가 데이터를 포함합니다.
```json
{
  "sub": "1234567890",
  "name": "홍길동",
  "admin": true,
  "iat": 1516239022
}
```

### 1.3 서명 (Signature)

- 헤더의 인코딩 값과 페이로드의 인코딩 값을 합친 후, 비밀키로 해시하여 생성합니다.
```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret
)
```

## 2. JWT의 장단점

### 장점:

1. 상태 비저장 (Stateless): 서버 측에서 별도의 세션 저장소가 필요 없습니다.
2. 확장성: 분산 시스템에서 효과적으로 작동합니다.
3. 모바일 친화적: 모바일 환경에서도 쉽게 사용할 수 있습니다.
4. 크로스 도메인/CORS: 여러 도메인에서 쉽게 사용할 수 있습니다.

### 단점:

1. 페이로드 크기: JWT에 너무 많은 정보를 담으면 네트워크 부하가 증가할 수 있습니다.
2. 보안: 한번 발급된 토큰은 만료되기 전까지 계속 유효합니다. (해지 리스트 관리 필요)
3. 저장: 클라이언트 측에서 안전하게 저장해야 합니다.

## 3. JWT 사용 시나리오

1. 인증 (Authentication)
2. 정보 교환 (Information Exchange)
3. 싱글 사인온 (Single Sign On)

## 4. JWT 구현 시 고려사항

### 4.1 토큰 저장

- 클라이언트 측: LocalStorage나 SessionStorage 대신 HttpOnly 쿠키 사용 권장
- 서버 측: 중요한 정보는 토큰에 직접 저장하지 않고, 데이터베이스 참조 키만 저장

### 4.2 토큰 갱신

Refresh Token을 사용한 갱신 방식:

```java
@PostMapping("/token/refresh")
public ResponseEntity<?> refreshToken(@RequestBody RefreshTokenRequest request) {
    String refreshToken = request.getRefreshToken();
    if (jwtUtil.validateRefreshToken(refreshToken)) {
        String username = jwtUtil.extractUsernameFromRefreshToken(refreshToken);
        String newAccessToken = jwtUtil.generateToken(username);
        return ResponseEntity.ok(new TokenRefreshResponse(newAccessToken));
    } else {
        return ResponseEntity.status(HttpStatus.UNAUTHORIZED).body("Invalid refresh token");
    }
}
```

### 4.3 토큰 해지

블랙리스트 방식 예제:

```java
@Component
public class TokenBlacklist {
    private Set<String> blacklist = new ConcurrentHashSet<>();

    public void addToBlacklist(String token) {
        blacklist.add(token);
    }

    public boolean isBlacklisted(String token) {
        return blacklist.contains(token);
    }
}

// JwtUtil 클래스에 추가
@Autowired
private TokenBlacklist tokenBlacklist;

public boolean validateToken(String token, String username) {
    if (tokenBlacklist.isBlacklisted(token)) {
        return false;
    }
    // 기존 검증 로직
    ...
}
```

## 5. Spring Boot에서의 JWT 보안 강화

### 5.1 비밀키 관리

- 환경 변수나 암호화된 설정 파일 사용
```yaml
jwt:
  secret: ${JWT_SECRET}
  expiration: 3600000
```

### 5.2 CSRF 보호

CSRF 토큰 사용 예제:

```java
@Controller
public class CSRFController {
    @GetMapping("/csrf")
    public String getCSRFToken(HttpServletRequest request, Model model) {
        CsrfToken csrf = (CsrfToken) request.getAttribute(CsrfToken.class.getName());
        model.addAttribute("csrf", csrf);
        return "csrfPage";
    }
}
```

### 5.3 요청 제한 (Rate Limiting)

Bucket4j를 사용한 요청 제한 예제:

```java
@Configuration
public class RateLimitConfig {
    @Bean
    public Bucket createNewBucket() {
        long overdraft = 50;
        Refill refill = Refill.greedy(10, Duration.ofMinutes(1));
        Bandwidth limit = Bandwidth.classic(overdraft, refill);
        return Bucket4j.builder().addLimit(limit).build();
    }
}

@RestController
public class RateLimitedController {
    @Autowired
    private Bucket bucket;

    @GetMapping("/api/limited")
    public ResponseEntity<?> getLimitedResource() {
        if (bucket.tryConsume(1)) {
            return ResponseEntity.ok("요청이 성공적으로 처리되었습니다.");
        }
        return ResponseEntity.status(HttpStatus.TOO_MANY_REQUESTS).body("요청 한도를 초과했습니다.");
    }
}
```

## 결론

JWT는 현대 웹 애플리케이션에서 인증과 정보 교환을 위한 강력한 도구입니다. Spring Boot와 함께 사용하면 안전하고 확장 가능한 인증 시스템을 구축할 수 있습니다. 하지만 보안, 성능, 사용자 경험을 모두 고려하여 적절히 구현해야 합니다. 지속적인 모니터링과 업데이트를 통해 시스템의 안전성을 유지하는 것이 중요합니다.

이러한 심층적인 이해와 구현 지식은 개발자들이 JWT를 효과적으로 활용하는 데 도움이 될 것입니다. 추가 질문이나 특정 주제에 대해 더 자세한 설명이 필요하시면 언제든 말씀해 주세요.