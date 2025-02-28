---
tags: 
created: 2024-12-24 08:39
modified: 2024-12-24 08:39
related: []
updated: 2024-12-24 09:04
---
### GIT 두 reposuitorey 동시 push
```
git remote add [name] [repo-url]

#이미 있을 경우
git remote set-url --add --push [name] [repo-url] 

# 동시에 push
git push [name] [branch]

# 실 예제
git remote add all https://github.com/nextnow-kr/ums-pilot.git
git remote set-url --add --push all https://jschoi3@bitbucket.org/odinue/nextnow_ums_2025.git
```


