---
created: 2024-08-17 13:56
updated: 2024-08-17 14:10
---
```mermaid
graph TD 
	A[모바일 앱] -->|API 요청| B(백엔드 서버) 
	B -->|데이터 저장/조회| C[(MySql)] 
	B -->|OCR 요청| D[Google Cloud Vision API] 
	B -->|의약품 정보 요청| E[식품의약안전처 API] 
	F[관리자 웹 페이지] -->|데이터 관리| B
```