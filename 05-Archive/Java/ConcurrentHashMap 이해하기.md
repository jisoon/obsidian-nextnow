---
tags: 
created: 2024-10-24 17:24
modified: 2024-10-24 17:24
related: []
updated: 2024-10-24 17:27
---
## Index

- [1. ConcurrentHashMap이란?](#1.%20ConcurrentHashMap%EC%9D%B4%EB%9E%80?)
	- [1.1 간단한 비유로 이해하기](#1.1%20%EA%B0%84%EB%8B%A8%ED%95%9C%20%EB%B9%84%EC%9C%A0%EB%A1%9C%20%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0)
- [2. 기본적인 사용법](#2.%20%EA%B8%B0%EB%B3%B8%EC%A0%81%EC%9D%B8%20%EC%82%AC%EC%9A%A9%EB%B2%95)
	- [2.1 생성하기](#2.1%20%EC%83%9D%EC%84%B1%ED%95%98%EA%B8%B0)
	- [2.2 기본 작업](#2.2%20%EA%B8%B0%EB%B3%B8%20%EC%9E%91%EC%97%85)
- [3. 실제 사용 예제](#3.%20%EC%8B%A4%EC%A0%9C%20%EC%82%AC%EC%9A%A9%20%EC%98%88%EC%A0%9C)
	- [3.1 간단한 캐시 구현](#3.1%20%EA%B0%84%EB%8B%A8%ED%95%9C%20%EC%BA%90%EC%8B%9C%20%EA%B5%AC%ED%98%84)
	- [3.2 온라인 사용자 관리](#3.2%20%EC%98%A8%EB%9D%BC%EC%9D%B8%20%EC%82%AC%EC%9A%A9%EC%9E%90%20%EA%B4%80%EB%A6%AC)
- [4. 주의할 점과 팁](#4.%20%EC%A3%BC%EC%9D%98%ED%95%A0%20%EC%A0%90%EA%B3%BC%20%ED%8C%81)
	- [4.1 Null 사용 불가](#4.1%20Null%20%EC%82%AC%EC%9A%A9%20%EB%B6%88%EA%B0%80)
	- [4.2 안전한 복합 연산](#4.2%20%EC%95%88%EC%A0%84%ED%95%9C%20%EB%B3%B5%ED%95%A9%20%EC%97%B0%EC%82%B0)
- [5. 실전 활용 예제: 간단한 방문자 카운터](#5.%20%EC%8B%A4%EC%A0%84%20%ED%99%9C%EC%9A%A9%20%EC%98%88%EC%A0%9C:%20%EA%B0%84%EB%8B%A8%ED%95%9C%20%EB%B0%A9%EB%AC%B8%EC%9E%90%20%EC%B9%B4%EC%9A%B4%ED%84%B0)
- [6. 언제 ConcurrentHashMap을 사용해야 할까?](#6.%20%EC%96%B8%EC%A0%9C%20ConcurrentHashMap%EC%9D%84%20%EC%82%AC%EC%9A%A9%ED%95%B4%EC%95%BC%20%ED%95%A0%EA%B9%8C?)
	- [사용해야 하는 경우:](#%EC%82%AC%EC%9A%A9%ED%95%B4%EC%95%BC%20%ED%95%98%EB%8A%94%20%EA%B2%BD%EC%9A%B0:)
	- [사용하지 않아도 되는 경우:](#%EC%82%AC%EC%9A%A9%ED%95%98%EC%A7%80%20%EC%95%8A%EC%95%84%EB%8F%84%20%EB%90%98%EB%8A%94%20%EA%B2%BD%EC%9A%B0:)
- [7. 성능 팁](#7.%20%EC%84%B1%EB%8A%A5%20%ED%8C%81)
- [8. 결론](#8.%20%EA%B2%B0%EB%A1%A0)
	- [기억할 점:](#%EA%B8%B0%EC%96%B5%ED%95%A0%20%EC%A0%90:)


## 1. ConcurrentHashMap이란?

ConcurrentHashMap은 여러 스레드가 동시에 접근해도 안전하게 데이터를 저장하고 조회할 수 있는 Map 컬렉션입니다. 일반 HashMap과 비슷하지만, 멀티스레드 환경에서 사용할 수 있도록 특별히 설계되었습니다.

### 1.1 간단한 비유로 이해하기

일반 HashMap을 카페의 메뉴판이라고 생각해봅시다.
- HashMap: 한 명의 직원만 메뉴판을 수정할 수 있는 상황
- ConcurrentHashMap: 여러 직원이 동시에 각자 담당하는 메뉴를 수정할 수 있는 상황

## 2. 기본적인 사용법

### 2.1 생성하기
```java
// 기본 생성
ConcurrentHashMap<String, Integer> scores = new ConcurrentHashMap<>();

// 초기 용량 지정
ConcurrentHashMap<String, Integer> scores = new ConcurrentHashMap<>(100);

// 초기값 지정
Map<String, Integer> initialScores = Map.of("Kim", 95, "Lee", 85);
ConcurrentHashMap<String, Integer> scores = new ConcurrentHashMap<>(initialScores);
```

### 2.2 기본 작업
```java
public class ScoreManager {
    private ConcurrentHashMap<String, Integer> studentScores = new ConcurrentHashMap<>();
    
    // 점수 추가
    public void addScore(String studentName, int score) {
        studentScores.put(studentName, score);
    }
    
    // 점수 조회
    public Integer getScore(String studentName) {
        return studentScores.get(studentName);
    }
    
    // 점수가 없을 때만 추가
    public void addScoreIfAbsent(String studentName, int score) {
        studentScores.putIfAbsent(studentName, score);
    }
    
    // 점수 수정
    public void updateScore(String studentName, int newScore) {
        studentScores.replace(studentName, newScore);
    }
    
    // 점수 삭제
    public void removeScore(String studentName) {
        studentScores.remove(studentName);
    }
}
```

## 3. 실제 사용 예제

### 3.1 간단한 캐시 구현
```java
public class SimpleCache {
    private final ConcurrentHashMap<String, String> cache = new ConcurrentHashMap<>();
    
    // 데이터 가져오기 (없으면 계산해서 저장)
    public String getData(String key) {
        // 캐시에 없으면 새로 계산해서 저장
        return cache.computeIfAbsent(key, k -> {
            System.out.println("데이터 계산 중...");
            return "계산된 데이터: " + k;
        });
    }
    
    // 데이터 저장
    public void putData(String key, String value) {
        cache.put(key, value);
    }
    
    // 데이터 삭제
    public void removeData(String key) {
        cache.remove(key);
    }
}
```

### 3.2 온라인 사용자 관리
```java
public class OnlineUserManager {
    private final ConcurrentHashMap<String, UserSession> onlineUsers = new ConcurrentHashMap<>();
    
    // 사용자 세션 정보
    @Data
    @AllArgsConstructor
    public static class UserSession {
        private String userId;
        private LocalDateTime loginTime;
        private String sessionId;
    }
    
    // 사용자 로그인
    public void userLogin(String userId, String sessionId) {
        UserSession session = new UserSession(userId, LocalDateTime.now(), sessionId);
        onlineUsers.put(userId, session);
        System.out.println("사용자 로그인: " + userId);
    }
    
    // 사용자 로그아웃
    public void userLogout(String userId) {
        onlineUsers.remove(userId);
        System.out.println("사용자 로그아웃: " + userId);
    }
    
    // 온라인 사용자 수 확인
    public int getOnlineUserCount() {
        return onlineUsers.size();
    }
    
    // 특정 사용자가 온라인인지 확인
    public boolean isUserOnline(String userId) {
        return onlineUsers.containsKey(userId);
    }
}
```

## 4. 주의할 점과 팁

### 4.1 Null 사용 불가
```java
public class NullExample {
    public static void main(String[] args) {
        ConcurrentHashMap<String, String> map = new ConcurrentHashMap<>();
        
        // 이렇게 하면 NullPointerException 발생!
        try {
            map.put(null, "value");  // 에러!
            map.put("key", null);    // 에러!
        } catch (NullPointerException e) {
            System.out.println("Null은 사용할 수 없습니다!");
        }
        
        // 대신 이렇게 사용하세요
        map.put("noData", "");  // 빈 문자열 사용
        // 또는 Optional 사용
        Optional.ofNullable(map.get("key")).orElse("기본값");
    }
}
```

### 4.2 안전한 복합 연산
```java
public class SafeOperationExample {
    private final ConcurrentHashMap<String, Integer> counter = new ConcurrentHashMap<>();
    
    // 좋지 않은 방법 (일관성 문제 가능성)
    public void badIncrement(String key) {
        Integer value = counter.get(key);
        if (value == null) {
            counter.put(key, 1);
        } else {
            counter.put(key, value + 1);  // 레이스 컨디션 가능성!
        }
    }
    
    // 좋은 방법
    public void goodIncrement(String key) {
        counter.compute(key, (k, v) -> v == null ? 1 : v + 1);
        // 또는
        counter.merge(key, 1, Integer::sum);
    }
}
```

## 5. 실전 활용 예제: 간단한 방문자 카운터

```java
public class VisitorCounter {
    private final ConcurrentHashMap<String, AtomicInteger> pageViews = new ConcurrentHashMap<>();
    
    // 페이지 조회수 증가
    public void incrementPageView(String page) {
        pageViews.computeIfAbsent(page, k -> new AtomicInteger(0))
                 .incrementAndGet();
    }
    
    // 특정 페이지의 조회수 조회
    public int getPageViews(String page) {
        return pageViews.getOrDefault(page, new AtomicInteger(0)).get();
    }
    
    // 모든 페이지의 총 조회수 계산
    public int getTotalPageViews() {
        return pageViews.values().stream()
                       .mapToInt(AtomicInteger::get)
                       .sum();
    }
    
    // 가장 많이 본 페이지 찾기
    public String getMostViewedPage() {
        return pageViews.entrySet().stream()
                       .max(Map.Entry.comparingByValue(Comparator.comparingInt(AtomicInteger::get)))
                       .map(Map.Entry::getKey)
                       .orElse("No pages viewed");
    }
}
```

## 6. 언제 ConcurrentHashMap을 사용해야 할까?

### 사용해야 하는 경우:
1. 여러 스레드가 동시에 데이터를 읽고 쓰는 경우
2. 캐시를 구현할 때
3. 세션 관리할 때
4. 실시간 카운터나 통계를 관리할 때

### 사용하지 않아도 되는 경우:
1. 단일 스레드 환경에서는 HashMap 사용
2. 읽기 전용 데이터는 불변 컬렉션 사용
3. 동시성이 필요 없는 간단한 데이터 저장

## 7. 성능 팁

```java
public class PerformanceTips {
    // 초기 용량을 적절히 설정하여 리사이징 최소화
    private final ConcurrentHashMap<String, String> map = 
        new ConcurrentHashMap<>(16, 0.75f, 16);
        
    // 배치 작업은 한번에 처리
    public void batchUpdate(Map<String, String> updates) {
        map.putAll(updates);  // 한 번에 여러 데이터 업데이트
    }
    
    // 자주 접근하는 데이터는 로컬 변수에 캐싱
    public String frequentAccess(String key) {
        String value = map.get(key);  // 한 번만 접근
        // value를 여러 번 사용...
        return value;
    }
}
```

## 8. 결론

ConcurrentHashMap은 멀티스레드 환경에서 안전하게 데이터를 관리할 수 있는 훌륭한 도구입니다. 
처음에는 복잡해 보일 수 있지만, 기본적인 사용법을 익히고 실제 예제를 연습하다 보면 
자연스럽게 활용할 수 있게 됩니다.

### 기억할 점:
- null 값을 사용할 수 없음
- 복합 연산은 compute나 merge 메서드 사용
- 초기 용량은 예상 데이터 크기에 맞게 설정
- 단순 읽기/쓰기는 get/put 메서드로 충분

이 가이드를 통해 ConcurrentHashMap의 기본 개념과 사용법을 이해하셨기를 바랍니다. 
실제 프로젝트에서 연습하면서 점차 고급 기능들도 활용해보세요!
