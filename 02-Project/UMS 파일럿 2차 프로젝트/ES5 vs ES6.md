---
tags: 
created: 2025-02-11 10:27
modified: 2025-02-11 10:27
related: []
updated: 2025-02-11 10:30
---
dataset api 는 html5 규격으로 ie11 에서 지원 가능

## ES5 vs ES6
**ES5와 ES6의 차이와 사용 가능한 문법 비교**는 JavaScript의 발전 과정에서 중요한 부분입니다. ES6(ECMAScript 2015)는 ES5(ECMAScript 2009)에 비해 더 현대적이고 간결한 문법과 기능을 제공합니다. 아래는 주요 차이점과 사용 가능한 문법을 정리한 내용입니다.

---

## **1. 변수 선언**
| ES5                     | ES6                           |
|--------------------------|-------------------------------|
| `var` 키워드만 사용 가능 (함수 스코프) | `let`, `const` 키워드 추가 (블록 스코프) |
| 변수 재선언 가능           | `let`은 재선언 불가, `const`는 재할당 불가 |

**예시:**
```javascript
// ES5
var x = 10;
var x = 20; // 재선언 가능

// ES6
let y = 10;
// let y = 20; // SyntaxError: Identifier 'y' has already been declared
const z = 30;
// z = 40; // TypeError: Assignment to constant variable.
```

---

## **2. 함수**
### **화살표 함수 (Arrow Function)**
- ES5에서는 일반 함수만 사용 가능.
- ES6에서는 화살표 함수(`=>`)를 도입하여 간결한 함수 표현과 `this` 바인딩 문제 해결.

**예시:**
```javascript
// ES5
var add = function(a, b) {
    return a + b;
};

// ES6
const add = (a, b) => a + b;
```

---

## **3. 템플릿 리터럴**
- ES5에서는 문자열 연결에 `+` 연산자를 사용.
- ES6에서는 백틱(``` ` ```

**예시:**
```javascript
// ES5
var name = "World";
console.log("Hello, " + name + "!");

// ES6
let name = "World";
console.log(`Hello, ${name}!`);
```

---

## **4. 클래스 (`class`)**
- ES5에서는 프로토타입 기반으로 클래스를 흉내 냈음.
- ES6에서는 `class` 키워드로 객체 지향 프로그래밍을 지원.

**예시:**
```javascript
// ES5
function Person(name, age) {
    this.name = name;
    this.age = age;
}
Person.prototype.sayHello = function() {
    console.log("Hello, " + this.name + "!");
};

// ES6
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    sayHello() {
        console.log(`Hello, ${this.name}!`);
    }
}
```

---

## **5. 모듈 시스템**
- ES5에서는 모듈 시스템이 없어서 CommonJS(require)나 AMD를 사용.
- ES6에서는 `import`와 `export` 키워드를 도입하여 모듈화를 지원.

**예시:**
```javascript
// ES6
// module.js
export const greet = () => console.log("Hello!");

// main.js
import { greet } from './module.js';
greet();
```

---

## **6. 기본 매개변수 (Default Parameters)**
- ES5에서는 기본값을 설정하기 위해 조건문이나 논리 연산자를 사용.
- ES6에서는 함수 선언 시 기본값 설정 가능.

**예시:**
```javascript
// ES5
function multiply(a, b) {
    b = (typeof b !== 'undefined') ? b : 1;
    return a * b;
}

// ES6
const multiply = (a, b = 1) => a * b;
```

---

## **7. 구조 분해 할당 (Destructuring Assignment)**
- ES6에서 배열이나 객체의 값을 쉽게 추출 가능.

**예시:**
```javascript
// 배열 구조 분해
const [a, b] = [1, 2];

// 객체 구조 분해
const { name, age } = { name: "Alice", age: 25 };
```

---

## **8. 스프레드 연산자 (`...`) 및 나머지 매개변수**
- 배열이나 객체를 복사하거나 병합할 때 유용.
- 나머지 매개변수로 가변 인수를 처리 가능.

**예시:**
```javascript
// 스프레드 연산자
const arr1 = [1, 2];
const arr2 = [...arr1, 3]; // [1, 2, 3]

// 나머지 매개변수
const sum = (...nums) => nums.reduce((a, b) => a + b);
```

---

## **9. 반복문 개선 (`for...of`)**
- ES5에서는 `for`, `forEach`만 사용 가능.
- ES6에서는 `for...of`를 추가하여 이터러블 객체 순회 가능.

**예시:**
```javascript
// ES5
var arr = [1, 2, 3];
for (var i = 0; i < arr.length; i++) {
    console.log(arr[i]);
}

// ES6
for (let num of arr) {
    console.log(num);
}
```

---

## **10. 프로미스 (`Promise`)**
- 비동기 처리를 위해 콜백 대신 프로미스를 도입.

**예시:**
```javascript
// ES5 - 콜백 방식
function fetchData(callback) {
    setTimeout(() => callback("data"), 1000);
}

// ES6 - 프로미스 방식
const fetchData = () => new Promise(resolve => setTimeout(() => resolve("data"), 1000));
fetchData().then(data => console.log(data));
```

---

## **비교 요약**

| 기능                     | ES5                          | ES6                          |
|--------------------------|------------------------------|------------------------------|
| 변수 선언                | `var`                        | `let`, `const`               |
| 함수                     | 일반 함수                    | 화살표 함수 (`=>`)           |
| 문자열                  | 문자열 연결 (+ 연산자)       | 템플릿 리터럴 (`${}`)        |
| 클래스                  | 프로토타입 기반              | `class` 키워드               |
| 모듈                   | CommonJS/AMD                 | `import`, `export`           |
| 기본 매개변수            | 조건문으로 설정              | 함수 선언 시 기본값 제공      |
| 구조 분해 할당           | 지원하지 않음                | 배열/객체 구조 분해 지원      |
| 스프레드 연산자          | 지원하지 않음                | 배열/객체 병합 및 복사        |
| 반복문                  | `for`, `forEach`             | `for...of`                   |
| 비동기 처리              | 콜백                        | 프로미스 (`Promise`)         |

---

ES6는 코드의 가독성과 효율성을 높이는 다양한 기능을 제공하며, 현대 JavaScript 개발에서 필수적으로 사용됩니다.

출처
[1] [Javascript] ECMAScript, ES5와 ES6 비교 / 기능 / 사용법 / 예제코드 https://aiday.tistory.com/87
[2] [JS] Javascript ES5 vs ES6 비교 문법 정리 https://juniordev-team.tistory.com/6
[3] ECMAScript ES5 ES6 차이 - velog https://velog.io/@oigu529/ES5-vs-ES6
[4] JavaScript의 ES란?, ES5/ES6 문법 차이 - velog https://velog.io/@leejpsd/JavaScript%EC%9D%98-ES%EB%9E%80-ES5ES6-%EB%AC%B8%EB%B2%95-%EC%B0%A8%EC%9D%B4
[5] [JavaScript] ES5와 ES6 비교 - 아이딕 IT블로그 - 티스토리 https://tmdrnr96.tistory.com/25
[6] [JavaScript] ES6 기본 문법, ES5와의 비교 - 공부하려고 만든 블로그 https://xoxorbrb.tistory.com/8
[7] [JS] 자바스크립트 ES5 ES6 차이 - 허범성 - 티스토리 https://hbsowo58.tistory.com/407
[8] JavaScript의 ES란?, ES5/ES6 문법 차이 - 준의 개발일지 - 티스토리 https://redjun89.tistory.com/63
[9] [JavaScript] ECMAScript란? ES5와 ES6의 차이 - Seize the day! https://doozi0316.tistory.com/entry/JavaScript-ECMAScript%EB%9E%80-ES5%EC%99%80-ES6%EC%9D%98-%EC%B0%A8%EC%9D%B4var-const-let-%ED%99%94%EC%82%B4%ED%91%9C-%ED%95%A8%EC%88%98-class

