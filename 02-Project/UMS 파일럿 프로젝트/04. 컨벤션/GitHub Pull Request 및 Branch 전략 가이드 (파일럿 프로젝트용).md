---
tags: 
created: 2024-12-20 12:49
modified: 2024-12-20 12:49
related: []
updated: 2024-12-20 13:18
---
## 목차
1. [Branch 전략](#1-branch-전략)
2. [Branch 생성 및 작업 프로세스](#2-branch-생성-및-작업-프로세스)
3. [Pull Request 프로세스](#3-pull-request-프로세스)
4. [코드 리뷰 가이드라인](#4-코드-리뷰-가이드라인)
5. [Merge 규칙](#5-merge-규칙)

## 1. Branch 전략

### 1.1 기본 브랜치 구조
```
main (또는 master)
  └── develop
       ├── feature/*
       ├── bugfix/*
       └── release/*
```

### 1.2 브랜치 명명 규칙
```
feature/[기능명]  # 예: feature/user-registration
bugfix/[버그내용] # 예: bugfix/login-error
release/[버전명]  # 예: release/v1.0.0
```

### 1.3 브랜치별 용도
- **feature**: 새로운 기능 개발
- **bugfix**: 버그 수정
- **release**: 배포 준비

## 2. Branch 생성 및 작업 프로세스

### 2.1 작업 시작 전
```bash
# develop 브랜치 최신화
git checkout develop
git pull origin develop

# 새로운 브랜치 생성
git checkout -b feature/user-registration
```

### 2.2 작업 중 관리
```bash
# develop 변경사항 반영
git checkout develop
git pull origin develop
git checkout feature/user-registration
git rebase develop

# 커밋 메시지 규칙

# 기본 형식
<type>: <description>

# 이슈 관리 시스템 사용 시
<type>: <description> (#issue-number)

# Type 종류
feat: 새로운 기능 추가
fix: 버그 수정
docs: 문서 수정
style: 코드 포맷팅
refactor: 코드 리팩토링
test: 테스트 코드
chore: 기타 변경사항

# 커밋 메시지 예시
feat: 회원가입 기능 구현
feat: 회원가입 기능 구현 (#123)
fix: 로그인 실패 문제 수정 (#124)
docs: README 업데이트
```

## 3. Pull Request 프로세스

### 3.1 PR 작성자의 업무

#### PR 생성 전 준비사항
- [ ] 로컬 테스트 완료
- [ ] 코드 컨벤션 준수 확인
- [ ] 불필요한 코드/주석/로그 제거

#### PR 작성 가이드
- PR 제목: "[작업 타입] 작업 내용"
  - 예: "[Feat] 회원가입 기능 구현"
  - 예: "[Fix] 로그인 실패 문제 수정"

#### PR 템플릿
```markdown
## 작업 내용
- 구현한 기능 또는 수정사항 설명

## 변경사항
- 주요 변경사항 리스트
- UI 변경시 스크린샷 첨부

## 테스트
- [ ] 로컬 테스트 완료
- [ ] 테스트 방법 (필요시 기재)

## 기타
- 리뷰어가 알아야 할 내용
```

## 4. 코드 리뷰 가이드라인

### 4.1 리뷰어
- 24시간 이내 리뷰 시작
- 명확하고 구체적인 피드백 제공
- 피드백 예시:
  - "이 부분은 이런 방식으로 개선하면 좋을 것 같습니다..."
  - "이 로직이 [상황]에서도 정상 동작하나요?"

### 4.2 PR 작성자
- 리뷰어 피드백 빠른 확인
- 모든 코멘트에 응답
- 수정 후 리뷰어에게 재검토 요청

### 4.3 효과적인 커뮤니케이션
- 복잡한 로직은 설명 추가
- 급한 리뷰 필요시 사유 명시
- 의견 차이는 팀 논의를 통해 해결

## 5. Merge 규칙

### 5.1 Merge 조건
- [ ] 모든 리뷰 코멘트 해결
- [ ] 최소 1명 이상의 승인
- [ ] 로컬 테스트 통과

### 5.2 브랜치별 Merge 정책
- **feature**: develop 브랜치로 merge
- **bugfix**: develop 브랜치로 merge
- **release**: main으로 merge

### 5.3 Merge 방식
- Squash and Merge 사용
  - 커밋 메시지: "[작업 타입] 작업 내용 요약"
  - 이슈 관리 시스템 사용 시: "[작업 타입] 작업 내용 요약 (#이슈번호)"
- Merge 후 작업 브랜치 삭제

### 5.4 Release 브랜치 관리

#### Release 브랜치 생성 시점
- 계획된 릴리스 일정 전 (예: 릴리스 2주 전)
- develop 브랜치의 기능이 충분히 안정화되었을 때

#### Release 브랜치 생성 및 관리
```bash
# release 브랜치 생성
git checkout develop
git pull origin develop
git checkout -b release/v1.0.0

# release 브랜치에서 수행할 작업
- 버전 번호 업데이트
- 최종 테스트 진행
- 발견된 버그 수정
- 문서 업데이트

# main 브랜치로 병합
git checkout main
git merge release/v1.0.0
git tag v1.0.0
git push origin main --tags

# develop 브랜치로 백포팅
git checkout develop
git merge release/v1.0.0
git push origin develop
```

#### Release 체크리스트
- [ ] 버전 번호 업데이트 완료
- [ ] 모든 테스트 통과 확인
- [ ] 문서 업데이트 완료
- [ ] 성능 테스트 완료 (필요시)
- [ ] 보안 점검 완료 (필요시)
