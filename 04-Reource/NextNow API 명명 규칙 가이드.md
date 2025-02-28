---
created: 2024-10-21 13:14
updated: 2025-02-28 12:30
related:
  - "[[NextNow API 개발 가이드 구축]]"
tags:
  - 가이드
  - 넥스트나우
  - NextNow
---
# Index

- [1. 소개](#1.%20%EC%86%8C%EA%B0%9C)
- [2. 기본 원칙](#2.%20%EA%B8%B0%EB%B3%B8%20%EC%9B%90%EC%B9%99)
- [3. 명명 규칙](#3.%20%EB%AA%85%EB%AA%85%20%EA%B7%9C%EC%B9%99)
	- [3.1. 컨트롤러](#3.1.%20%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC)
	- [3.2. 메서드](#3.2.%20%EB%A9%94%EC%84%9C%EB%93%9C)
	- [3.3. DTO (Data Transfer Object)](#3.3.%20DTO%20(Data%20Transfer%20Object))
	- [3.4. URL 경로](#3.4.%20URL%20%EA%B2%BD%EB%A1%9C)
- [4. 검색 조건 용어 비교](#4.%20%EA%B2%80%EC%83%89%20%EC%A1%B0%EA%B1%B4%20%EC%9A%A9%EC%96%B4%20%EB%B9%84%EA%B5%90)
- [5. 샘플 코드](#5.%20%EC%83%98%ED%94%8C%20%EC%BD%94%EB%93%9C)
- [6. 추가 고려사항](#6.%20%EC%B6%94%EA%B0%80%20%EA%B3%A0%EB%A0%A4%EC%82%AC%ED%95%AD)



# 1. 소개

이 가이드는 우리 회사의 API 개발에 있어 일관성 있고 명확한 명명 규칙을 제시합니다. 우리는 '혼합 접근법'을 채택하여 명사와 동사를 적절히 조합함으로써 코드의 가독성과 유지보수성을 향상시키고자 합니다.

# 2. 기본 원칙

1. 명확성: 이름만으로도 해당 컴포넌트의 목적을 이해할 수 있어야 합니다.
2. 일관성: 전체 프로젝트에서 동일한 패턴을 유지해야 합니다.
3. 간결성: 불필요한 단어는 제거하고, 핵심만 남깁니다.
4. 도메인 중심: 주요 비즈니스 개념을 이름에 반영합니다.

# 3. 명명 규칙

## 3.1. 컨트롤러
- 명사 + "Controller"로 명명
- 예: `UserController`, `ProductController`

## 3.2. 메서드
- HTTP 메서드에 해당하는 동사 + 명사(도메인 개념)
- 예: `getUser`, `createOrder`, `updateProfile`, `deleteComment`

## 3.3. DTO (Data Transfer Object)
- 요청(Request) DTO: 동사 + 명사 + "Request"
  - 예: `CreateUserRequest`, `UpdateOrderRequest`
- 응답(Response) DTO: 명사 + "Response"
  - 예: `UserResponse`, `OrderResponse`
- 검색 조건 DTO: 명사 + 선택한 검색 조건 용어
  - 예: `UserSearchCriteria`, `OrderFilterParameters`

## 3.4. URL 경로
- 복수형 명사 사용
- 계층 구조를 반영
- 예: `/api/v1/users`, `/api/v1/users/{userId}/orders`

# 4. 검색 조건 용어 비교

검색 조건을 나타내는 다양한 용어들과 그 장단점, 그리고 한국어 발음을 비교한 표입니다:

| 용어            | 한국어 발음 | 장점                                                        | 단점                                   | 추천 사용 상황               |
| ------------- | ------ | --------------------------------------------------------- | ------------------------------------ | ---------------------- |
| Criteria      | 크라이티리아 | - 기술적으로 정확한 용어<br>- ORM 프레임워크에서 흔히 사용됨<br>- 복잡한 검색 조건에 적합 | - 비기술적 사용자에게 생소할 수 있음                | 복잡한 검색 조건, ORM 사용 시    |
| Condition     | 컨디션    | - 직관적이고 일반적인 용어<br>- 다양한 상황에 유연하게 적용 가능                   | - 때로 너무 일반적이어서 모호할 수 있음              | 간단한 검색 조건, 일반적인 상황     |
| Filter        | 필터     | - 사용자 인터페이스에서 흔히 사용되는 용어<br>- 직관적이고 이해하기 쉬움               | - 데이터 처리 관점에서는 덜 정확할 수 있음            | 사용자 인터페이스 관련 기능        |
| Parameter     | 파라미터   | - 프로그래밍에서 널리 사용되는 용어<br>- 함수나 메서드의 입력을 나타내는 데 적합          | - 검색 조건 외의 다른 입력값도 포함할 수 있어 모호할 수 있음 | 메서드 파라미터로 사용 시         |
| Query         | 쿼리     | - 데이터베이스 컨텍스트에서 정확한 용어<br>- 복잡한 검색 작업을 잘 표현함              | - 때로 SQL 쿼리와 혼동될 수 있음                | 데이터베이스 관련 작업           |
| Specification | 스펙피케이션 | - 도메인 주도 설계(DDD)에서 사용되는 용어<br>- 복잡한 비즈니스 규칙을 표현하는 데 적합    | - 단순한 검색 조건에는 과도하게 복잡할 수 있음          | 도메인 주도 설계, 복잡한 비즈니스 규칙 |
| Predicate     | 프레디킷   | - 함수형 프로그래밍에서 자주 사용됨<br>- 불리언 로직을 명확히 표현                  | - 비개발자에게는 이해하기 어려울 수 있음              | 함수형 프로그래밍 컨텍스트         |

프로젝트의 성격과 팀의 선호도를 고려하여 적절한 용어를 선택하고, 일관성 있게 사용하세요.

# 5. 샘플 코드

다음은 위 규칙을 적용한 샘플 코드입니다:

```java
@RestController
@RequestMapping("/api/v1/users")
public class UserController {

    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    @GetMapping("/{userId}")
    public ApiResponse<UserResponse> getUser(@PathVariable Long userId) {
        UserResponse user = userService.getUser(userId);
        return ApiResponse.success(user);
    }

    @PostMapping
    public ApiResponse<UserResponse> createUser(@RequestBody @Valid CreateUserRequest request) {
        UserResponse createdUser = userService.createUser(request);
        return ApiResponse.success(createdUser);
    }

    @PutMapping("/{userId}")
    public ApiResponse<UserResponse> updateUser(
            @PathVariable Long userId,
            @RequestBody @Valid UpdateUserRequest request) {
        UserResponse updatedUser = userService.updateUser(userId, request);
        return ApiResponse.success(updatedUser);
    }

    @DeleteMapping("/{userId}")
    public ApiResponse<Void> deleteUser(@PathVariable Long userId) {
        userService.deleteUser(userId);
        return ApiResponse.success(null);
    }

    @GetMapping
    public ApiResponse<Page<UserResponse>> searchUsers(
            @Valid UserSearchCriteria criteria,
            Pageable pageable) {
        Page<UserResponse> users = userService.searchUsers(criteria, pageable);
        return ApiResponse.success(users);
    }
}

public class CreateUserRequest {
    private String username;
    private String email;
    // Other fields, getters, setters
}

public class UpdateUserRequest {
    private String email;
    // Other fields, getters, setters
}

public class UserResponse {
    private Long id;
    private String username;
    private String email;
    // Other fields, getters, setters
}

public class UserSearchCriteria {
    private String usernameContains;
    private String emailContains;
    // Other fields, getters, setters
}
```

# 6. 추가 고려사항

1. 버전 관리: API 버전을 URL에 포함시키세요. (예: `/api/v1/...`)
2. 일관된 응답 형식: `ApiResponse<T>`와 같은 래퍼 클래스를 사용하여 일관된 응답 구조를 유지하세요.
3. 유효성 검사: `@Valid` 어노테이션을 사용하여 입력 데이터의 유효성을 검사하세요.
4. 페이지네이션: 목록 조회 API에는 페이지네이션을 적용하세요.
5. 예외 처리: 통일된 예외 처리 메커니즘을 사용하세요.
6. 문서화: Swagger나 Spring REST Docs를 사용하여 API를 문서화하세요.
7. 테스트: 단위 테스트와 통합 테스트를 작성하여 API의 정확성을 보장하세요.

이 가이드를 따르면 일관성 있고 이해하기 쉬운 API를 개발할 수 있습니다. 팀 내에서 이 규칙을 공유하고, 코드 리뷰 시 이를 기준으로 피드백을 주고받으세요. 필요에 따라 이 가이드를 조정하고 발전시켜 나가는 것도 좋습니다.