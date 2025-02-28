---
tags:
  - leasy
  - aws
  - excalidraw
created: 2024-11-11 15:08
modified: 2024-11-11 15:08
related: 
updated: 2024-12-03 15:54
---
### 운영 CloudFront 캐시 무효화
```aws
aws cloudfront create-invalidation --distribution-id E1XW1LK7LPIVIG --paths "/*" --profile skdnd-leasy-prd-cf
```


### 개발 CloudFront 캐시 무효화
```aws
aws cloudfront create-invalidation --distribution-id E3A5WDBGGEPGP4 --paths "/*" --profile skdnd-mass-cf
```

```aws
aws cloudfront create-invalidation --distribution-id E13L0CXOWR8DES --paths "/*" --profile skdnd-mass-cf
```
