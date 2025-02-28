---
tags: 
created: 2025-02-11 10:42
modified: 2025-02-11 10:42
related: []
updated: 2025-02-11 10:42
---
ES5에서 다른 JavaScript 파일의 기능(함수나 객체)을 `new` 키워드를 사용해 활용하려면 **생성자 함수(Constructor Function)**를 정의하고 이를 외부 파일에서 사용할 수 있도록 설정해야 합니다. ES5 자체에는 모듈 시스템이 없으므로, 브라우저 환경에서는 전역 객체를 활용하거나 HTML `<script>` 태그로 파일을 로드하는 방식이 주로 사용됩니다.

---

## **1. 생성자 함수를 정의하고 다른 파일에서 사용하기**

### **Step 1: 생성자 함수 정의**
다른 파일에 생성자 함수를 정의합니다.

```javascript
// math.js
function MathOperations(a, b) {
    this.a = a;
    this.b = b;

    this.add = function() {
        return this.a + this.b;
    };

    this.subtract = function() {
        return this.a - this.b;
    };
}
```

---

### **Step 2: HTML에서 `<script>` 태그로 로드**
`math.js` 파일을 HTML에 `<script>` 태그로 먼저 로드한 뒤, 해당 생성자 함수를 사용할 수 있습니다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ES5 Example</title>
</head>
<body>
    <script src="math.js"></script> <!-- math.js 로드 -->
    <script>
        // 생성자 함수 사용
        var mathInstance = new MathOperations(10, 5);
        console.log(mathInstance.add());       // 출력: 15
        console.log(mathInstance.subtract()); // 출력: 5
    </script>
</body>
</html>
```

---

## **2. IIFE(즉시 실행 함수)로 모듈화**
전역 스코프 오염을 방지하기 위해 IIFE를 사용하여 모듈처럼 활용할 수 있습니다.

### **Step 1: IIFE로 생성자 함수 정의**
```javascript
// math.js
var MathModule = (function() {
    function MathOperations(a, b) {
        this.a = a;
        this.b = b;
    }

    MathOperations.prototype.add = function() {
        return this.a + this.b;
    };

    MathOperations.prototype.subtract = function() {
        return this.a - this.b;
    };

    return MathOperations; // 생성자 함수를 반환
})();
```

---

### **Step 2: HTML에서 사용**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ES5 Example with IIFE</title>
</head>
<body>
    <script src="math.js"></script> <!-- math.js 로드 -->
    <script>
        // IIFE로 반환된 생성자 함수 사용
        var mathInstance = new MathModule(20, 10);
        console.log(mathInstance.add());       // 출력: 30
        console.log(mathInstance.subtract()); // 출력: 10
    </script>
</body>
</html>
```

---

## **3. 브라우저 환경에서 전역 객체 활용**
생성자 함수를 전역 객체에 등록하면 다른 파일에서도 접근할 수 있습니다.

### **Step 1: 전역 객체에 생성자 함수 등록**
```javascript
// math.js
window.MathOperations = function(a, b) {
    this.a = a;
    this.b = b;

    this.add = function() {
        return this.a + this.b;
    };

    this.subtract = function() {
        return this.a - this.b;
    };
};
```

---

### **Step 2: HTML에서 사용**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ES5 Example with Global Object</title>
</head>
<body>
    <script src="math.js"></script> <!-- math.js 로드 -->
    <script>
        // 전역 객체를 통해 생성자 함수 사용
        var mathInstance = new MathOperations(15, 7);
        console.log(mathInstance.add());       // 출력: 22
        console.log(mathInstance.subtract()); // 출력: 8
    </script>
</body>
</html>
```

---

## **4. Node.js 환경에서 CommonJS 없이 사용**
Node.js 환경에서는 `module.exports` 없이도 전역 객체 `global`을 활용해 다른 파일의 생성자 함수를 사용할 수 있습니다.

### **Step 1: 전역 객체에 등록**
```javascript
// math.js
global.MathOperations = function(a, b) {
    this.a = a;
    this.b = b;

    this.add = function() {
        return this.a + this.b;
    };

    this.subtract = function() {
        return this.a - this.b;
    };
};
```

---

### **Step 2: 다른 파일에서 사용**
```javascript
// main.js
require('./math'); // math.js를 로드

var mathInstance = new global.MathOperations(10, 5);
console.log(mathInstance.add());       // 출력: 15
console.log(mathInstance.subtract()); // 출력: 5
```

---

## **요약**

| 방법                     | 설명                                                                 |
|--------------------------|----------------------------------------------------------------------|
| **HTML `<script>` 태그** | 브라우저 환경에서 가장 기본적인 방식으로 파일을 순차적으로 로드.         |
| **IIFE**                 | 전역 스코프 오염을 방지하며 모듈처럼 동작.                              |
| **전역 객체 활용**       | `window`(브라우저) 또는 `global`(Node.js)에 생성자 함수를 등록하여 사용. |
| **Node.js 환경**         | CommonJS 없이도 `global`을 통해 공유 가능.                              |

위 방법 중 프로젝트 환경과 요구사항에 맞는 방식을 선택하여 구현하면 됩니다!

**IIFE 방식으로 만들어서 사용**



출처
[1] ES5의 생성자 함수 - 뒤죽박죽 공책 https://9uk-e.tistory.com/25
[2] ES5 간단히 보기 (2) https://leesoo7595.github.io/class/2020/02/16/es5_second_week/
[3] ES5 to ES6+ | TOAST UI :: Make Your Web Delicious! https://ui.toast.com/fe-guide/ko_ES5-TO-ES6/
[4] Javascript ES5 클래스에 의한 인스턴스 생성 - velog https://velog.io/@dev-redo/Javascript-%EC%83%9D%EC%84%B1%EC%9E%90%ED%95%A8%EC%88%98%EC%97%90-%EC%9D%98%ED%95%9C-%EA%B0%9D%EC%B2%B4-%EC%83%9D%EC%84%B1
[5] [Javascript] 자바스크립트 근본 핵심 이해 / 5. Functio instance, 생성자 ... https://blog.naver.com/jaeeun_98/222504351232?viewType=pc
[6] [자바스크립트]클래스(class) [1] 객체지향 프로그래밍의 상속방법(es5 ... https://jssq2468.tistory.com/60
[7] [Javascript] ECMAScript, ES5와 ES6 비교 / 기능 / 사용법 / 예제코드 https://aiday.tistory.com/87
[8] ES5 .Object(1) - Object(객체) - Santos의 개발블로그 - 티스토리 https://sangcho.tistory.com/entry/ES5-Object1-Object%EA%B0%9D%EC%B2%B4

