---
tags:
  - jackson
  - gson
  - 가이드
  - 자기개발
  - 프로그래밍
created: 2024-10-31 00:39
modified: 2024-10-31 00:39
related: 
updated: 2024-11-18 10:55
---
## Index

- [1. 기본 개념](#1.%20%EA%B8%B0%EB%B3%B8%20%EA%B0%9C%EB%85%90)
- [2. 라이브러리 비교](#2.%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%20%EB%B9%84%EA%B5%90)
	- [Jackson](#Jackson)
	- [Gson](#Gson)
- [3. 주요 기능](#3.%20%EC%A3%BC%EC%9A%94%20%EA%B8%B0%EB%8A%A5)
	- [3.1 필드명 매핑](#3.1%20%ED%95%84%EB%93%9C%EB%AA%85%20%EB%A7%A4%ED%95%91)
	- [3.2 민감정보 제어](#3.2%20%EB%AF%BC%EA%B0%90%EC%A0%95%EB%B3%B4%20%EC%A0%9C%EC%96%B4)
	- [3.3 필드 제외](#3.3%20%ED%95%84%EB%93%9C%20%EC%A0%9C%EC%99%B8)
	- [3.4 날짜 형식 지정](#3.4%20%EB%82%A0%EC%A7%9C%20%ED%98%95%EC%8B%9D%20%EC%A7%80%EC%A0%95)
	- [3.5 JSON 문자열 처리](#3.5%20JSON%20%EB%AC%B8%EC%9E%90%EC%97%B4%20%EC%B2%98%EB%A6%AC)
	- [3.6 API 버전 관리 (Gson)](#3.6%20API%20%EB%B2%84%EC%A0%84%20%EA%B4%80%EB%A6%AC%20(Gson))
- [4. 실제 사용 예시](#4.%20%EC%8B%A4%EC%A0%9C%20%EC%82%AC%EC%9A%A9%20%EC%98%88%EC%8B%9C)
	- [4.1 API 응답 DTO](#4.1%20API%20%EC%9D%91%EB%8B%B5%20DTO)
	- [4.2 Controller에서 활용](#4.2%20Controller%EC%97%90%EC%84%9C%20%ED%99%9C%EC%9A%A9)
- [5. 주의사항](#5.%20%EC%A3%BC%EC%9D%98%EC%82%AC%ED%95%AD)
- [6. 자주 발생하는 문제](#6.%20%EC%9E%90%EC%A3%BC%20%EB%B0%9C%EC%83%9D%ED%95%98%EB%8A%94%20%EB%AC%B8%EC%A0%9C)
 

## 1. 기본 개념
Jackson과 Gson은 Java 객체와 JSON 간의 변환을 담당하는 라이브러리입니다.

## 2. 라이브러리 비교

### Jackson
```java
// Maven
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
</dependency>

// 기본 사용
@JsonProperty("user_name")
private String userName;
```

### Gson
```java
// Maven
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
</dependency>

// 기본 사용
@SerializedName("user_name")
private String userName;
```

## 3. 주요 기능

### 3.1 필드명 매핑
```java
public class UserDto {
    // Jackson
    @JsonProperty("user_name")
    private String userName;
    
    // Gson
    @SerializedName("user_name")
    private String userName;
}
```

### 3.2 민감정보 제어
```java
public class UserResponse {
    private String name;
    private String email;
    
    // JSON 응답에서 제외, 요청에서만 사용
    @JsonProperty(access = JsonProperty.Access.WRITE_ONLY)
    private String password;
}
```

### 3.3 필드 제외
```java
public class UserProfile {
    // Jackson
    @JsonIgnore 
    private String secretKey;
    
    // Gson
    @Expose(serialize = false, deserialize = true)
    private String temporaryCode;
}
```

### 3.4 날짜 형식 지정
```java
public class ArticleDto {
    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private LocalDateTime createdAt;
    
    @JsonFormat(pattern = "yyyy-MM-dd")
    private LocalDate publishDate;
}
```

### 3.5 JSON 문자열 처리
```java
public class DataDto {
    @JsonRawValue
    private String jsonData = "{\"key\":\"value\"}";
}
```

### 3.6 API 버전 관리 (Gson)
```java
public class FeatureDto {
    @Since(1.0)  // 1.0 버전부터 사용
    private String newFeature;
    
    @Until(2.0)  // 2.0 버전까지만 사용
    private String oldFeature;
}
```

## 4. 실제 사용 예시

### 4.1 API 응답 DTO
```java
@Getter @Setter
public class UserResponseDto {
    private Long id;
    
    @JsonProperty("user_name")
    private String userName;
    
    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private LocalDateTime createdAt;
    
    @JsonProperty(access = JsonProperty.Access.WRITE_ONLY)
    private String password;
    
    @JsonIgnore
    private String internalNote;
}
```

### 4.2 Controller에서 활용
```java
@RestController
@RequestMapping("/api/v1/users")
public class UserController {
    
    @PostMapping
    public ResponseEntity<UserResponseDto> createUser(@RequestBody UserResponseDto request) {
        // password는 받을 수 있지만, 응답에서는 제외됨
        return ResponseEntity.ok(userService.create(request));
    }
}
```

## 5. 주의사항
- Jackson과 Gson 애노테이션 혼용 금지
- 민감정보는 애노테이션과 함께 보안 로직 구현
- 프로젝트 내 일관된 날짜 형식 사용
- API 스펙 문서화 필수

## 6. 자주 발생하는 문제
1. 필드명 불일치
```java
// 잘못된 예
private String userName;  // JSON: "user_name"

// 해결
@JsonProperty("user_name")
private String userName;
```

2. 날짜 형식 오류
```java
// 잘못된 예
private LocalDateTime createdAt;

// 해결
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
private LocalDateTime createdAt;
```
