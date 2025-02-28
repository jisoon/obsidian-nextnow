---
tags:
  - react
  - storybook
created: 2024-11-18 10:28
modified: 2024-11-18 10:28
related: 
updated: 2025-02-28 12:30
---
# React Storybook 도입 가이드

## 1. Storybook이란?
Storybook은 UI 컴포넌트를 독립적으로 개발하고 테스트할 수 있는 도구입니다. Vue나 Flutter에서의 컴포넌트 개발과 비슷한 개념이에요.

### 1.1 Storybook의 장점
- 컴포넌트를 독립적으로 개발할 수 있어요
- 문서화가 자동으로 됩니다
- 다른 개발자들과 컴포넌트를 쉽게 공유할 수 있어요
- 컴포넌트 테스트가 쉬워집니다

## 2. 설치 방법

### 2.1 새 프로젝트에 설치하기
```bash
# React 프로젝트 생성
npx create-react-app my-app
cd my-app

# Storybook 설치
npx storybook@latest init
```

### 2.2 기존 프로젝트에 설치하기
```bash
# 프로젝트 폴더에서
npx storybook@latest init
```

## 3. 기본 구조 이해하기

```plaintext
my-app/
  ├── .storybook/          # Storybook 설정 폴더
  │   ├── main.js          # Storybook 메인 설정
  │   └── preview.js       # Storybook 미리보기 설정
  ├── src/
  │   ├── components/      # 컴포넌트 폴더
  │   └── stories/         # 스토리 파일들
```

## 4. 첫 번째 Story 만들기

### 4.1 버튼 컴포넌트 생성
`src/components/Button.jsx` 파일을 만듭니다:

```jsx
// src/components/Button.jsx
import React from 'react';

export const Button = ({ label, primary = false, size = 'medium', onClick }) => {
  const baseStyle = 'rounded-md font-bold';
  const sizeStyle = {
    small: 'px-3 py-1 text-sm',
    medium: 'px-4 py-2',
    large: 'px-6 py-3 text-lg'
  };
  
  const colorStyle = primary 
    ? 'bg-blue-500 text-white hover:bg-blue-600' 
    : 'bg-gray-200 text-gray-800 hover:bg-gray-300';

  return (
    <button
      className={`${baseStyle} ${sizeStyle[size]} ${colorStyle}`}
      onClick={onClick}
    >
      {label}
    </button>
  );
};
```

### 4.2 Story 파일 생성
`src/stories/Button.stories.jsx` 파일을 만듭니다:

```jsx
// src/stories/Button.stories.jsx
import { Button } from '../components/Button';

export default {
  title: 'Components/Button',  // Storybook 메뉴에서 보일 위치
  component: Button,
  tags: ['autodocs'],  // 자동 문서화 활성화
};

// 기본 버튼 스토리
export const Default = {
  args: {
    label: '버튼',
    primary: false,
    size: 'medium',
  },
};

// 주요 버튼 스토리
export const Primary = {
  args: {
    label: '주요 버튼',
    primary: true,
    size: 'medium',
  },
};

// 크기별 버튼 스토리
export const Small = {
  args: {
    label: '작은 버튼',
    size: 'small',
  },
};

export const Large = {
  args: {
    label: '큰 버튼',
    size: 'large',
  },
};
```

## 5. Storybook 실행하기

```bash
# 개발 서버 실행
npm run storybook
```

## 6. 실제 개발 워크플로우

1. **컴포넌트 생성**:
   - 먼저 컴포넌트를 만듭니다 (예: `Button.jsx`)
   - Vue나 Flutter처럼 재사용 가능한 UI 요소를 만든다고 생각하세요

2. **Story 작성**:
   - 컴포넌트의 다양한 상태를 보여주는 스토리를 작성합니다
   - 각 스토리는 컴포넌트의 다른 사용 사례를 보여줍니다

3. **개발 및 테스트**:
   - Storybook에서 컴포넌트를 실시간으로 확인
   - 다양한 상태와 속성을 테스트

## 7. 유용한 팁

### 7.1 Controls 활용하기
- Storybook의 Controls 패널을 사용하면 Vue의 데브툴스처럼 실시간으로 props를 변경할 수 있어요
- 컴포넌트의 다양한 상태를 쉽게 테스트할 수 있습니다

### 7.2 문서 자동화
```jsx
// Button.jsx에 JSDoc 추가
/**
 * 기본 버튼 컴포넌트
 * @param {Object} props
 * @param {string} props.label - 버튼 텍스트
 * @param {boolean} [props.primary=false] - 주요 버튼 여부
 * @param {'small'|'medium'|'large'} [props.size='medium'] - 버튼 크기
 * @param {() => void} props.onClick - 클릭 핸들러
 */
export const Button = ({ ... }) => { ... };
```

## 8. 자주 하는 실수와 해결방법

### 8.1 컴포넌트가 보이지 않을 때
- 스토리 파일 이름이 `.stories.jsx` 로 끝나는지 확인
- 컴포넌트 import 경로가 정확한지 확인

### 8.2 스타일이 적용되지 않을 때
- `.storybook/preview.js`에 스타일 import 확인
- Tailwind CSS 사용 시 설정 확인

## 9. 다음 단계
1. 더 복잡한 컴포넌트 만들기
2. 컴포넌트 간 상호작용 스토리 작성
3. 테스트 자동화 도입

## 참고 사항
- Vue나 Flutter에서처럼 컴포넌트를 독립적인 단위로 생각하세요
- 처음에는 간단한 컴포넌트부터 시작하세요
- 문서화를 습관화하면 나중에 큰 도움이 됩니다

이 가이드를 따라하면서 궁금한 점이 있다면 언제든 추가 질문해주세요! 😊
