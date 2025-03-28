---
tags: 
created: 2024-11-12 09:34
modified: 2024-11-12 09:34
related: []
updated: 2024-11-12 09:34
---

## 1. SSE란?
서버에서 클라이언트(웹브라우저)로 자동으로 업데이트를 보내는 기술
- **단방향 통신**: 서버 → 클라이언트 
- **실시간 데이터**: 서버의 새로운 데이터를 즉시 수신

## 2. 실생활 비유 
📱 **뉴스 속보 알림 서비스**
1. 사용자(클라이언트)가 뉴스 알림을 신청
2. 방송사(서버)가 새로운 속보 발생시 자동으로 알림 전송
3. 사용자는 별도 요청 없이 새로운 소식을 받아볼 수 있음

## 3. AI 챗봇에서의 활용
### 실시간 스트리밍 답변
```
사용자: "안녕하세요"
AI: "안녕하세요! 
    무엇을...
    도와드릴까요?"
    (글자가 하나씩 실시간으로 나타남)
```

## 4. 주요 장점
- ✨ 자연스러운 사용자 경험
- 🚀 서버 자원의 효율적 사용
- 💻 간단한 구현 방식

## 5. SSE 미사용시
```
사용자: "안녕하세요"
(로딩중...)
(로딩중...)
AI: "안녕하세요! 무엇을 도와드릴까요?" 
    (전체 답변이 한번에 나타남)
```

### 단점
- ⏳ 전체 답변 생성까지 대기 필요
- ❌ 시스템 동작 상태 파악 어려움
- 👎 떨어지는 사용자 경험

## 요약
> SSE는 AI 챗봇에서 "실시간 스트리밍 답변"을 구현하는 핵심 기술입니다.
> ChatGPT, Claude 등 최신 AI 서비스들이 채택하고 있는 표준 기술입니다.
