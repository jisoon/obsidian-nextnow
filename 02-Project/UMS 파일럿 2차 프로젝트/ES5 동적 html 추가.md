---
tags: 
created: 2025-02-11 10:51
modified: 2025-02-11 10:51
related: []
updated: 2025-02-11 10:51
---
ES5에서 **동적으로 HTML을 추가**하면서 **효율적이고 모듈화**할 수 있는 방법은 다음과 같습니다. 이 방법은 **재사용 가능한 함수**를 작성하고, **DOM API**를 활용하여 동적으로 요소를 생성하고 추가하는 방식입니다.

---

## **1. 효율적인 동적 HTML 추가 방법**
ES5에서는 `document.createElement()`, `appendChild()`와 같은 DOM 조작 메서드를 사용하여 HTML 요소를 동적으로 추가할 수 있습니다. 이 방식은 성능이 좋고, 모듈화가 가능합니다.

### **기본 코드 예제**
```javascript
function createAndAppendElement(parentId, tagName, attributes, innerHTML) {
    // 1. 부모 요소 선택
    var parent = document.getElementById(parentId);
    if (!parent) {
        console.error("Parent element not found!");
        return;
    }

    // 2. 새로운 HTML 요소 생성
    var newElement = document.createElement(tagName);

    // 3. 속성 추가
    for (var key in attributes) {
        if (attributes.hasOwnProperty(key)) {
            newElement.setAttribute(key, attributes[key]);
        }
    }

    // 4. 내용 추가
    if (innerHTML) {
        newElement.innerHTML = innerHTML;
    }

    // 5. 부모 요소에 자식으로 추가
    parent.appendChild(newElement);
}
```

---

### **사용 예제**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dynamic HTML Example</title>
</head>
<body>
    <div id="container"></div>
    
    <script src="dynamic.js"></script>
    <script>
        // 동적 요소 추가 호출
        createAndAppendElement("container", "div", { class: "box", id: "box1" }, "This is a dynamically added div.");
        createAndAppendElement("container", "p", { class: "text" }, "This is a dynamically added paragraph.");
    </script>
</body>
</html>
```

**결과:**
```html
<div id="container">
    <div class="box" id="box1">This is a dynamically added div.</div>
    <p class="text">This is a dynamically added paragraph.</p>
</div>
```

---

## **2. 모듈화된 구조**
동적 HTML 추가 로직을 별도의 파일로 분리하여 재사용성을 높일 수 있습니다.

### **Step 1: JavaScript 파일로 분리**
`dynamic.js` 파일:
```javascript
// 동적 HTML 생성 및 추가 함수
function createAndAppendElement(parentId, tagName, attributes, innerHTML) {
    var parent = document.getElementById(parentId);
    if (!parent) {
        console.error("Parent element not found!");
        return;
    }

    var newElement = document.createElement(tagName);

    for (var key in attributes) {
        if (attributes.hasOwnProperty(key)) {
            newElement.setAttribute(key, attributes[key]);
        }
    }

    if (innerHTML) {
        newElement.innerHTML = innerHTML;
    }

    parent.appendChild(newElement);
}
```

### **Step 2: HTML에서 호출**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Modular Dynamic HTML</title>
</head>
<body>
    <div id="content"></div>

    <!-- dynamic.js 로드 -->
    <script src="dynamic.js"></script>
    
    <!-- 동적 HTML 생성 -->
    <script>
        createAndAppendElement("content", "h1", { class: "header" }, "Dynamic Header");
        createAndAppendElement("content", "button", { class: "btn", onclick: "alert('Button clicked!')" }, "Click Me");
    </script>
</body>
</html>
```

---

## **3. 효율성을 높이는 팁**

### **(1) `DocumentFragment` 사용**
많은 요소를 한 번에 추가하려면 `DocumentFragment`를 사용해 DOM 조작을 최소화할 수 있습니다.

```javascript
function addMultipleElements(parentId, elements) {
    var parent = document.getElementById(parentId);
    if (!parent) return;

    var fragment = document.createDocumentFragment();

    elements.forEach(function(element) {
        var newElement = document.createElement(element.tagName);

        for (var key in element.attributes) {
            if (element.attributes.hasOwnProperty(key)) {
                newElement.setAttribute(key, element.attributes[key]);
            }
        }

        if (element.innerHTML) {
            newElement.innerHTML = element.innerHTML;
        }

        fragment.appendChild(newElement);
    });

    parent.appendChild(fragment); // 한 번에 DOM에 삽입
}
```

**사용 예제:**
```javascript
addMultipleElements("container", [
    { tagName: "div", attributes: { class: "box" }, innerHTML: "Box 1" },
    { tagName: "p", attributes: { class: "text" }, innerHTML: "Paragraph 1" },
]);
```

---

### **(2) 템플릿 문자열 활용**
복잡한 구조의 HTML을 동적으로 추가하려면 템플릿 문자열과 `insertAdjacentHTML`을 사용할 수도 있습니다.

```javascript
function addTemplateHTML(parentId, htmlString) {
    var parent = document.getElementById(parentId);
    if (!parent) return;

    parent.insertAdjacentHTML("beforeend", htmlString);
}

// 사용 예제:
addTemplateHTML("container", `
  <div class="card">
      <h2>Card Title</h2>
      <p>This is a card description.</p>
  </div>
`);
```

---

## **4. 비교 요약**

| 방법                     | 특징                                                                 |
|--------------------------|----------------------------------------------------------------------|
| `createElement` + `appendChild` | DOM API를 활용한 기본 방식으로 성능이 좋고 안전함.                              |
| `DocumentFragment`       | 여러 요소를 한 번에 추가할 때 DOM 조작 횟수를 줄여 성능 최적화 가능.                   |
| `insertAdjacentHTML`     | 텍스트 기반으로 빠르게 HTML을 삽입 가능하지만 XSS(스크립트 삽입 공격)에 취약할 수 있음. |

---

## **결론**
- ES5에서 동적으로 HTML을 추가하려면 `createElement`와 `appendChild`를 사용하는 것이 가장 안전하고 효율적입니다.
- 많은 요소를 추가해야 한다면 `DocumentFragment`로 성능을 최적화할 수 있습니다.
- 모듈화를 위해 함수를 별도의 파일로 분리하고 필요한 곳에서 호출하여 재사용성을 높이는 것이 좋습니다.

출처
[1] [Javascript] 동적 html 요소 추가하기 - 어제 오늘 내일 - 티스토리 https://hianna.tistory.com/866
[2] [Javascript] HTML 태그 동적 추가하는 다양한 방법들 https://blogcreator.blog/post/29
[3] Javascript를 이용해 HTML에 동적으로 태그 추가 - P&H_Station https://ludeno-studying.tistory.com/82
[4] 'javascript ES5 정리' 카테고리의 글 목록 - ohmyfun - 티스토리 https://ohmyfun.tistory.com/category/javascript%20ES5%20%EC%A0%95%EB%A6%AC
[5] [Javascript] JS로 HTML 요소 생성 및 추가하기 - 개발 잘하기 - 티스토리 https://welldoing-dogfoot.tistory.com/18
[6] [JS] 자바스크립트 ES5 ES6 차이 - 허범성 - 티스토리 https://hbsowo58.tistory.com/407
[7] JavaScript - script async와 defer의 차이점, 공부방향 - REXIANN https://rexiann.github.io/2020/09/21/javascript-mode-use-strict.html
[8] 2. 기본 문법 | 자바스크립트 기초 - 짧굵배 https://dinfree.com/lecture/frontend/123_js_2.html

