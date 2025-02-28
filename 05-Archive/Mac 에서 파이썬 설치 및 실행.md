---
tags:
  - mac
  - python
created: 2024-10-29 23:32
modified: 2024-10-29 23:32
related: 
updated: 2024-10-29 23:33
---
## Index

- [1. 시스템 요구사항](#1.%20%EC%8B%9C%EC%8A%A4%ED%85%9C%20%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%AD)
- [2. Python 및 필수 도구 설치](#2.%20Python%20%EB%B0%8F%20%ED%95%84%EC%88%98%20%EB%8F%84%EA%B5%AC%20%EC%84%A4%EC%B9%98)
	- [2.1 Homebrew 설치](#2.1%20Homebrew%20%EC%84%A4%EC%B9%98)
	- [2.2 Python3 설치](#2.2%20Python3%20%EC%84%A4%EC%B9%98)
- [3. 프로젝트 설정](#3.%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EC%84%A4%EC%A0%95)
	- [3.1 프로젝트 폴더 생성](#3.1%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%ED%8F%B4%EB%8D%94%20%EC%83%9D%EC%84%B1)
	- [3.2 가상환경 생성 및 활성화](#3.2%20%EA%B0%80%EC%83%81%ED%99%98%EA%B2%BD%20%EC%83%9D%EC%84%B1%20%EB%B0%8F%20%ED%99%9C%EC%84%B1%ED%99%94)
- [4. 프로젝트 파일 설정](#4.%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%ED%8C%8C%EC%9D%BC%20%EC%84%A4%EC%A0%95)
	- [4.1 requirements.txt 생성](#4.1%20requirements.txt%20%EC%83%9D%EC%84%B1)
	- [4.2 필요한 패키지 설치](#4.2%20%ED%95%84%EC%9A%94%ED%95%9C%20%ED%8C%A8%ED%82%A4%EC%A7%80%20%EC%84%A4%EC%B9%98)
	- [4.3 Python 스크립트 생성](#4.3%20Python%20%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%20%EC%83%9D%EC%84%B1)
- [5. 스크립트 실행](#5.%20%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%20%EC%8B%A4%ED%96%89)
	- [5.1 입력 파일 준비](#5.1%20%EC%9E%85%EB%A0%A5%20%ED%8C%8C%EC%9D%BC%20%EC%A4%80%EB%B9%84)
	- [5.2 스크립트 실행](#5.2%20%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%20%EC%8B%A4%ED%96%89)
- [6. 프로젝트 관리 및 유지보수](#6.%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%20%EB%B0%8F%20%EC%9C%A0%EC%A7%80%EB%B3%B4%EC%88%98)
	- [6.1 현재 설치된 패키지 목록 업데이트](#6.1%20%ED%98%84%EC%9E%AC%20%EC%84%A4%EC%B9%98%EB%90%9C%20%ED%8C%A8%ED%82%A4%EC%A7%80%20%EB%AA%A9%EB%A1%9D%20%EC%97%85%EB%8D%B0%EC%9D%B4%ED%8A%B8)
	- [6.2 다른 환경에서 프로젝트 설정](#6.2%20%EB%8B%A4%EB%A5%B8%20%ED%99%98%EA%B2%BD%EC%97%90%EC%84%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EC%84%A4%EC%A0%95)
	- [6.3 가상환경 비활성화](#6.3%20%EA%B0%80%EC%83%81%ED%99%98%EA%B2%BD%20%EB%B9%84%ED%99%9C%EC%84%B1%ED%99%94)
- [7. 문제 해결](#7.%20%EB%AC%B8%EC%A0%9C%20%ED%95%B4%EA%B2%B0)
	- [7.1 일반적인 문제](#7.1%20%EC%9D%BC%EB%B0%98%EC%A0%81%EC%9D%B8%20%EB%AC%B8%EC%A0%9C)
	- [7.2 Homebrew 업데이트](#7.2%20Homebrew%20%EC%97%85%EB%8D%B0%EC%9D%B4%ED%8A%B8)
- [8. 프로젝트 폴더 구조](#8.%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%ED%8F%B4%EB%8D%94%20%EA%B5%AC%EC%A1%B0)


## 1. 시스템 요구사항
- macOS 운영체제
- 인터넷 연결
- 터미널 접근 권한

## 2. Python 및 필수 도구 설치

### 2.1 Homebrew 설치
1. 터미널을 열고 다음 명령어 실행:
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
2. 설치 완료 후 터미널에 표시되는 추가 설정 지침 따르기

### 2.2 Python3 설치
```bash
brew install python3
```

설치 확인:
```bash
python3 --version
```

## 3. 프로젝트 설정

### 3.1 프로젝트 폴더 생성
```bash
mkdir excel_project
cd excel_project
```

### 3.2 가상환경 생성 및 활성화
```bash
# 가상환경 생성
python3 -m venv venv

# 가상환경 활성화
source venv/bin/activate
```

## 4. 프로젝트 파일 설정

### 4.1 requirements.txt 생성
다음 내용으로 requirements.txt 파일 생성:
```text
openpyxl==3.1.2
```

### 4.2 필요한 패키지 설치
```bash
pip install -r requirements.txt
```

### 4.3 Python 스크립트 생성
excel_filter.py 파일을 생성하고 다음 코드 입력:

```python
from openpyxl import load_workbook, Workbook

def filter_phone_numbers(input_file, output_file):
    """
    3번째 컬럼의 전화번호가 비어있지 않은 행만 새로운 엑셀 파일로 저장
    """
    # 원본 엑셀 파일 열기
    wb = load_workbook(input_file)
    sheet = wb.active
    
    # 새로운 워크북 생성
    new_wb = Workbook()
    new_sheet = new_wb.active
    
    # 헤더 복사
    for col in range(1, sheet.max_column + 1):
        new_sheet.cell(1, col, sheet.cell(1, col).value)
    
    # 데이터 필터링하여 복사
    new_row = 2  # 헤더 다음 행부터 시작
    total_rows = 0
    filtered_rows = 0
    
    for row in range(2, sheet.max_row + 1):  # 헤더 제외하고 시작
        total_rows += 1
        phone_number = sheet.cell(row, 3).value  # 3번째 컬럼
        
        # 전화번호가 있는 경우만 복사
        if phone_number and str(phone_number).strip():
            filtered_rows += 1
            for col in range(1, sheet.max_column + 1):
                new_sheet.cell(new_row, col, sheet.cell(row, col).value)
            new_row += 1
    
    # 새 파일 저장
    new_wb.save(output_file)
    
    print(f"처리 완료:")
    print(f"- 전체 데이터 수: {total_rows}")
    print(f"- 저장된 데이터 수: {filtered_rows}")
    print(f"- 제외된 데이터 수: {total_rows - filtered_rows}")

if __name__ == "__main__":
    # 파일 경로 설정
    input_file = "input.xlsx"  # 원본 파일 경로
    output_file = "output.xlsx"  # 저장할 파일 경로
    
    try:
        filter_phone_numbers(input_file, output_file)
        print(f"\n새 파일이 저장되었습니다: {output_file}")
    except FileNotFoundError:
        print(f"Error: {input_file} 파일을 찾을 수 없습니다.")
    except Exception as e:
        print(f"Error: 처리 중 오류가 발생했습니다 - {str(e)}")
```

## 5. 스크립트 실행

### 5.1 입력 파일 준비
- input.xlsx 파일을 프로젝트 폴더에 복사
- 파일 구조가 다음과 같은지 확인:
  - 1열: 이름
  - 2열: 이메일
  - 3열: 전화번호

### 5.2 스크립트 실행
```bash
python excel_filter.py
```

## 6. 프로젝트 관리 및 유지보수

### 6.1 현재 설치된 패키지 목록 업데이트
```bash
pip freeze > requirements.txt
```

### 6.2 다른 환경에서 프로젝트 설정
```bash
# 1. 프로젝트 복사
git clone [repository-url] # 또는 프로젝트 폴더 복사

# 2. 프로젝트 폴더로 이동
cd [project-folder]

# 3. 가상환경 생성
python3 -m venv venv

# 4. 가상환경 활성화
source venv/bin/activate

# 5. 패키지 설치
pip install -r requirements.txt
```

### 6.3 가상환경 비활성화
작업 완료 후:
```bash
deactivate
```

## 7. 문제 해결

### 7.1 일반적인 문제
1. "command not found: python3"
   - Homebrew를 통해 Python3 재설치
   ```bash
   brew reinstall python3
   ```

2. "ModuleNotFoundError: No module named 'openpyxl'"
   - 가상환경이 활성화되어 있는지 확인
   - requirements.txt를 통해 패키지 재설치
   ```bash
   pip install -r requirements.txt
   ```

3. 파일 권한 문제
   ```bash
   chmod +x excel_filter.py
   ```

### 7.2 Homebrew 업데이트
정기적으로 Homebrew 업데이트 실행:
```bash
brew update
brew upgrade python3
```

## 8. 프로젝트 폴더 구조
```
excel_project/
├── venv/
├── input.xlsx
├── output.xlsx
├── requirements.txt
└── excel_filter.py
```
