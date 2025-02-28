---
tags:
  - 보안
  - 가이드
created: 2024-10-23 17:50
modified: 2024-10-23 17:50
related:
  - "[[VNTG]]"
updated: 2024-10-24 13:48
---
OWASP(Open Web Application Security Project)는 웹 애플리케이션 보안을 개선하기 위한 전 세계적인 비영리 단체입니다. OWASP에서 제공하는 보안 가이드라인은 웹 애플리케이션 개발 시 고려해야 할 주요 보안 위협과 이에 대한 대응 방안을 제시합니다.

OWASP Top 10은 가장 중요한 웹 애플리케이션 보안 위험 요소를 정리한 문서입니다. 2021년 기준 OWASP Top 10은 다음과 같습니다:

1. 접근 통제 취약점 (Broken Access Control)
   - 인증되지 않은 기능 접근
   - 다른 사용자의 데이터 접근
   - 권한 상승
   - API 권한 검증 우회

2. 암호화 실패 (Cryptographic Failures)
   - 중요 데이터 암호화 미흡
   - 취약한 암호화 알고리즘 사용
   - 데이터 전송 시 암호화 미적용

3. 인젝션 (Injection)
   - SQL 인젝션
   - NoSQL 인젝션
   - LDAP 인젝션
   - OS 명령어 인젝션

4. 불안전한 설계 (Insecure Design)
   - 비즈니스 로직 결함
   - 보안 설계 미흡
   - 중요 기능 검증 부재

5. 보안 설정 오류 (Security Misconfiguration)
   - 기본 계정/패스워드 미변경
   - 불필요한 기능 활성화
   - 최신 보안 패치 미적용
   - 에러 메시지의 과도한 정보 노출

6. 취약하고 오래된 구성요소 (Vulnerable and Outdated Components)
   - 취약점이 있는 라이브러리 사용
   - 지원 종료된 소프트웨어 사용
   - 보안 패치 미적용

7. 인증 및 식별 실패 (Identification and Authentication Failures)
   - 취약한 패스워드 정책
   - 세션 관리 미흡
   - 다중 인증 부재

8. 소프트웨어 및 데이터 무결성 실패 (Software and Data Integrity Failures)
   - 무결성 검증 미흡
   - 악성 업데이트 
   - CI/CD 파이프라인 보안 미흡

9. 보안 로깅 및 모니터링 실패 (Security Logging and Monitoring Failures)
   - 보안 이벤트 감지 실패
   - 로그 기록 미흡
   - 모니터링 부재

10. 서버 사이드 요청 위조 (Server-Side Request Forgery)
    - 내부 서버 접근 취약점
    - 신뢰할 수 없는 URL 요청
    - 네트워크 경계 보안 미흡

이러한 보안 위협에 대응하기 위한 주요 구현 사항:

1. 인증/인가
   ```java
   // Spring Security 설정 예시
   @Configuration
   @EnableWebSecurity
   public class SecurityConfig extends WebSecurityConfigurerAdapter {
       @Override
       protected void configure(HttpSecurity http) throws Exception {
           http
               .authorizeRequests()
                   .antMatchers("/api/public/**").permitAll()
                   .antMatchers("/api/admin/**").hasRole("ADMIN")
                   .anyRequest().authenticated()
               .and()
               .csrf().disable()
               .sessionManagement()
                   .sessionCreationPolicy(SessionCreationPolicy.STATELESS);
       }
   }
   ```

2. XSS 방지
   ```java
   // 입력 값 검증
   public class XSSFilter {
       public String clean(String value) {
           return Jsoup.clean(value, Whitelist.basic());
       }
   }
   ```

3. SQL 인젝션 방지
   ```java
   // JPA 사용으로 SQL 인젝션 방지
   @Repository
   public interface UserRepository extends JpaRepository<User, Long> {
       @Query("SELECT u FROM User u WHERE u.username = :username")
       User findByUsername(@Param("username") String username);
   }
   ```

4. 보안 헤더 설정
   ```java
   // 보안 헤더 설정
   @Configuration
   public class WebConfig implements WebMvcConfigurer {
       @Override
       public void addInterceptors(InterceptorRegistry registry) {
           registry.addInterceptor(new SecurityHeadersInterceptor());
       }
   }
   ```

이러한 OWASP 가이드라인을 준수함으로써:
- 보안 취약점 최소화
- 안전한 사용자 데이터 보호
- 시스템 무결성 유지
- 보안 감사 대응 용이

웹 애플리케이션 개발 시 이러한 보안 가이드라인을 참고하여 설계 단계부터 보안을 고려하는 것이 중요합니다.
