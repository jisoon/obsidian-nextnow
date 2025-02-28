---
created: 2025-02-04 18:17
updated: 2025-02-04 18:17
---
# UMS Pilot API 목록

## 1. 템플릿 API (`/apis/msg/tmpl`)

### 1.1 RCS 템플릿
#### RCS 기본 템플릿
- **POST** `/rcs-template/create`
  - 설명: RCS 템플릿 생성
  - 요청: `CreateRcsTemplateDefaultReq`
  - 응답: `CreateTemplateResDto`

#### RCS 메시지
- **POST** `/rcs/lms/create`
  - 설명: RCS LMS 템플릿 생성
  - 요청: `CreateRcsLmsReq`
  - 응답: `CreateTemplateResDto`

- **POST** `/rcs/mms/create`
  - 설명: RCS MMS 템플릿 생성
  - 요청: `CreateRcsMmsReq`
  - 응답: `CreateTemplateResDto`

### 1.2 카카오톡 템플릿
#### 알림톡
- **GET** `/alrim-talk/brand/search`
  - 설명: 알림톡 브랜드 목록 조회
  - 응답: `List<KakaoBrandRes>`

- **POST** `/alrim-talk/template/create`
  - 설명: 알림톡 템플릿 생성
  - 요청: `CreateAlrimTalkReq`
  - 응답: `CreateTemplateResDto`

- **POST** `/alrim-talk/template-id/create`
  - 설명: 알림톡 템플릿 ID 생성
  - 요청: `CreateAlrimTalkTemplateIdReq`
  - 응답: `ResDto`

#### 친구톡
- **POST** `/friend-talk/template/create`
  - 설명: 친구톡 템플릿 생성
  - 요청: `CreateFriendTalkTemplateReq`
  - 응답: `CreateTemplateResDto`

### 1.3 문자 템플릿
- **POST** `/lms/create`
  - 설명: LMS 템플릿 생성
  - 요청: `CreateTemplateDto`
  - 응답: `CreateTemplateResDto`

- **POST** `/mms/create`
  - 설명: MMS 템플릿 생성
  - 요청: `CreateTemplateDto`
  - 응답: `CreateTemplateResDto`

## 2. 이미지 API (`/apis/msg/image`)

### 2.1 이미지 관리
- **GET** `/search`
  - 설명: 이미지 목록 조회
  - 요청: `ImageSearchCondition`
  - 응답: `ResDto` (페이징 처리)

## 3. 파일 API (`/apis/file`)

### 3.1 파일 관리
- **GET** `/{fileName}`
  - 설명: 파일 다운로드
  - 응답: `Resource`

- **POST** `/upload`
  - 설명: 파일 업로드
  - 요청: `MultipartFile`
  - 응답: `ResDto`

- **POST** `/delete`
  - 설명: 파일 삭제
  - 요청: `DeleteFileReq`
  - 응답: `ResDto`

## 4. 업무 코드 API (`/apis/msg/biz`)

### 4.1 업무 코드 관리
- **GET** `/list`
  - 설명: 업무 코드 목록 조회
  - 요청: `BizCodeSearchCondition`
  - 응답: `ResDto`

- **GET** `/detail`
  - 설명: 업무 코드 상세 조회
  - 요청: `BizCodeSearchCondition`
  - 응답: `ResDto`

- **POST** `/create`
  - 설명: 업무 코드 생성
  - 요청: `CreateBizCodeReq`
  - 응답: `ResDto`

## 5. DTO 구조

### 5.1 템플릿 DTO
- **MasterTemplateDto**
  - 템플릿 마스터 정보
  - 그룹사 코드, 템플릿 시퀀스, 버전 등 기본 정보

- **ChannelTemplateDto**
  - 채널별 템플릿 정보
  - 메시지 타입, 내용, 첨부파일 등

### 5.2 요청/응답 DTO
- **CreateTemplateDto**
  - 템플릿 생성 공통 요청 정보
  - 채널별 특화 정보는 상속하여 확장

- **CreateTemplateResDto**
  - 템플릿 생성 결과
  - 생성된 템플릿 ID 및 상태 정보

## 6. API 특징

### 6.1 공통 사항
1. **인증**
   - `@LoginRequired` 어노테이션으로 로그인 필수 체크
   - `SessionUser`를 통한 사용자 정보 접근

2. **응답 형식**
   - `ResDto`를 통한 일관된 응답 포맷
   - 페이징이 필요한 경우 `ResDto.createSuccessPagingRes()` 사용

3. **검증**
   - DTO 레벨의 기본 유효성 검증
   - 서비스 레벨의 비즈니스 규칙 검증

### 6.2 확장성
1. **템플릿 타입**
   - 채널별로 독립적인 컨트롤러 구성
   - 공통 서비스를 통한 기본 기능 제공
   - 채널별 특화 기능은 전용 서비스로 분리

2. **파일 처리**
   - 범용적인 파일 업로드/다운로드 지원
   - 이미지 전용 API 별도 제공

3. **업무 코드**
   - 독립적인 업무 코드 관리 기능
   - 템플릿과의 연계성 유지
