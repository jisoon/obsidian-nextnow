---
tags:
  - Java
  - stream
  - anthropic
created: 2024-11-12 09:02
modified: 2024-11-12 09:02
related: 
updated: 2024-11-12 09:04
---
## 리스트 필터링
```java
// 상황: 특정 조건에 맞는 데이터만 추출
List<User> adults = users.stream()
    .filter(user -> user.getAge() >= 20)
    .collect(Collectors.toList());

// 여러 조건 조합
List<User> activeAdults = users.stream()
    .filter(user -> user.getAge() >= 20)
    .filter(User::isActive)
    .collect(Collectors.toList());
```

## 데이터 변환 (매핑)
```java
// 상황: 객체에서 특정 필드만 추출
List<String> userNames = users.stream()
    .map(User::getName)
    .collect(Collectors.toList());

// 중첩 객체 처리
List<Address> addresses = users.stream()
    .map(User::getAddress)
    .filter(Objects::nonNull)
    .collect(Collectors.toList());
```

## 그룹핑
```java
// 상황: 나이대별로 사용자 그룹화
Map<Integer, List<User>> usersByAge = users.stream()
    .collect(Collectors.groupingBy(user -> user.getAge() / 10 * 10));

// 그룹별 카운팅
Map<String, Long> userCountByCity = users.stream()
    .collect(Collectors.groupingBy(
        user -> user.getAddress().getCity(),
        Collectors.counting()
    ));
```

## 집계 연산
```java
// 상황: 통계 데이터 추출
double averageAge = users.stream()
    .mapToInt(User::getAge)
    .average()
    .orElse(0.0);

// 최대/최소값 찾기
User oldestUser = users.stream()
    .max(Comparator.comparing(User::getAge))
    .orElseThrow();
```

## 중복 제거
```java
// 상황: 고유한 값만 추출
List<String> uniqueCities = users.stream()
    .map(user -> user.getAddress().getCity())
    .distinct()
    .collect(Collectors.toList());
```

## 정렬
```java
// 상황: 데이터 정렬
List<User> sortedUsers = users.stream()
    .sorted(Comparator.comparing(User::getAge))
    .collect(Collectors.toList());

// 다중 조건 정렬
List<User> sortedUsers = users.stream()
    .sorted(Comparator.comparing(User::getAge)
        .thenComparing(User::getName))
    .collect(Collectors.toList());
```

## Optional과 함께 사용
```java
// 상황: null 안전한 처리
String firstAdultName = users.stream()
    .filter(user -> user.getAge() >= 20)
    .map(User::getName)
    .findFirst()
    .orElse("Unknown");
```

## flatMap 활용
```java
// 상황: 중첩 컬렉션 처리
List<String> allPhoneNumbers = users.stream()
    .flatMap(user -> user.getPhoneNumbers().stream())
    .collect(Collectors.toList());
```

주요 사용 팁:
1. 가독성을 위해 메서드 체이닝은 적절히 줄바꿈하여 사용하세요.
2. 복잡한 로직은 별도의 메서드로 분리하여 filter나 map에 사용하세요.
3. 병렬 처리가 필요한 경우 `parallelStream()`을 고려하세요.
4. 스트림은 한 번만 사용할 수 있으므로, 재사용이 필요한 경우 새로운 스트림을 생성하세요.

좀 더 구체적인 상황이나 예제가 필요하시다면 말씀해 주세요!
