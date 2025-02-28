---
tags: 
created: 2025-02-11 10:39
modified: 2025-02-11 10:39
related: []
updated: 2025-02-11 10:39
---
ES5에서 **`new`** 키워드를 통해 커스텀 객체를 생성하려면 **생성자 함수(Constructor Function)**를 사용해야 합니다. 생성자 함수는 객체를 초기화하고, 필요한 속성과 메서드를 정의하는 데 사용됩니다.

---

## **1. 생성자 함수의 기본 구조**
생성자 함수는 일반 함수와 비슷하지만, 다음과 같은 특징이 있습니다:
- 이름은 관례적으로 **파스칼 케이스(PascalCase)**로 작성합니다.
- `new` 키워드와 함께 호출되면:
  1. 새로운 객체가 생성됩니다.
  2. 생성된 객체가 `this`로 바인딩됩니다.
  3. 명시적으로 반환값이 없는 경우, 생성된 객체가 자동으로 반환됩니다.

### **예제: 기본 생성자 함수**
```javascript
function Person(name, age) {
    // this는 새로 생성된 객체를 가리킴
    this.name = name;
    this.age = age;

    // 메서드 정의
    this.sayHello = function() {
        console.log(`Hello, my name is ${this.name} and I am ${this.age} years old.`);
    };
}

// new 키워드로 객체 생성
const person1 = new Person("Alice", 25);
const person2 = new Person("Bob", 30);

person1.sayHello(); // 출력: Hello, my name is Alice and I am 25 years old.
person2.sayHello(); // 출력: Hello, my name is Bob and I am 30 years old.
```

---

## **2. 프로토타입을 사용한 메서드 정의**
생성자 함수 내부에서 메서드를 정의하면, 각 인스턴스마다 메서드가 복사되어 메모리를 낭비할 수 있습니다. 이를 방지하기 위해 **프로토타입(prototype)**을 사용하여 메서드를 공유할 수 있습니다.

### **예제: 프로토타입 활용**
```javascript
function Person(name, age) {
    this.name = name;
    this.age = age;
}

// 프로토타입에 메서드 추가
Person.prototype.sayHello = function() {
    console.log(`Hello, my name is ${this.name} and I am ${this.age} years old.`);
};

const person1 = new Person("Alice", 25);
const person2 = new Person("Bob", 30);

person1.sayHello(); // 출력: Hello, my name is Alice and I am 25 years old.
person2.sayHello(); // 출력: Hello, my name is Bob and I am 30 years old.

// 프로토타입 확인
console.log(person1.sayHello === person2.sayHello); // true (메서드가 공유됨)
```

---

## **3. `new` 키워드 없이 호출 시 문제점**
- `new` 키워드 없이 생성자 함수를 호출하면, `this`가 전역 객체(브라우저에서는 `window`)를 가리키게 됩니다.
- 이를 방지하기 위해 내부적으로 `new` 키워드를 강제하는 패턴을 사용할 수 있습니다.

### **예제: `new` 강제 패턴**
```javascript
function Person(name, age) {
    if (!(this instanceof Person)) {
        return new Person(name, age); // new 없으면 강제로 호출
    }
    this.name = name;
    this.age = age;
}

const person1 = Person("Alice", 25); // new 없이 호출해도 안전
console.log(person1 instanceof Person); // true
```

---

## **4. 반환값 처리**
- 생성자 함수에서 명시적으로 객체를 반환하면 해당 객체가 반환됩니다.
- 하지만 원시값(문자열, 숫자 등)을 반환하면 무시되고 기본적으로 생성된 객체가 반환됩니다.

### **예제: 반환값 처리**
```javascript
function CustomObject() {
    this.name = "Default";

    // 명시적으로 객체를 반환
    return { custom: "This is a custom object" };
}

const obj = new CustomObject();
console.log(obj); // { custom: "This is a custom object" }
```

---

## **5. 상속 구현 (프로토타입 체인 활용)**
ES5에서는 클래스를 지원하지 않으므로 프로토타입 체인을 사용해 상속을 구현합니다.

### **예제: 상속 구현**
```javascript
function Animal(name) {
    this.name = name;
}

Animal.prototype.speak = function() {
    console.log(`${this.name} makes a noise.`);
};

function Dog(name, breed) {
    Animal.call(this, name); // 부모 생성자 호출
    this.breed = breed;
}

// 프로토타입 상속 설정
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

// Dog의 고유 메서드 추가
Dog.prototype.bark = function() {
    console.log(`${this.name} barks!`);
};

const dog = new Dog("Rex", "Labrador");
dog.speak(); // 출력: Rex makes a noise.
dog.bark();  // 출력: Rex barks!
```

---

## **6. 요약 및 주의점**
- ES5에서 `new` 키워드를 통해 커스텀 객체를 생성하려면 **생성자 함수**를 사용해야 합니다.
- 메서드는 프로토타입에 정의하여 메모리 효율성을 높이는 것이 좋습니다.
- `new` 키워드를 강제하는 패턴을 사용하면 실수로 `new`를 빼먹는 문제를 방지할 수 있습니다.
- 상속은 프로토타입 체인을 활용하며, 부모 생성자를 명시적으로 호출해야 합니다.

이러한 방식은 ES6의 클래스(`class`) 문법이 도입되기 전까지 JavaScript에서 객체 지향 프로그래밍(OOP)을 구현하는 주요 방법이었습니다.

출처
[1] [JS] new 키워드를 사용하지 않고 객체 생성하기 https://bonjenny.tistory.com/73
[2] JS #2. this에 대한 모든 것(ES5, ES6) - 오웬의 개발 이야기 https://devowen.com/245
[3] ES5 .Object(1) - Object(객체) - Santos의 개발블로그 - 티스토리 https://sangcho.tistory.com/entry/ES5-Object1-Object%EA%B0%9D%EC%B2%B4
[4] [JS] 모던 Javascript(es5, es6)에서의 OOP - 갬장장이의 코드 대장간 https://gamesmith.tistory.com/46
[5] ES5 to ES6+ | TOAST UI :: Make Your Web Delicious! https://ui.toast.com/fe-guide/ko_ES5-TO-ES6/
[6] [Javascript] 함수 호출 시 'new' 키워드를 사용하면 어떤 일이 벌어질까 https://blog.naver.com/sqlpro/222704541298?viewType=pc
[7] [Javascript] ECMAScript, ES5와 ES6 비교 / 기능 / 사용법 / 예제코드 https://aiday.tistory.com/87
[8] ES5 : Javascript 정리 - 아스피린 개발자 - 티스토리 https://uoonleen.tistory.com/86

