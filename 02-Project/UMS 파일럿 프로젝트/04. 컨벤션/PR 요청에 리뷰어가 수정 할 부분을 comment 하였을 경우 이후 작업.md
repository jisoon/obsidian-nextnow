---
tags: 
created: 2024-12-26 17:07
modified: 2024-12-26 17:07
related: []
updated: 2024-12-26 17:08
---
## 1. PR 코멘트 확인
- GitHub에서 해당 PR의 코멘트들을 검토
- 요청된 변경사항들을 정확히 이해
## 2. 로컬 작업 환경 준비
- 해당 PR 브랜치를 최신 상태로 업데이트
```bash
git checkout pr-branch-name
git pull origin pr-branch-name
```

## 3. 코드 수정
- 코멘트에서 지적된 부분들을 수정
- 필요한 경우 테스트 코드 추가/수정

## 4. 변경사항 커밋
```bash
git add .
git commit -m "fix: PR 리뷰 반영"
```

## 5. 수정된 코드 푸시
```bash
git push origin pr-branch-name
```

## 6. PR 업데이트
- GitHub에서 PR 페이지 확인
- 코멘트에 답변 작성 (수정 내용 설명이나 질문에 대한 답변)
- 필요한 경우 "Resolve conversation" 클릭하여 해결된 코멘트 표시

이후 리뷰어가 다시 확인하고 추가 코멘트를 달거나 승인(Approve)을 하게 됩니다.
