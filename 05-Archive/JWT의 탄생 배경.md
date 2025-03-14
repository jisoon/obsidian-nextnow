---
created: 2024-10-21 12:31
updated: 2024-10-21 12:35
---
## JWT의 탄생 배경

### 1. 웹 애플리케이션의 진화

웹 애플리케이션이 발전하면서 단순한 서버-클라이언트 모델에서 벗어나 복잡한 분산 시스템으로 진화했습니다. 이에 따라 기존의 인증 방식으로는 새로운 환경에 적합하지 않은 문제가 발생했습니다.

### 2. 세션 기반 인증의 한계

전통적인 세션 기반 인증 방식은 다음과 같은 한계점을 가지고 있었습니다:

- 확장성 문제: 서버 측에서 세션 정보를 저장해야 해서 분산 환경에서 관리가 어려웠습니다.
- 성능 이슈: 사용자가 늘어날수록 서버의 메모리 부담이 커졌습니다.
- CORS(Cross-Origin Resource Sharing) 문제: 다른 도메인 간 인증 정보 공유가 어려웠습니다.

### 3. RESTful API의 등장

RESTful API가 널리 사용되면서 무상태(Stateless) 통신의 중요성이 커졌습니다. 세션 기반 인증은 상태를 유지해야 하므로 RESTful 원칙과 맞지 않았습니다.

### 4. 모바일 애플리케이션의 증가

모바일 앱의 보편화로 인해 다양한 플랫폼에서 동작하는 인증 시스템의 필요성이 대두되었습니다.

### 5. 마이크로서비스 아키텍처의 도입

마이크로서비스 아키텍처가 인기를 얻으면서 서비스 간 인증 및 권한 부여를 효율적으로 처리할 수 있는 방법이 필요해졌습니다.

## JWT의 등장

이러한 배경에서 JWT가 다음과 같은 장점을 가지고 등장했습니다:
### 1. 무상태성 (Stateless)
- 서버가 클라이언트의 상태를 저장할 필요가 없어 확장성이 향상되었습니다.

### 2. 자가 수용적 (Self-contained)
- 토큰 자체에 필요한 모든 정보가 포함되어 있어 별도의 조회 과정이 필요 없습니다.

### 3. 확장성 (Scalability)
- 분산 시스템과 마이크로서비스 환경에서 효과적으로 작동합니다.

### 4. 보안성
- 서명을 통해 토큰의 무결성을 보장합니다.

### 5. 범용성
- JSON 형식을 사용하여 다양한 프로그래밍 언어와 플랫폼에서 쉽게 사용할 수 있습니다.

### 6. 효율성
- 작은 크기로 인해 네트워크 오버헤드가 적습니다.

## JWT 표준화

JWT는 2015년 5월에 RFC 7519로 표준화되었습니다. 이를 통해 다양한 시스템과 서비스 간의 상호 운용성이 크게 향상되었습니다.

## JWT의 영향

JWT의 등장으로 인해 다음과 같은 변화가 일어났습니다:

1. 싱글 사인온(SSO) 구현 용이성 증가
2. API 보안의 표준화
3. 클라우드 서비스와의 통합 간소화
4. IoT 디바이스 인증의 효율성 향상

## 결론

JWT는 현대적인 웹 애플리케이션의 복잡한 요구사항을 충족시키기 위해 탄생했습니다. 기존 인증 방식의 한계를 극복하고, 분산 환경에서의 효율적인 인증 및 정보 교환을 가능하게 했습니다. 그 결과 오늘날 많은 기업과 개발자들이 JWT를 선택하고 있으며, 웹 개발의 중요한 도구로 자리잡았습니다.

JWT는 계속 발전하고 있으며, 보안과 효율성을 더욱 높이기 위한 연구와 개선이 이루어지고 있습니다. 개발자들은 JWT의 장단점을 이해하고 적절히 활용함으로써 더 안전하고 효율적인 애플리케이션을 구축할 수 있습니다.