---
tags:
  - aws
  - cloud-front
created: 2024-10-24 13:45
modified: 2024-10-24 13:45
related: 
updated: 2024-11-05 09:22
---
# AWS CLI CloudFront 캐시 무효화 가이드

## 1. AWS 자격 증명 설정 명령어 설명

```bash
aws configure --profile skdnd-mass-cf
```

이 명령어는 AWS CLI에서 특정 프로필의 자격 증명을 설정하는 명령어입니다.

### 각 입력 항목 설명
- `--profile skdnd-mass-cf`: 새로운 프로필 이름을 지정합니다. 여러 AWS 계정을 관리할 때 유용합니다.
- `AWS Access Key ID`: AWS 계정의 액세스 키 ID
- `AWS Secret Access Key`: AWS 계정의 비밀 액세스 키
- `Default region name`: AWS 서비스를 사용할 기본 리전 (비워둘 수 있음)
- `Default output format`: 명령어 실행 결과의 출력 형식 (json으로 설정)

## 2. CloudFront 캐시 무효화 명령어 설명

```bash
aws cloudfront create-invalidation --distribution-id E3A5WDBGGEPGP4 --paths "/web-link/contract-extend.html" --profile skdnd-mass-cf
```

이 명령어는 CloudFront CDN의 캐시를 무효화하는 명령어입니다.

### 매개변수 설명
- `create-invalidation`: CloudFront의 캐시 무효화를 생성하는 명령어
- `--distribution-id E3A5WDBGGEPGP4`: CloudFront 배포(Distribution)의 고유 식별자
- `--paths "/web-link/contract-extend.html"`: 캐시를 무효화할 파일의 경로
  - 경로는 반드시 슬래시(/)로 시작해야 합니다.
  - 예: "/web-link/contract-extend.html"
- `--profile skdnd-mass-cf`: 사용할 AWS 프로필 이름 (앞서 설정한 프로필)

## 실행 결과 예시 및 설명

```json
{
    "Location": "https://cloudfront.amazonaws.com/2020-05-31/distribution/E3A5WDBGGEPGP4/invalidation/I2FDVMUSRHFGNJQHKMV70L55LC",
    "Invalidation": {
        "Id": "I2FDVMUSRHFGNJQHKMV70L55LC",
        "Status": "InProgress",
        "CreateTime": "2024-10-24T04:36:37.349000+00:00",
        "InvalidationBatch": {
            "Paths": {
                "Quantity": 1,
                "Items": [
                    "/web-link/contract-extend.html"
                ]
            },
            "CallerReference": "cli-1729744594-731103"
        }
    }
}
```

### 결과 항목 설명
- `Location`: 생성된 무효화 요청의 세부 정보를 확인할 수 있는 AWS API 엔드포인트
- `Id`: 무효화 요청의 고유 식별자 (예: I2FDVMUSRHFGNJQHKMV70L55LC)
- `Status`: 무효화 진행 상태
  - `InProgress`: 진행 중
  - `Completed`: 완료됨
- `CreateTime`: 무효화 요청이 생성된 시간 (UTC)
- `InvalidationBatch`: 무효화 요청의 세부 정보
  - `Quantity`: 무효화할 파일 경로의 수
  - `Items`: 무효화할 파일 경로 목록
  - `CallerReference`: AWS 내부적으로 사용하는 참조 ID

## 주의사항
1. 액세스 키와 시크릿 키는 민감한 정보이므로 절대 외부에 노출되지 않도록 주의해야 합니다.
2. 캐시 무효화는 과다 사용시 추가 비용이 발생할 수 있으므로 필요한 경우에만 실행해야 합니다.
3. 프로필 설정 후에는 해당 프로필을 사용하여 다른 AWS CLI 명령어도 실행할 수 있습니다.
4. CloudFront의 경로는 반드시 루트를 나타내는 슬래시(/)로 시작해야 합니다.
5. 무효화 상태가 `InProgress`인 경우, 완전한 적용까지 몇 분이 소요될 수 있습니다.

## 일반적인 사용 시나리오
- 웹사이트 콘텐츠를 업데이트한 후 사용자들이 최신 버전을 볼 수 있도록 할 때
- 잘못된 콘텐츠가 캐시된 경우 이를 즉시 제거하고자 할 때
- 정기적인 콘텐츠 업데이트 후 캐시 갱신이 필요할 때
