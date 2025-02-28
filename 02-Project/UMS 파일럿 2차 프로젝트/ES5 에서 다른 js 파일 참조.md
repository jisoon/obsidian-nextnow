---
tags: 
created: 2025-02-11 10:39
modified: 2025-02-11 10:39
related: []
updated: 2025-02-11 10:40
---
CommonJS를 사용하지 않고 JavaScript에서 다른 파일의 함수나 객체를 사용하는 방법은 **브라우저 환경**과 **Node.js 환경**에 따라 다릅니다. 아래는 CommonJS를 사용하지 않는 대안적인 방법들을 정리한 내용입니다.

---

## **1. 브라우저 환경에서의 방법**

### **(1) `<script>` 태그로 파일 로드**
- 가장 기본적인 방법으로, HTML 파일에서 `<script>` 태그를 사용해 여러 JavaScript 파일을 로드합니다.
- 모든 스크립트는 **전역 스코프**를 공유하므로, 다른 파일의 함수나 객체를 전역 변수로 정의하면 접근 가능합니다.

**예시:**
```html
<!-- math.js -->
<script>
    function add(a, b) {
        return a + b;
    }
    function subtract(a, b) {
        return a - b;
    }
</script>

<!-- main.js -->
<script>
    console.log(add(5, 3));       // 출력: 8
    console.log(subtract(5, 3)); // 출력: 2
</script>
```

---

### **(2) IIFE(즉시 실행 함수 표현)를 사용한 모듈화**
- 전역 스코프 오염을 방지하기 위해 IIFE를 사용하여 모듈을 캡슐화합니다.
- 필요한 함수나 객체를 반환하여 사용할 수 있습니다.

**예시:**
```javascript
// math.js
var MathModule = (function() {
    function add(a, b) {
        return a + b;
    }
    function subtract(a, b) {
        return a - b;
    }
    return {
        add: add,
        subtract: subtract
    };
})();
```

```html
<!-- main.js -->
<script src="math.js"></script>
<script>
    console.log(MathModule.add(5, 3));       // 출력: 8
    console.log(MathModule.subtract(5, 3)); // 출력: 2
</script>
```

---

### **(3) ES6 모듈 시스템 (`import` / `export`)**
- 최신 브라우저에서는 ES6 모듈 시스템을 지원하므로 `import`와 `export` 키워드를 사용할 수 있습니다.
- HTML에서 `<script type="module">` 속성을 추가해야 합니다.

**예시:**
```javascript
// math.js
export function add(a, b) {
    return a + b;
}
export function subtract(a, b) {
    return a - b;
}
```

```html
<!-- main.js -->
<script type="module">
    import { add, subtract } from './math.js';
    console.log(add(5, 3));       // 출력: 8
    console.log(subtract(5, 3)); // 출력: 2
</script>
```

---

## **2. Node.js 환경에서의 방법 (CommonJS 미사용)**

### **(1) 전역 객체 사용**
- CommonJS 없이도 전역 객체를 활용하여 데이터를 공유할 수 있습니다.

**예시:**
```javascript
// math.js
global.MathModule = {
    add: (a, b) => a + b,
    subtract: (a, b) => a - b
};
```

```javascript
// main.js
require('./math.js'); // math.js 로드
console.log(MathModule.add(5, 3));       // 출력: 8
console.log(MathModule.subtract(5, 3)); // 출력: 2
```

---

### **(2) ES6 모듈 시스템 (Node.js 버전 >= 12 필요)**
- Node.js에서도 ES6 모듈을 사용할 수 있습니다. 이를 위해 파일 확장자를 `.mjs`로 설정하거나 `package.json`에 `"type": "module"`을 추가해야 합니다.

**예시:**
```javascript
// math.mjs
export function add(a, b) {
    return a + b;
}
export function subtract(a, b) {
    return a - b;
}
```

```javascript
// main.mjs
import { add, subtract } from './math.mjs';
console.log(add(5, 3));       // 출력: 8
console.log(subtract(5, 3)); // 출력: 2
```

---

## **3. 브라우저와 Node.js 모두에서 사용할 수 있는 방법**

### **UMD (Universal Module Definition)**
- UMD는 브라우저와 Node.js 환경 모두에서 동작하도록 설계된 모듈 패턴입니다.
- AMD와 CommonJS를 모두 지원하며 전역 변수로도 동작합니다.

**예시:**
```javascript
// math.js (UMD 패턴)
(function(root, factory) {
    if (typeof define === 'function' && define.amd) {
        define([], factory); // AMD 지원
    } else if (typeof module === 'object' && module.exports) {
        module.exports = factory(); // CommonJS 지원
    } else {
        root.MathModule = factory(); // 전역 변수로 등록 (브라우저)
    }
}(this, function() {
    return {
        add: function(a, b) { return a + b; },
        subtract: function(a, b) { return a - b; }
    };
}));
```

**사용 예시 (브라우저):**
```html
<script src="math.js"></script>
<script>
    console.log(MathModule.add(5, 3));       // 출력: 8
</script>
```

**사용 예시 (Node.js):**
```javascript
const MathModule = require('./math');
console.log(MathModule.add(5, 3));       // 출력: 8
```

---

## **결론**

| 방법                     | 브라우저 환경 | Node.js 환경 | 특징                                                                 |
|--------------------------|---------------|--------------|----------------------------------------------------------------------|
| `<script>` 태그          | 가능          | 불가능       | 간단하지만 전역 스코프 오염 위험.                                    |
| IIFE                     | 가능          | 가능         | 전역 스코프 오염 방지.                                              |
| ES6 모듈 (`import/export`)| 가능          | 가능         | 최신 브라우저 및 Node.js에서만 동작.                                 |
| UMD                      | 가능          | 가능         | 브라우저와 Node.js 모두 호환되지만 구현이 복잡함.                    |

CommonJS 없이도 위의 다양한 방법으로 다른 파일의 함수나 객체를 사용할 수 있습니다. 선택은 프로젝트 환경과 요구사항에 따라 결정하면 됩니다.

출처
[1] 구형 브라우저에서 es6 사용하기 (es6 to es5 webpack 빌드) https://parinote.tistory.com/33
[2] 브라우저/노드 환경에서 모듈, AMD, CommonJS, UMD 알아보기 https://gobae.tistory.com/130
[3] 모던 브라우저에도 꼭 es5를 써야 할까 (Differential Serving) - velog https://velog.io/@vnthf/%EB%AA%A8%EB%8D%98%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80%EC%97%90%EB%8F%84-%EA%BC%AD-es5%EB%A5%BC-%EC%8D%A8%EC%95%BC-%ED%95%A0%EA%B9%8C-Differential-Serving
[4] 모듈 소개 - 모던 JavaScript 튜토리얼 https://ko.javascript.info/modules-intro
[5] es5은 어떻게 브라우저에서 동작했을까 & UMD - eunji - 티스토리 https://0119eunji.tistory.com/243
[6] [모던JS: Core] 모듈 - velog https://velog.io/@longroadhome/%EB%AA%A8%EB%8D%98JS-%EB%AA%A8%EB%93%88
[7] Import & Export in ES5, 6 - 프로그래밍 공부하기 - 티스토리 https://study-ihl.tistory.com/88
[8] JavaScript | Module (모듈) - velog https://velog.io/@xedni/Module

