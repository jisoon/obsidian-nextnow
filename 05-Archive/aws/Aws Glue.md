---
created: 2024-09-09 21:57
updated: 2024-11-05 09:22
---
# AWS Glue 소개 및 학습 가이드
대표님에 의견을 드리기 위해 정리한 문서인데.. 어떤 의견을 드려야 하는지...
우리가 할 수 있는지??? 

ETL 데이터 통합 프로세스
Extract
Transform
Load

데이터 카탈로그, Job, Crawler

파이썬, 스칼로 코드로 job 생성

카탈로그
데이터를 쉽게 찾고 관리 해주는
크롤러 자동으로 데이터 스키마 찾아서 저장
자동 파티션 가능

AWS athena ??? S3 전용 조회 서비스??
AWS redshift ???
AWS EMR ??
AWS Glue Studio?? >> 코드가 아닌 Web Builder? 코드를 작성하지 않고 Glue ETL 구현
- 3 Component : Source, Transform, Target
BI Tool ??


크롤러 데이터 카탈로그 변경 감지
카탈로그는 파일이나 정보에에 대한... 메타 정보?

AWS Glue ETL 서비스
Job 스크립트 생성, 실행을 도와주는 서비스

Dev Endpoint 로 IDE 사용 가능?

Job 북마크.. 변경된 것만?


AWS Glue는 데이터 통합을 위한 서버리스 서비스로, 데이터 분석, 기계 학습, 애플리케이션 개발에 필요한 데이터를 쉽게 준비하고 통합할 수 있게 해줍니다.
![[Pasted image 20240910001823.png]]
![[Pasted image 20240910002308.png]]
![[Pasted image 20240910003413.png]]
![[Pasted image 20240910004346.png]]

사용 패턴 

![[Pasted image 20240910005047.png]]



![[Pasted image 20240910005120.png]]

여러 데이터 소스를 조합하여 카탈로그를 만들어서 ETL 할 수 있음

데이터레이크
하나의 저장소에 다양한 파일을 저장한고 다양한 서비스에서 해당 데이터를 사용함

AWS EMR과 AWS Glue는 모두 AWS에서 제공하는 데이터 처리 서비스이지만, 그 목적과 사용 방식에서 차이가 있습니다.

- **AWS EMR (Elastic MapReduce)**:
  - EMR은 Apache Hadoop, Apache Spark와 같은 빅 데이터 프레임워크를 실행하기 위한 관리형 클러스터 플랫폼입니다[5].
  - 주로 대규모 데이터 처리 및 분석을 위해 사용되며, EC2 인스턴스를 기반으로 클러스터를 구성하여 사용자가 직접 관리해야 합니다[2][4].
  - Hadoop 생태계를 활용하여 대용량 데이터를 분산 처리할 수 있으며, 사용자 정의가 가능하여 다양한 오픈 소스 도구와의 통합이 용이합니다[2][4].

- **AWS Glue**:
  - Glue는 서버리스 데이터 통합 서비스로, 데이터 탐색, 준비, 이동 및 통합을 쉽게 할 수 있도록 지원합니다[3][4].
  - 주로 데이터 통합 및 ETL(Extract, Transform, Load) 작업에 사용되며, 서버리스로 운영되어 클러스터 관리가 필요 없습니다[2][3].
  - Glue는 Apache Spark를 지원하며, 데이터 카탈로그 및 크롤러 기능을 통해 다양한 데이터 소스와의 통합을 제공합니다[4].

따라서, EMR은 대규모 빅 데이터 처리에 적합하며, Glue는 데이터 통합 및 ETL 작업에 적합합니다.

출처
[1] [AWS] EMR(hadoop) - Glue 메타 스토어 (HIVE, SPARK) - 코알못 https://co-de.tistory.com/67
[2] AWS GLUE 와 EMR-EMR/MapReduce/Spark - 나도 코딩 할 줄 알아요 https://yanadoo-coding.tistory.com/75
[3] Difference between Amazon Glue and Amazon EMR - AWS re:Post https://repost.aws/ko/questions/QUH_TduNTHTnqBdRr1qCl7Jw/difference-between-amazon-glue-and-amazon-emr
[4] [AWS] AWS EMR과 AWS Glue의 차이 - 작은하마 - 티스토리 https://jin-2130.tistory.com/33
[5] Amazon EMR이란 무엇인가요? - 아마존 EMR https://docs.aws.amazon.com/ko_kr/emr/latest/ManagementGuide/emr-what-is-emr.html
[6] 효율적인 빅데이터 분석 및 처리를 위한 Glue, EMR 활용 - YouTube https://www.youtube.com/watch?v=aavblrrk4Fo
[7] AWS GLUE 와 EMR-/후처리/Glue Studio/Crawler/Data Catalog https://yanadoo-coding.tistory.com/74



## AWS Glue의 주요 특징

1. **서버리스**: 인프라 관리 없이 사용 가능
2. **자동화된 데이터 발견**: 크롤러를 통해 데이터 스키마 자동 감지
3. **ETL(추출, 변환, 로드) 자동화**: 데이터 변환 작업을 쉽게 생성 및 관리
4. **유연한 작업 스케줄링**: 시간 기반 또는 이벤트 기반 트리거 설정 가능

## 주요 개념

### 1. Data Catalog

AWS Glue의 중앙 메타데이터 저장소입니다. 데이터의 위치, 스키마, 데이터 형식 등의 정보를 저장합니다.

### 2. 크롤러(Crawler)

데이터 소스를 스캔하여 스키마를 자동으로 감지하고 Data Catalog에 저장합니다.

### 3. 작업(Job)

ETL 작업을 정의하고 실행하는 단위입니다. Python 또는 Scala로 작성된 스크립트를 사용합니다.

### 4. 트리거(Trigger)

작업을 자동으로 시작하는 메커니즘입니다. 시간 기반 또는 이벤트 기반으로 설정할 수 있습니다.

## 초보 개발자를 위한 학습 방법

1. **AWS 공식 문서 활용**
   - AWS Glue 개발자 가이드를 참조하여 기본 개념과 사용법 학습

2. **실습 중심 학습**
   - AWS 프리 티어를 활용하여 실제 환경에서 AWS Glue 사용해보기
   - 간단한 ETL 작업 생성 및 실행해보기

3. **온라인 튜토리얼 및 동영상**
   - AWS 공식 YouTube 채널의 AWS Glue 관련 동영상 시청
   - Udemy, Coursera 등의 온라인 학습 플랫폼 활용

4. **단계별 접근**
   - Data Catalog 생성 및 크롤러 설정부터 시작
   - 간단한 ETL 작업 생성 후 점진적으로 복잡한 작업으로 확장

5. **커뮤니티 참여**
   - AWS 관련 포럼이나 Stack Overflow에서 질문하고 답변 찾기
   - 지역 AWS 사용자 그룹 모임 참석

6. **샘플 프로젝트 따라하기**
   - AWS 공식 문서나 블로그에서 제공하는 샘플 프로젝트 실습

7. **AWS Glue Studio 활용**
   - 시각적 인터페이스를 통해 ETL 작업을 쉽게 생성하고 관리할 수 있는 AWS Glue Studio 사용해보기

AWS Glue는 강력한 도구이지만, 처음에는 복잡해 보일 수 있습니다. 천천히 단계별로 학습하며, 실제 프로젝트에 적용해보는 것이 가장 효과적인 학습 방법입니다.

Citations:
[1] https://aws.amazon.com/ko/glue/features/
[2] https://docs.aws.amazon.com/ko_kr/prescriptive-guidance/latest/serverless-etl-aws-glue/serverless-etl-aws-glue.pdf
[3] https://docs.aws.amazon.com/ko_kr/glue/latest/dg/machine-learning-transform-tutorial.html
[4] https://docs.aws.amazon.com/ko_kr/glue/latest/dg/what-is-glue.html
[5] https://docs.aws.amazon.com/ko_kr/glue/latest/dg/components-key-concepts.html
[6] https://velog.io/%40ginee_park/AWS-Glue%EB%9E%80
[7] https://dev-records.tistory.com/entry/AWS-Glue%EB%9E%80
[8] https://aws.amazon.com/ko/glue/getting-started/