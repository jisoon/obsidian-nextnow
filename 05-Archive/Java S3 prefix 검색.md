---
tags:
  - Java
  - S3
  - aws
created: 2024-10-30 15:07
modified: 2024-10-30 15:07
related: 
updated: 2024-10-30 17:53
---
S3의 prefix는 정규식을 직접 지원하지 않습니다. 하지만 파일 목록을 가져온 후 Java에서 정규식으로 필터링하는 방법으로 구현할 수 있습니다.

예를 들어 "rpa/Allbaro" 로 시작하고 특정 패턴을 가진 파일들을 찾고 싶다면:

```java
import java.util.regex.Pattern;

// S3에서 prefix로 먼저 필터링 (시작 부분만)
String prefix = "rpa/Allbaro";

ListObjectsV2Request request = ListObjectsV2Request.builder()
    .bucket("your-bucket-name")
    .prefix(prefix)
    .build();

// 정규식 패턴 정의
Pattern pattern = Pattern.compile("rpa/Allbaro.*\\d{8}\\.txt"); // 예: Allbaro뒤에 날짜(8자리)가 있고 .txt로 끝나는 파일

// 파일 목록 조회 및 정규식 필터링
List<S3Object> filteredFiles = s3Client.listObjectsV2(request)
    .contents()
    .stream()
    .filter(obj -> pattern.matcher(obj.key()).matches())
    .collect(Collectors.toList());
```

좀 더 체계적으로 관리하고 싶다면 다음과 같이 작성할 수 있습니다:



```java
import software.amazon.awssdk.services.s3.S3Client;
import software.amazon.awssdk.services.s3.model.*;
import java.time.Instant;
import java.util.List;
import java.util.regex.Pattern;
import java.util.stream.Collectors;

public class S3RegexFileFilter {
    private final S3Client s3Client;
    private final String bucketName;

    public S3RegexFileFilter(S3Client s3Client, String bucketName) {
        this.s3Client = s3Client;
        this.bucketName = bucketName;
    }

    public static class S3FileInfo {
        private final String key;
        private final long size;
        private final Instant lastModified;

        public S3FileInfo(S3Object object) {
            this.key = object.key();
            this.size = object.size();
            this.lastModified = object.lastModified();
        }

        public String getKey() { return key; }
        public long getSize() { return size; }
        public Instant getLastModified() { return lastModified; }
        
        @Override
        public String toString() {
            return String.format("Key: %s, Size: %d bytes, Modified: %s",
                key, size, lastModified);
        }
    }

    // 정규식 패턴으로 파일 검색
    public List<S3FileInfo> findFilesByRegex(String prefix, String regex) {
        Pattern pattern = Pattern.compile(regex);
        
        ListObjectsV2Request request = ListObjectsV2Request.builder()
            .bucket(bucketName)
            .prefix(prefix)
            .build();

        return s3Client.listObjectsV2(request)
            .contents()
            .stream()
            .filter(obj -> pattern.matcher(obj.key()).matches())
            .map(S3FileInfo::new)
            .collect(Collectors.toList());
    }

    // 여러 패턴으로 검색 (OR 조건)
    public List<S3FileInfo> findFilesByPatterns(String prefix, List<String> regexPatterns) {
        List<Pattern> patterns = regexPatterns.stream()
            .map(Pattern::compile)
            .collect(Collectors.toList());
        
        ListObjectsV2Request request = ListObjectsV2Request.builder()
            .bucket(bucketName)
            .prefix(prefix)
            .build();

        return s3Client.listObjectsV2(request)
            .contents()
            .stream()
            .filter(obj -> patterns.stream()
                .anyMatch(pattern -> pattern.matcher(obj.key()).matches()))
            .map(S3FileInfo::new)
            .collect(Collectors.toList());
    }

    // 날짜 범위와 정규식 조합 검색
    public List<S3FileInfo> findFilesByRegexAndDateRange(
            String prefix, 
            String regex, 
            Instant startDate, 
            Instant endDate) {
        Pattern pattern = Pattern.compile(regex);
        
        ListObjectsV2Request request = ListObjectsV2Request.builder()
            .bucket(bucketName)
            .prefix(prefix)
            .build();

        return s3Client.listObjectsV2(request)
            .contents()
            .stream()
            .filter(obj -> pattern.matcher(obj.key()).matches() &&
                          obj.lastModified().isAfter(startDate) &&
                          obj.lastModified().isBefore(endDate))
            .map(S3FileInfo::new)
            .collect(Collectors.toList());
    }
}

```

사용 예시:

```java
// S3 클라이언트 초기화
S3Client s3Client = S3Client.builder()
    .region(Region.AP_NORTHEAST_2)
    .build();

// 필터 객체 생성
S3RegexFileFilter filter = new S3RegexFileFilter(s3Client, "your-bucket-name");

// 1. 단일 정규식 패턴으로 검색
// 예: rpa/Allbaro로 시작하고 YYYYMMDD 형식의 날짜가 있는 .txt 파일
String regex = "rpa/Allbaro.*\\d{8}\\.txt";
List<S3FileInfo> files = filter.findFilesByRegex("rpa/Allbaro", regex);

// 2. 여러 패턴으로 검색
List<String> patterns = Arrays.asList(
    "rpa/Allbaro.*\\.txt$",
    "rpa/Allbaro.*\\.csv$"
);
List<S3FileInfo> multiPatternFiles = filter.findFilesByPatterns("rpa/Allbaro", patterns);

// 3. 날짜 범위와 정규식 조합 검색
Instant startDate = Instant.now().minus(Duration.ofDays(7));
Instant endDate = Instant.now();
List<S3FileInfo> dateRangeFiles = filter.findFilesByRegexAndDateRange(
    "rpa/Allbaro",
    regex,
    startDate,
    endDate
);
```

주요 기능:
1. 기본 prefix로 1차 필터링 후 정규식으로 2차 필터링
2. 여러 정규식 패턴을 OR 조건으로 검색 가능
3. 날짜 범위와 정규식을 조합한 검색 지원
4. 파일 정보를 캡슐화하여 관리
5. 스트림 API를 활용한 효율적인 필터링

참고사항:
- prefix는 성능을 위해 가능한 구체적으로 지정하는 것이 좋습니다
- 정규식 패턴은 전체 파일 키(경로 포함)에 대해 적용됩니다
- 대용량 버킷의 경우 pagination을 고려해야 할 수 있습니다
