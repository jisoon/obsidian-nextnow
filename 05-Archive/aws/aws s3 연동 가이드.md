---
created: 2024-10-22 22:22
updated: 2024-11-05 09:22
---
# Spring Boot AWS S3 개발 가이드

## 목차
1. 개요
2. Gradle 설정
3. AWS 설정
   - Profile 기반 설정
   - 자격 증명 설정
4. S3 구성
5. 주요 기능 구현
6. 예외 처리
7. 보안 고려사항

## 1. 개요
본 가이드는 Spring Boot 애플리케이션에서 AWS S3를 활용하는 방법을 설명합니다. AWS SDK를 사용한 파일 관리와 AWS Profile 기반의 설정 방법을 포함합니다.

## 2. Gradle 설정

```groovy
plugins {
    id 'org.springframework.boot' version '2.7.7'
    id 'io.spring.dependency-management' version '1.0.15.RELEASE'
    id 'java'
}

group = 'com.company'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'

configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.cloud:spring-cloud-starter-aws:2.2.6.RELEASE'
    implementation 'aws.sdk.kotlin:s3:0.17.5-beta'
    
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
    
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

dependencyManagement {
    imports {
        mavenBom "org.springframework.cloud:spring-cloud-aws-dependencies:2.2.6.RELEASE"
    }
}

test {
    useJUnitPlatform()
}
```

## 3. AWS 설정

### Profile 기반 설정

#### AWS CLI Profile 설정 방법
1. AWS CLI 설치 후 다음 명령어로 프로필 설정:
```bash
aws configure --profile [profile-name]
```

2. ~/.aws/credentials 파일 예시:
```ini
[default]
aws_access_key_id = your-access-key
aws_secret_access_key = your-secret-key

[dev]
aws_access_key_id = dev-access-key
aws_secret_access_key = dev-secret-key

[prod]
aws_access_key_id = prod-access-key
aws_secret_access_key = prod-secret-key
```

3. ~/.aws/config 파일 예시:
```ini
[default]
region = ap-northeast-2
output = json

[profile dev]
region = ap-northeast-2
output = json

[profile prod]
region = ap-northeast-2
output = json
```

### Profile 기반 S3 설정 클래스

```java
@Configuration
@EnableConfigurationProperties
public class S3Config {

    @Value("${cloud.aws.region.static}")
    private String region;

    @Value("${cloud.aws.profile}")
    private String profile;
    
    @Bean
    public AmazonS3Client amazonS3Client() {
        return (AmazonS3Client) AmazonS3ClientBuilder
                .standard()
                .withRegion(region)
                .withCredentials(new ProfileCredentialsProvider(profile))
                .build();
    }
}
```

### 환경별 application.yml 설정

```yaml
# application.yml
spring:
  profiles:
    active: local

cloud:
  aws:
    region:
      static: ap-northeast-2
    stack:
      auto: false
    profile: default

---
# application-local.yml
spring:
  config:
    activate:
      on-profile: local

cloud:
  aws:
    profile: default
    s3:
      bucket: your-bucket-name-local

---
# application-dev.yml
spring:
  config:
    activate:
      on-profile: dev

cloud:
  aws:
    profile: dev
    s3:
      bucket: your-bucket-name-dev

---
# application-prod.yml
spring:
  config:
    activate:
      on-profile: prod

cloud:
  aws:
    profile: prod
    s3:
      bucket: your-bucket-name-prod
```

### Profile 기반 확장 S3Service 클래스

```java
@Service
@RequiredArgsConstructor
@Slf4j
public class S3Service {
    
    private final AmazonS3Client amazonS3Client;
    
    @Value("${cloud.aws.s3.bucket}")
    private String bucket;

    @Value("${cloud.aws.profile}")
    private String awsProfile;

    // 파일 업로드 (메타데이터 추가)
    public String uploadFile(MultipartFile file, Map<String, String> metadata) {
        try {
            String fileName = createFileName(file.getOriginalFilename());
            
            ObjectMetadata objectMetadata = new ObjectMetadata();
            objectMetadata.setContentType(file.getContentType());
            objectMetadata.setContentLength(file.getSize());
            
            // 커스텀 메타데이터 추가
            metadata.forEach((key, value) -> objectMetadata.addUserMetadata(key, value));
            
            // 프로필 정보를 메타데이터에 추가
            objectMetadata.addUserMetadata("environment", awsProfile);
            
            PutObjectRequest putObjectRequest = new PutObjectRequest(
                bucket, 
                fileName, 
                file.getInputStream(), 
                objectMetadata
            ).withCannedAcl(CannedAccessControlList.PublicRead);

            amazonS3Client.putObject(putObjectRequest);
            
            return amazonS3Client.getUrl(bucket, fileName).toString();
            
        } catch (IOException e) {
            log.error("파일 업로드 실패: {}", e.getMessage());
            throw new S3FileUploadException("파일 업로드 중 오류가 발생했습니다.", e);
        }
    }

    // 특정 환경의 파일만 조회
    public List<S3ObjectSummary> listFilesByEnvironment() {
        ListObjectsRequest listObjectsRequest = new ListObjectsRequest()
            .withBucketName(bucket);
            
        ObjectListing objectListing = amazonS3Client.listObjects(listObjectsRequest);
        
        return objectListing.getObjectSummaries().stream()
            .filter(this::isCurrentEnvironmentFile)
            .collect(Collectors.toList());
    }

    private boolean isCurrentEnvironmentFile(S3ObjectSummary objectSummary) {
        try {
            ObjectMetadata metadata = amazonS3Client.getObjectMetadata(bucket, objectSummary.getKey());
            String fileEnvironment = metadata.getUserMetadata().get("environment");
            return awsProfile.equals(fileEnvironment);
        } catch (AmazonS3Exception e) {
            log.error("메타데이터 조회 실패: {}", e.getMessage());
            return false;
        }
    }

    // 버킷 존재 여부 확인
    public boolean checkBucketExists() {
        return amazonS3Client.doesBucketExistV2(bucket);
    }

    // 버킷 용량 조회
    public BucketSizeInfo getBucketSize() {
        long totalSize = 0;
        int totalFiles = 0;
        
        ObjectListing objectListing = amazonS3Client.listObjects(bucket);
        
        do {
            for (S3ObjectSummary objectSummary : objectListing.getObjectSummaries()) {
                totalSize += objectSummary.getSize();
                totalFiles++;
            }
            objectListing = amazonS3Client.listNextBatchOfObjects(objectListing);
        } while (objectListing.isTruncated());
        
        return new BucketSizeInfo(totalSize, totalFiles);
    }
}

@Getter
@AllArgsConstructor
public class BucketSizeInfo {
    private long totalSize; // bytes
    private int totalFiles;
}
```

### 예외 클래스 추가

```java
@Getter
public class S3FileUploadException extends RuntimeException {
    public S3FileUploadException(String message, Throwable cause) {
        super(message, cause);
    }
}

@Getter
public class S3FileNotFoundException extends RuntimeException {
    public S3FileNotFoundException(String message) {
        super(message);
    }
}
```

## 5. Controller 확장

```java
@RestController
@RequestMapping("/api/s3")
@RequiredArgsConstructor
@Slf4j
public class S3Controller {
    
    private final S3Service s3Service;

    @PostMapping("/upload")
    public ResponseEntity<String> uploadFile(
            @RequestParam("file") MultipartFile file,
            @RequestParam Map<String, String> metadata) {
        String fileUrl = s3Service.uploadFile(file, metadata);
        return ResponseEntity.ok(fileUrl);
    }

    @GetMapping("/bucket/info")
    public ResponseEntity<BucketSizeInfo> getBucketInfo() {
        BucketSizeInfo bucketInfo = s3Service.getBucketSize();
        return ResponseEntity.ok(bucketInfo);
    }

    @GetMapping("/files/environment")
    public ResponseEntity<List<S3ObjectSummary>> listEnvironmentFiles() {
        List<S3ObjectSummary> files = s3Service.listFilesByEnvironment();
        return ResponseEntity.ok(files);
    }
}
```

## 6. AWS Profile 사용 시 주의사항

1. 프로필 우선순위
   - 시스템 환경 변수
   - Java 시스템 속성
   - AWS 자격 증명 파일 (~/.aws/credentials)
   - AWS 설정 파일 (~/.aws/config)

2. 보안 고려사항
   - 프로덕션 환경에서는 IAM Role 사용 권장
   - 민감한 자격 증명 정보는 AWS Secrets Manager 사용
   - 로컬 개발 환경에서만 프로필 사용 권장

3. 프로필 전환
```java
// 런타임에 프로필 전환이 필요한 경우
public void switchProfile(String profileName) {
    amazonS3Client = (AmazonS3Client) AmazonS3ClientBuilder
        .standard()
        .withRegion(region)
        .withCredentials(new ProfileCredentialsProvider(profileName))
        .build();
}
```

## 7. 배포 환경별 설정 예시

### Docker 환경
```dockerfile
FROM openjdk:11-jdk-slim

# AWS 자격 증명 파일을 위한 디렉토리 생성
RUN mkdir -p /root/.aws

# AWS 자격 증명 파일 복사
COPY .aws/credentials /root/.aws/credentials
COPY .aws/config /root/.aws/config

COPY build/libs/*.jar app.jar

ENTRYPOINT ["java","-jar","/app.jar"]
```

### Kubernetes 환경
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: aws-credentials
type: Opaque
data:
  credentials: base64로인코딩된AWS인증파일내용
  config: base64로인코딩된AWS설정파일내용
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: your-app
spec:
  template:
    spec:
      containers:
      - name: your-app
        volumeMounts:
        - name: aws-credentials
          mountPath: /root/.aws
          readOnly: true
      volumes:
      - name: aws-credentials
        secret:
          secretName: aws-credentials
```

## 8. 로깅 설정 추가

```java
@Aspect
@Component
@Slf4j
public class S3OperationLoggingAspect {

    @Around("execution(* com.company.service.S3Service.*(..))")
    public Object logS3Operation(ProceedingJoinPoint joinPoint) throws Throwable {
        String methodName = joinPoint.getSignature().getName();
        
        log.info("S3 Operation Started: {} with args: {}", 
            methodName, 
            Arrays.toString(joinPoint.getArgs())
        );
        
        long startTime = System.currentTimeMillis();
        Object result = joinPoint.proceed();
        long endTime = System.currentTimeMillis();
        
        log.info("S3 Operation Completed: {} in {}ms", 
            methodName, 
            (endTime - startTime)
        );
        
        return result;
    }
}
```

## 9. 성능 최적화 팁

1. 대용량 파일 처리
```java
public void uploadLargeFile(File file) {
    TransferManager transferManager = TransferManagerBuilder
        .standard()
        .withS3Client(amazonS3Client)
        .build();

    Upload upload = transferManager.upload(bucket, file.getName(), file);
    
    try {
        upload.waitForCompletion();
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
        throw new S3FileUploadException("파일 업로드가 중단되었습니다.", e);
    }
}
```

2. 병렬 처리
```java
public List<String> uploadMultipleFiles(List<MultipartFile> files) {
    return files.parallelStream()
        .map(file -> {
            try {
                return uploadFile(file, Collections.emptyMap());
            } catch (Exception e) {
                log.error("파일 업로드 실패: {}", file.getOriginalFilename());
                return null;
            }
        })
        .filter(Objects::nonNull)
        .collect(Collectors.toList());
}
```