---
tags:
  - anthropic
created: 2024-11-06 20:02
modified: 2024-11-06 20:02
related: 
updated: 2024-11-06 20:03
---


## 주의사항
이 기능은 베타 버전이며 일반적인 API나 채팅 인터페이스와는 다른 고유한 위험이 있습니다. 특히 인터넷과 상호작용할 때 위험이 높아집니다. 위험을 최소화하기 위해 다음과 같은 예방 조치를 고려하세요:

1. 시스템 공격이나 사고를 방지하기 위해 최소한의 권한을 가진 전용 가상 머신이나 컨테이너 사용
2. 계정 로그인 정보와 같은 민감한 데이터에 대한 접근 제한
3. 악성 콘텐츠 노출을 줄이기 위해 허용된 도메인으로만 인터넷 접근 제한
4. 중요한 실제 결과를 초래할 수 있는 결정은 반드시 사람이 확인

## 설치 및 실행 방법

### 1. Anthropic API 사용 시
```bash
# API 키 설정 (Anthropic Console에서 확인 가능)
export ANTHROPIC_API_KEY=your_api_key

# Docker 실행
docker run \
    -e ANTHROPIC_API_KEY=$ANTHROPIC_API_KEY \
    -v $HOME/.anthropic:/home/computeruse/.anthropic \
    -p 5900:5900 \
    -p 8501:8501 \
    -p 6080:6080 \
    -p 8080:8080 \
    -it ghcr.io/anthropics/anthropic-quickstarts:computer-use-demo-latest
```

### 2. 데모 앱 접속 방법
컨테이너가 실행되면 다음 URL로 접속할 수 있습니다:

- 통합 인터페이스 (에이전트 채팅 + 데스크톱 뷰): http://localhost:8080
- Streamlit 인터페이스: http://localhost:8501
- 데스크톱 뷰: http://localhost:6080/vnc.html
- VNC 직접 연결: vnc://localhost:5900

### 3. 화면 크기 설정
환경 변수 `WIDTH`와 `HEIGHT`로 화면 크기를 설정할 수 있습니다:

```bash
docker run \
    -e ANTHROPIC_API_KEY=$ANTHROPIC_API_KEY \
    -v $HOME/.anthropic:/home/computeruse/.anthropic \
    -p 5900:5900 \
    -p 8501:8501 \
    -p 6080:6080 \
    -p 8080:8080 \
    -e WIDTH=1920 \
    -e HEIGHT=1080 \
    -it ghcr.io/anthropics/anthropic-quickstarts:computer-use-demo-latest
```

## 개발 환경 설정

개발을 위한 설정은 다음과 같습니다:

```bash
# 개발 환경 설정
./setup.sh  # venv 구성, 개발 의존성 설치, pre-commit hooks 설치

# Docker 이미지 빌드 (선택사항)
docker build . -t computer-use-demo:local

# 개발용 Docker 실행
export ANTHROPIC_API_KEY=your_api_key
docker run \
    -e ANTHROPIC_API_KEY=$ANTHROPIC_API_KEY \
    -v $(pwd)/computer_use_demo:/home/computeruse/computer_use_demo/ \
    -v $HOME/.anthropic:/home/computeruse/.anthropic \
    -p 5900:5900 \
    -p 8501:8501 \
    -p 6080:6080 \
    -p 8080:8080 \
    -it computer-use-demo:local
```

설정과 관련하여 추가 질문이 있으시다면 답변 드리겠습니다.

