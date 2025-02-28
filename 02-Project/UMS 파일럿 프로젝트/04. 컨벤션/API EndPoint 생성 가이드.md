---
tags: 
created: 2024-12-17 17:59
modified: 2024-12-17 17:59
related: []
updated: 2024-12-18 10:12
---
  
## 1. 기본 원칙

### 목록 조회
URI 끝에 'list'를 붙여 사용합니다.
- 예: `/user/list`, `/message/list`, `/order/list`

### 상세 조회 
URI 끝에 'detail'을 붙이고 ID를 파라미터로 전달합니다.
- 예: `/users/detail?userId=123`, `/products/detail?productId=123`

### 데이터 생성
URI 끝에 'create'를 붙여 사용합니다.
- 예: `/user/create`, `/message/create`, `/order/create`

### 데이터 수정
URI 끝에 'update'를 붙여 사용합니다.
- 예: `/user/update`, `/message/update`, `/order/update`

### 데이터 삭제
URI 끝에 'delete'를 붙여 사용합니다.
- 예: `/user/delete`, `/message/delete`, `/order/delete`

## 2. HTTP Request 메소드 사용 원칙

### 사용 가능 메소드

#### GET
데이터 조회 시 사용합니다.
- 목록 조회, 상세 정보 조회 등 데이터를 요청하는 모든 경우에 사용

#### POST 
데이터 변경이 필요한 모든 요청에 사용합니다.
- 새로운 데이터 생성
- 기존 데이터 수정 
- 데이터 삭제

※ PUT, DELETE 등 다른 HTTP 메소드는 사용하지 않습니다.

## 3. CRUD 동작 원칙 예시

### 게시글(Article) API 

#### 목록 조회
```
GET  /article/list?category=ALL
```
전체 게시글 목록을 조회합니다. (Request Parameter 로 데이터 전달)

#### 게시글 생성
```
POST  /article/create
```
새로운 게시글을 생성합니다. (Request Body로 데이터 전달)

#### 상세 조회
```
GET  /article/detail?id=1234
```
특정 게시글의 상세 정보를 조회합니다. (Request Parameter 로 데이터 전달)

#### 게시글 수정
```
POST  /article/update
```
게시글을 수정합니다. (Request Body로 데이터 전달)

#### 게시글 삭제
```
POST  /article/delete
```
게시글을 삭제합니다. (Request Body로 데이터 전달)
