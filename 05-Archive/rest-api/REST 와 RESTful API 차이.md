---
created: 2024-10-21 10:08
updated: 2024-11-05 09:22
---
# 1. REST API 개념

REST API(Representational State Transfer API)는 웹 서비스를 위한 아키텍처 스타일을 기반으로 만든 API입니다. REST 아키텍처의 제약조건을 준수하는 애플리케이션 프로그래밍 인터페이스를 의미합니다.

주요 특징:
- 자원(Resource): URI로 표현
- 행위(Verb): HTTP 메서드로 표현 (GET, POST, PUT, DELETE 등)
- 표현(Representation): 자원의 상태를 표현 (주로 JSON, XML 형식)

# 2. RESTful API 개념

RESTful API는 REST 아키텍처의 제약조건을 엄격하게 준수하여 설계된 API를 말합니다. REST의 원칙을 철저히 따르는 시스템을 RESTful하다고 말합니다.

# 3. REST API와 RESTful API의 차이점

1. 준수 정도
   - REST API: REST 아키텍처 스타일을 부분적으로 적용
   - RESTful API: REST 아키텍처 스타일을 엄격하게 준수

2. 설계 철학
   - REST API: 유연한 설계 가능
   - RESTful API: 일관성 있고 예측 가능한 설계 지향

3. 구현 복잡성
   - REST API: 상대적으로 구현이 쉬움
   - RESTful API: 엄격한 규칙으로 인해 구현이 더 복잡할 수 있음

# 4. RESTful API 설계 원칙

1. 자원의 식별
   - URI를 통해 자원을 명확하게 식별해야 함
   
2. 메시지를 통한 리소스 조작
   - HTTP 메서드를 사용하여 자원을 조작

3. 자기 서술적 메시지
   - 각 메시지는 자신을 어떻게 처리해야 하는지에 대한 정보를 포함해야 함

4. 애플리케이션 상태에 대한 엔진으로서 하이퍼미디어
   - 클라이언트가 애플리케이션의 상태를 전이시킬 수 있는 하이퍼링크를 제공해야 함

5. Stateless
   - 각 요청은 독립적이며, 서버는 클라이언트의 상태를 저장하지 않음

# 5. RESTful API 성숙도 모델 (Richardson Maturity Model)

Leonard Richardson은 RESTful API의 성숙도를 4단계로 구분했습니다. 이 모델은 API가 얼마나 RESTful한지를 평가하는 기준이 됩니다.

## 레벨 0: The Swamp of POX (Plain Old XML)
- HTTP를 단순한 전송 메커니즘으로만 사용
- 주로 단일 URI와 POST 메서드만 사용
- SOAP 웹 서비스가 대표적인 예시
```bash 
curl -X POST http://api.example.com/endpoint \ -H "Content-Type: application/xml" \ -d '<request><method>getUser</method><id>123</id></request>'
```

## 레벨 1: 리소스
- 개별 리소스에 대한 개념 도입
- 여러 URI를 사용하지만, HTTP 메서드는 제한적으로 사용 (주로 POST)

예시:
```bash
curl -X POST http://api.example.com/users/123 \
  -H "Content-Type: application/xml" \
  -d '<request><method>getUser</method></request>'
```

## 레벨 2: HTTP 동사
- HTTP 메서드를 의미에 맞게 사용 (GET, POST, PUT, DELETE 등)
- 적절한 HTTP 상태 코드 사용
예시:
```bash
# 사용자 정보 조회
curl -X GET http://api.example.com/users/123

# 새 사용자 생성
curl -X POST http://api.example.com/users \
  -H "Content-Type: application/json" \
  -d '{"name": "John Doe", "email": "john@example.com"}'

# 사용자 정보 수정
curl -X PUT http://api.example.com/users/123 \
  -H "Content-Type: application/json" \
  -d '{"name": "John Updated", "email": "john_updated@example.com"}'

# 사용자 삭제
curl -X DELETE http://api.example.com/users/123
```

## 레벨 3: 하이퍼미디어 컨트롤
- HATEOAS (Hypertext As The Engine Of Application State) 원칙 적용
- 응답에 관련 리소스에 대한 링크 포함
- 클라이언트가 API를 동적으로 탐색 가능

예시:
```bash
# 사용자 정보 조회 (HATEOAS)
curl -X GET http://api.example.com/users/123

# 응답 예시:
# {
#   "id": 123,
#   "name": "John Doe",
#   "email": "john@example.com",
#   "_links": {
#     "self": { "href": "/users/123" },
#     "update": { "href": "/users/123", "method": "PUT" },
#     "delete": { "href": "/users/123", "method": "DELETE" },
#     "posts": { "href": "/users/123/posts" }
#   }
# }

# 관련 리소스 접근 (사용자의 게시물)
curl -X GET http://api.example.com/users/123/posts
```

대부분의 RESTful API는 레벨 2에 해당하며, 이 정도로도 충분히 RESTful하다고 볼 수 있습니다. 레벨 3은 구현의 복잡성 때문에 실제로 적용하기 어려운 경우가 많습니다.

각 레벨별 특징:
- 레벨 0: REST의 원칙을 전혀 따르지 않음
- 레벨 1: 리소스의 개념 도입으로 REST의 기본 개념 적용 시작
- 레벨 2: HTTP 프로토콜의 의미를 제대로 사용하여 REST의 특성 대부분 구현
- 레벨 3: 가장 성숙한 REST API 모델로, 하이퍼미디어를 통한 자기 설명적 API 구현

API를 설계할 때는 프로젝트의 요구사항과 복잡성을 고려하여 적절한 레벨을 선택하는 것이 중요합니다.

# 6. 예제 코드

## REST API 예제 (Spring Boot)

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    @GetMapping
    public List<User> getAllUsers() {
        // 모든 사용자 조회 로직
    }

    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        // 특정 사용자 조회 로직
    }

    @PostMapping
    public User createUser(@RequestBody User user) {
        // 사용자 생성 로직
    }

    @PutMapping("/{id}")
    public User updateUser(@PathVariable Long id, @RequestBody User user) {
        // 사용자 정보 업데이트 로직
    }

    @DeleteMapping("/{id}")
    public void deleteUser(@PathVariable Long id) {
        // 사용자 삭제 로직
    }
}
```

## RESTful API 예제 (Spring Boot)

```java
@RestController
@RequestMapping("/api/v1/users")
public class UserController {

    @GetMapping
    public ResponseEntity<List<User>> getAllUsers() {
        List<User> users = userService.getAllUsers();
        return ResponseEntity.ok(users);
    }

    @GetMapping("/{id}")
    public ResponseEntity<User> getUser(@PathVariable Long id) {
        return userService.getUser(id)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
    }

    @PostMapping
    public ResponseEntity<User> createUser(@Valid @RequestBody User user) {
        User createdUser = userService.createUser(user);
        URI location = ServletUriComponentsBuilder
                .fromCurrentRequest()
                .path("/{id}")
                .buildAndExpand(createdUser.getId())
                .toUri();
        return ResponseEntity.created(location).body(createdUser);
    }

    @PutMapping("/{id}")
    public ResponseEntity<User> updateUser(@PathVariable Long id, @Valid @RequestBody User user) {
        return userService.updateUser(id, user)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        if (userService.deleteUser(id)) {
            return ResponseEntity.noContent().build();
        }
        return ResponseEntity.notFound().build();
    }
}
```

RESTful API 예제에서 주목할 점:
1. 버전 관리 (/api/v1/)
2. ResponseEntity 사용으로 HTTP 상태 코드와 헤더 제어
3. 유효성 검사 (@Valid)
4. 리소스 생성 시 URI 반환
5. 일관된 에러 처리

# 7. 결론

REST API와 RESTful API는 개념적으로 유사하지만, RESTful API가 REST 원칙을 더 엄격하게 따릅니다. RESTful API를 설계하면 일관성 있고 예측 가능한 API를 제공할 수 있지만, 구현의 복잡성이 증가할 수 있습니다. 프로젝트의 요구사항과 팀의 역량을 고려하여 적절한 접근 방식을 선택하는 것이 중요합니다.

Richardson 성숙도 모델은 API의 RESTful 정도를 평가하는 유용한 도구입니다. 대부분의 실용적인 API는 레벨 2에 해당하며, 이는 대부분의 상황에서 충분한 수준의 RESTful 설계를 제공합니다. 레벨 3(HATEOAS)은 이론적으로는 이상적이지만, 실제 구현과 사용에는 상당한 복잡성이 따릅니다.

API를 설계할 때는 RESTful 원칙을 이해하고 적용하되, 프로젝트의 실제 요구사항과 제약 조건을 고려하여 균형 잡힌 접근 방식을 취하는 것이 중요합니다.

# 참조
[[REST API]]