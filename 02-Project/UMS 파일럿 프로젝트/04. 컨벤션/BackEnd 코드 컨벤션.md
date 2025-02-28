---
tags: 
created: 2024-12-18 08:24
modified: 2024-12-18 08:24
related: []
updated: 2024-12-18 10:22
---
# 1. 소개  
  
이 가이드는 일관성 있고 명확한 명명 규칙을 제시합니다.  
  
우리는 명사와 동사를 적절히 조합하는 '혼합 접근법'을 사용함으로써 코드의 가독성과 유지보수성을 향상시키고자 합니다.  
  
# 2. 기본 원칙  
  
1. 명확성: 이름만으로도 해당 컴포넌트의 목적을 이해할 수 있어야 합니다.  
2. 일관성: 전체 프로젝트에서 동일한 패턴을 유지해야 합니다.  
3. 간결성: 불필요한 단어는 제거하고, 핵심만 남깁니다.  
4. 도메인 중심: 주요 비즈니스 개념을 이름에 반영합니다.  
  
# 3. 명명 규칙  
  
## 3.1. 기본 명명 규칙  
  
### 3.1.1. 클래스 명명 규칙: 파스칼 케이스  
  
파스칼 케이스(PascalCase)는 프로그래밍에서 사용되는 네이밍 컨벤션 중 하나입니다.  
  
주요 특징:  
  
1. 모든 단어의 첫 글자를 대문자로 표기  
2. 단어 사이에 공백이나 특수문자를 사용하지 않음  
3. 첫 단어도 대문자로 시작  
  
예시:  
  
- `FirstName`  
- `LastName`  
- `StudentInformation`  
- `UserAccountDetails`  
  
### 3.1.2. 메소드 명명규칙: 카멜 케이스  
  
카멜 케이스(camelCase)는 프로그래밍에서 널리 사용되는 네이밍 컨벤션입니다.  
  
주요 특징:  
  
1. 첫 단어는 소문자로 시작  
2. 그 이후의 단어들은 대문자로 시작  
3. 단어 사이에 공백이나 특수문자를 사용하지 않음  
  
예시:  
  
- `firstName`  
- `lastName`  
- `userEmail`  
- `backgroundColor`  
- `isActive`  
- `calculateTotalPrice`  
  
### 3.1.3. 변수 명명 규칙: 카멜케이스  
  
### 3.1.4. 상수 변수 명명 규칙: 대문자 스네이크 케이스  
  
대문자 스네이크 케이스(UPPER_SNAKE_CASE 또는 SCREAMING_SNAKE_CASE)는 스네이크 케이스의 변형으로, 모든 문자를 대문자로 표기하는 네이밍 컨벤션입니다.  
  
주요 특징:  
  
1. 모든 글자는 대문자 사용  
2. 단어와 단어 사이는 언더스코어(_)로 구분  
3. 공백이나 다른 특수문자는 사용하지 않음  
  
예시:  
  
- `MAX_VALUE`  
- `DATABASE_URL`  
- `API_KEY`  
- `CONSTANT_VALUE`  
- `HTTP_STATUS_CODE`  
- `DEFAULT_TIMEOUT_SECONDS`  
  
### 3.1.5 그 밖에  
  
- 팀원들간 협의 되지 않은 약어는 사용 하지 않는다.  
  
## 3.2. 컨트롤러  
  
- 명사 + "Controller"로 명명  
- 예: `UserController`, `ProductController`  
  
## 3.3. 메서드  
  
- HTTP 메서드에 해당하는 동사 + 명사(도메인 개념)  
- get(조회), create(생성), update(수정), delete(삭제)  
- 외부 라이브러리에 이미 명시된 메소드 명칭은 적용 대상이 아닙니다.  
- 예: `getUser`,  `getUserDetail`, `createOrder`, `updateProfile`, `deleteComment`  
  
## 3.4. DTO (Data Transfer Object)  
  
**Java 16 이상을 사용하는 프로젝트라면 Record Type 도 고려 할 수 있습니다.([Java Record Type](https://www.notion.so/Java-Record-Type-2657dbed09ca41b792883594e7271438?pvs=21))**  
  
- Request 및 Response 에 Map 을 이용하지 않는다.  
- 요청(Request) DTO: 동사 + 명사 + "Req"  
  - 예: `CreateUserReq`, `UpdateOrderReq`  
- 응답(Response) DTO: 동사 + 명사 + "Res"  
  - 예: `CreateUserRes`,  `UpdateOrderRes`  
- 검색 조건 DTO: 명사 + 선택한 검색 조건 용어  
  - 검색 조건 용어는 각 프로젝트 별로 하나로 정의해서 사용  
  - 예: `UserSearchCriteria`, `OrderFilterParameters` , UserSearchCondition  
  
# 4. 추가 고려사항  
  
1. 버전 관리: API 버전을 URL에 포함시키세요. (예: `/api/v1/...`)  
2. 일관된 응답 형식: `ApiResponse<T>`와 같은 래퍼 클래스를 사용하여 일관된 응답 구조를 유지하세요.  
3. 유효성 검사: `@Valid` 어노테이션을 사용하여 입력 데이터의 유효성을 검사하세요.  
4. 페이지네이션: 목록 조회 API에는 페이지네이션을 적용하세요.  
5. 예외 처리: 통일된 예외 처리 메커니즘을 사용하세요.  
6. 문서화: Swagger나 Spring REST Docs를 사용하여 API를 문서화하세요.  
7. 테스트: 단위 테스트와 통합 테스트를 작성하여 API의 정확성을 보장하세요.