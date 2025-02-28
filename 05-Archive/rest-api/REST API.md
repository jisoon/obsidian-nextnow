---
created: 2024-10-21 09:49
updated: 2024-11-05 09:22
---
# 1. REST란

REST(Representational State Transfer)는 웹 서비스를 위한 아키텍처 스타일입니다. '표현적 상태 전이'로 직역할 수 있습니다.
REST는 클라이언트가 서버의 리소스(자원)에 접근하는 방식을 정의한 아키텍처 원칙의 집합입니다.
REST 아키텍처 원칙을 따라 구현된 API를 REST API라고 합니다.

## 1.1. 왜 REST 스타일을 따라야 하나요?

기존의 클라이언트와 API 간 HTTP 통신은 정확한 규격이 없어 API를 구현하는 개발자에 따라 API 형식이 달랐습니다.

예:
- A 개발자: 사용자 목록 조회 GET http://api.url/getUserList
- B 개발자: 사용자 상세 조회 GET http://api.url/user/getDetail

이로 인해 프론트엔드 개발자는 항상 API 문서를 참고해야 했고, 의미가 중복되는 API 경로로 인해 혼란이 가중되었습니다.

웹의 창시자 중 한 사람인 Roy Fielding(로이 필딩)은 웹의 본래 설계의 장점을 충분히 활용하지 못하고 있다고 판단하여 2000년에 REST에 관한 박사 논문을 발표했습니다. 이후 REST는 웹 서비스 설계의 표준으로 널리 사용되고 있습니다.

## 1.2. Roy Fielding이 제시한 6가지 제약 조건

Fielding은 REST API가 준수해야 할 [6가지 주요 제약 조건](https://gorilla-ohgiraffers.tistory.com/3)을 제시했습니다.
1. **인터페이스 일관성**: 클라이언트가 API를 일관된 방식으로 사용할 수 있어야 합니다.
2. **클라이언트-서버 분리**: 클라이언트와 서버 애플리케이션은 완전히 독립적이어야 합니다[
3. **무상태(Stateless)**: 각 요청에는 처리에 필요한 모든 정보가 포함되어야 하며, 서버는 클라이언트의 상태를 저장하지 않습니다.
4. **캐시 가능성**: 가능한 경우 리소스를 캐시할 수 있어야 합니다.
5. **계층화 시스템**: API 호출과 응답은 여러 계층을 거칠 수 있으며, 클라이언트와 서버는 중간 계층의 존재를 알 필요가 없습니다.
6. **Code on Demand(선택사항)**: 서버는 필요시 실행 가능한 코드를 클라이언트에 전송할 수 있습니다

# 2. REST의 핵심 개념: 자원, 행위, 표현

## 2.1. 자원(Resource)
- REST에서 모든 것은 리소스입니다. 리소스는 서버에 존재하는 데이터나 서비스를 나타냅니다.
- 각 리소스는 고유한 URI(Uniform Resource Identifier)로 식별됩니다.
- 예: 사용자 정보, 게시글, 댓글 등
## 2.2. 행위(Action)
- HTTP 메서드를 사용하여 리소스에 대한 행위를 정의합니다.
- 주요 HTTP 메서드:
  - GET: 리소스 조회
  - POST: 새 리소스 생성
  - PUT: 리소스 전체 수정
  - PATCH: 리소스 일부 수정
  - DELETE: 리소스 삭제
## 2.3. 표현(Representation)
- 클라이언트와 서버가 데이터를 주고받는 형태입니다.
- 일반적으로 JSON, XML 등의 형식을 사용합니다.

# 3. Sample Code

## 3.1. 사용자 정보를 다루는 간단한 REST API 예제:

```java
import org.sprin
gframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/users")
public class UserController {

    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        // 사용자 조회 로직
        return new User(id, "홍길동", "hong@example.com");
    }

    @PostMapping
    public User createUser(@RequestBody User user) {
        // 사용자 생성 로직
        return user;
    }

    @PutMapping("/{id}")
    public User updateUser(@PathVariable Long id, @RequestBody User user) {
        // 사용자 수정 로직
        user.setId(id);
        return user;
    }

    @DeleteMapping("/{id}")
    public void deleteUser(@PathVariable Long id) {
        // 사용자 삭제 로직
    }
}

class User {
    private Long id;
    private String name;
    private String email;

    // 생성자, getter, setter 생략
}
```

이 예제는 사용자 정보에 대한 CRUD(Create, Read, Update, Delete) 작업을 수행하는 REST API를 보여줍니다. 각 엔드포인트는 적절한 HTTP 메서드를 사용하여 RESTful 원칙을 따릅니다.

# 4. REST 방식의 주요 한계점과 해결 방법:

1. 과도한 데이터 전송 또는 부족한 데이터 전송
   - 문제: REST API는 고정된 형태의 데이터를 주고받습니다. 이 때문에 필요 이상의 데이터를 받거나(과도한 전송), 필요한 데이터가 부족할 수 있습니다(부족한 전송).
   - 해결책: GraphQL을 사용하면 클라이언트가 필요한 데이터만 정확히 요청할 수 있어 이 문제를 해결할 수 있습니다.

2. 여러 번의 요청 필요
   - 문제: 복잡한 데이터를 가져오려면 여러 번의 API 호출이 필요할 수 있어, 성능이 저하될 수 있습니다.
   - 해결책: GraphQL이나 gRPC를 사용하면 한 번의 요청으로 여러 데이터를 가져올 수 있습니다.

3. 엄격한 데이터 형식 부족
   - 문제: REST API는 주로 JSON을 사용하는데, 이는 엄격한 데이터 구조를 강제하지 않아 오류가 발생할 수 있습니다.
   - 해결책: GraphQL이나 gRPC는 강력한 타입 시스템을 제공하여 이 문제를 해결합니다.

4. 버전 관리의 어려움
   - 문제: API가 변경되면 클라이언트 애플리케이션이 작동하지 않을 수 있습니다.
   - 해결책: HATEOAS를 사용하거나 API 게이트웨이를 도입하여 버전 관리를 더 쉽게 할 수 있습니다.

5. 실시간 기능 제한
   - 문제: REST는 주로 요청-응답 방식이라 실시간 업데이트에 적합하지 않습니다.
   - 해결책: WebSocket을 사용하거나 gRPC의 스트리밍 기능을 활용하면 실시간 통신이 가능합니다.

6. 표준화 부족
   - 문제: REST에는 엄격한 표준이 없어 API마다 구현 방식이 다를 수 있습니다.
   - 해결책: OpenAPI(Swagger)나 JSON:API 같은 명세를 사용하여 일관성을 높일 수 있습니다.

이러한 한계점들을 해결하기 위해 다음과 같은 방법들을 고려할 수 있습니다:

1. GraphQL 사용: 필요한 데이터만 정확히 요청할 수 있어 과도한/부족한 데이터 전송 문제를 해결합니다.
2. gRPC 도입: 효율적인 데이터 전송과 강력한 타입 시스템을 제공합니다.
3. WebSocket 활용: 실시간 양방향 통신이 가능해집니다.
4. OpenAPI(Swagger) 사용: API 문서화와 일관성을 개선합니다.
5. HATEOAS 적용: API의 동적 탐색을 가능하게 하여 버전 관리를 돕습니다.
6. Backend for Frontend 패턴 사용: 클라이언트별로 최적화된 API를 제공합니다.
7. API 게이트웨이 도입: API 관리, 보안, 모니터링을 중앙화합니다.

각 프로젝트의 특성과 요구사항에 따라 이러한 해결책들을 적절히 선택하고 적용하면 REST의 한계를 극복하고 더 효과적인 API를 구축할 수 있습니다.

# 5. 결론
- REST API를 설계할 때는 항상 리소스 중심으로 생각하고, 
- 적절한 HTTP 메서드를 사용하여 행위를 표현하는 것이 중요합니다. 
- 또한, 명확하고 일관된 URL 구조를 유지하고, 
- 적절한 HTTP 상태 코드를 반환하여 클라이언트가 쉽게 이해할 수 있도록 해야 합니다.