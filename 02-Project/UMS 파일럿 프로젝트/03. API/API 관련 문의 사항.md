---
tags: 
created: 2024-12-17 22:29
modified: 2024-12-17 22:29
related: []
updated: 2024-12-18 08:57
---
### BackEnd API URI 관련
Q: 광주 및 신한 은행의 경우 Restful API URI 형태는 아닌것 같습니다. 
조회 요청도 post 로 되어 있는데 아마 금융 프로젝트는 get, post 만 사용 할 수 있어서 그런건 아닌지요. 
파일럿 프로젝트는 광주은행과 동일하게 post 메소드만 허용하여 진행을 할지, Restful API 구현을 할지 
검토 및 의견 부탁 드립니다.
> 참고로 신한은행은 @RequestMapping 어노테이션으로 명시가 되어있어 Application 에서 메소드 제한은 없습니다.

### pagingQuery 관련 
Q: pagingQuery 부분을 찾을 수가 없는데 어느 부분을 보면 되는지 확인 부탁 드립니다.
```
<#return pagingQuery(query, reqData) />
```
