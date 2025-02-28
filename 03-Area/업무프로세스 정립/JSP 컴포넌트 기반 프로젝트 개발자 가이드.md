---
tags: 
created: 2024-12-13 10:34
modified: 2024-12-13 10:34
related: []
updated: 2024-12-13 11:16
---
## 1. 개요  
### 1.1 문서 목적  
본 문서는 JSP 컴포넌트 기반 웹 애플리케이션 개발을 위한 가이드를 제공합니다  
### 1.2 프로젝트 구조(예시)  
```  
project-root/
├── src/
│   └── main/
│       ├── java/
│       │   └── org/
│       │       └── odinue/
│       │           └── umspilot/
│       │               ├── controller/
│       │               │   └── RouterController.java
│       │               ├── UmsPilotApplication.java
│       │               └── ServletInitializer.java
│       └── webapp/
│           ├── WEB-INF/
│           │   ├── tags/
│           │   │   ├── button/
│           │   │   │   └── basic.tag
│           │   │   ├── grid/
│           │   │   │   └── basic.tag
│           │   │   └── layouts/
│           │   │       └── button-group/
│           │   │           └── buttonGroup.tag
│           │   └── views/
│           │       ├── components/
│           │       │   └── index.jsp
│           │       └── index.jsp
│           ├── components/
│           │   ├── button/
│           │   │   ├── include/
│           │   │   │   └── button.js
│           │   │   └── tag/
│           │   │       └── button.js
│           │   └── table/
│           │       └── tag/
│           │           └── table.js
│           └── js/
│               ├── api/
│               │   └── index.js -- API 연동 scriptit
```  
## 2. 컴포넌트 개발 가이드  
### 2.1 컴포넌트 설계의 기본 원칙
- 단일 책임 원칙: 하나의 컴포넌트는 하나의 기능만 담당  
- 재사용성: 다양한 상황에서 재사용 가능하도록 설계  
- 독립성: 다른 컴포넌트와의 의존성 최소화  
- 명확한 인터페이스: 필요한 파라미터를 명확히 정의  
### 2.2 컴포넌트의 구성 요소
컴포넌트는 네 가지 주요 부분으로 구성됩니다:
- 데이터 부분 (attribute): 컴포넌트가 사용할 데이터를 정의합니다.
- 화면 부분 (html): 사용자에게 보여질 화면을 만듭니다.
- 동작 부분 (script): 버튼 클릭 같은 사용자 동작을 처리합니다
- 디자인 부분 (style): 컴포넌트의 모양을 꾸밉니다
### 2.3 샘플 코드

```jsp
<%@ tag language="java" pageEncoding="UTF-8" %>  
<%@ attribute name="id" required="true" %>  
<%@ attribute name="cssClass" required="false" %>  
<%@ attribute name="style" required="false" %>  
<%@ attribute name="onClick" required="false" %>  
<%@ attribute name="disabled" required="false" type="java.lang.Boolean" %>  
<%@ attribute name="toolTip" required="false" %>  
<%@ attribute name="title" required="false" %>  
<%@ attribute name="size" required="false" %>  
<%@ attribute name="callback" required="false" %>  
  
<button  
        id="${id}"  
        type="button"  
        class="btn ${empty cssClass ? 'btn-default' : cssClass} ${empty size ? 'btn-medium' : 'btn-'.concat(size)}"  
        style="${style}"  
        ${disabled ? 'disabled' : ''}  
        ${empty onClick ? '' : 'onClick="'.concat(onClick).concat('"')}  
        ${empty callback ? '' : 'data-callback="'.concat(callback).concat('"')}>  
    <span class="button-content">  
        <span class="button-text">${empty title ? '' : title}</span>  
    </span></button>  
  
<script>  
    document.addEventListener('DOMContentLoaded', function() {  
        const buttonId = '${id}';  
        BUTTON_BASIC.init(buttonId);  
    });  
  
    BUTTON_BASIC = {  
        // Instances 저장소 추가  
        _instances: new Map(),  
        // callbacks 함수 저장소 추가  
        _callbacks: new Map(),  
        // state 저장소 추가  
        _state: new Map(),  
  
        // 초기화 메서드  
        init: function(buttonId) {  
            if (!this._instances.has(buttonId)) {  
                const button = this._getComponent(buttonId);  
                if (button) {  
                    const initialState = Object.entries(button.dataset).reduce((acc, [key, value]) => {  
                        acc[key] = value;  
                        return acc;  
                    }, {});  
  
                    // 초기 상태 설정  
                    this._state.set(buttonId, {  
                        text: '${title}',  
                        disabled: '${disabled}' === 'true',  
                        className: button.className,  
                        style: '${style}' || null,  
                        toolTip: '${toolTip}' === 'true',  
                        onClick: '${onClick}',  
                        ...initialState  
                    });  
  
                    this._instances.set(buttonId, button);  
                    this.beforeCreate(buttonId);  
                    this._bindEvents(button);  
  
                    const callback = button.getAttribute('data-callback');  
                    if (callback) {  
                        this._callbacks.set(buttonId, window[callback]);  
                    }  
  
                    this.created(buttonId);  
                    this.mounted(buttonId);  
                }  
            } else {  
                console.error('Button already initialized:', buttonId);  
            }  
        },  
        // component 여부  
        _getComponent: function(buttonId) {  
            return document.getElementById(buttonId);  
        },  
        // 이벤트 바인딩 메서드  
        _bindEvents: function(button) {  
            button.addEventListener('click', function() {  
                const callback = button.getAttribute('data-callback');  
                console.log(callback);  
                if (callback) {  
                    console.log('Callback:', callback);  
                    window[callback]();  
                }  
            });  
        },  
        // 상태 관리 메서드  
        setState: function(buttonId, newState) {  
            const currentState = this._state.get(buttonId) || {};  
            const updatedState = { ...currentState, ...newState };  
            this._state.set(buttonId, updatedState);  
            this._render(buttonId);  
        },  
        // 상태 가져 오기  
        getState: function(buttonId) {  
            return this._state.get(buttonId);  
        },  
        // 렌더링 메서드  
        _render: function(buttonId) {  
            const button = this._getComponent(buttonId);  
            const state = this._state.get(buttonId);  
  
            if (button && state) {  
                if (state.text) {  
                    const textSpan = button.querySelector('.button-text');  
                    if (textSpan) textSpan.innerHTML = state.text;  
                }  
  
                if (state.disabled !== undefined) {  
                    button.disabled = state.disabled;  
                }  
  
                if (state.className) {  
                    button.className = state.className;  
                }  
  
                if (state.style) {  
                    button.style = state.style;  
                }  
  
                if (state.toolTip) {  
                    button.title = state.toolTip;  
                }  
  
                if (state.onClick) {  
                    button.onclick = state.onClick;  
                }  
  
                console.log('Rendered:', state);  
            }  
        },  
        // 라이프 사이클 훅  
        beforeCreate: function(buttonId) {  
            console.log('beforeCreate:', buttonId);  
        },  
  
        created: function(buttonId) {  
            console.log('created:', buttonId);  
        },  
  
        mounted: function(buttonId) {  
            console.log('mounted:', buttonId);  
        },  
  
        beforeDestroy: function(buttonId) {  
            console.log('beforeDestroy:', buttonId);  
        },  
        // text 설정  
        setText: function(buttonId, text) {  
            this.setState(buttonId, { text });  
            const callback = this._callbacks.get(buttonId);  
            if (callback) {  
                callback(text);  
            }  
        },  
  
        setDisabled: function(buttonId, disabled) {  
            this.setState(buttonId, { disabled });  
        },  
  
        destroy: function(buttonId) {  
            this.beforeDestroy(buttonId);  
            this._instances.delete(buttonId);  
            this._callbacks.delete(buttonId);  
            this._state.delete(buttonId);  
        }  
    };  
  
</script>  
  
<!-- 기본 CSS --><style>  
  
    .btn {  
        padding: 8px 16px;  
        border-radius: 4px;  
        border: none;  
        cursor: pointer;  
        font-size: 14px;  
        transition: all 0.3s;  
        display: inline-flex;  
        align-items: center;  
        justify-content: center;  
        gap: 8px;  
    }  
  
    .btn-small {  
        padding: 4px 8px;  
        font-size: 12px;  
    }  
  
    .btn-medium {  
        padding: 8px 16px;  
        font-size: 14px;  
    }  
  
    .btn-large {  
        padding: 12px 24px;  
        font-size: 16px;  
    }  
  
    .btn-default {  
        background-color: #f0f0f0;  
        color: #333;  
    }  
  
    .btn-primary {  
        background-color: #007bff;  
        color: white;  
    }  
  
    .btn-success {  
        background-color: #28a745;  
        color: white;  
    }  
  
    .btn-danger {  
        background-color: #dc3545;  
        color: white;  
    }  
  
    .btn:hover:not(:disabled) {  
        opacity: 0.8;  
    }  
  
    .btn:disabled {  
        opacity: 0.5;  
        cursor: not-allowed;  
    }  
  
    .loading-spinner {  
        width: 16px;  
        height: 16px;  
        border: 2px solid #ffffff;  
        border-radius: 50%;  
        border-top-color: transparent;  
        animation: spin 1s linear infinite;  
    }  
  
    @keyframes spin {  
        to { transform: rotate(360deg); }  
    }  
</style>
```  

### 3. 페이지 구성 가이드
페이지는 여러 컴포넌트들을 조합해서 만듭니다. 이때 중요한 점들은:
1. 데이터 관리: 페이지에서 데이터를 관리하고, 필요한 데이터를 각 컴포넌트에 전달합니다.
2. 이벤트 처리: 컴포넌트에서 어떤 일이 발생했을 때 (예: 버튼 클릭) 실행될 함수(callback)를 미리 정의해둡니다.
3. 구조적 배치: 컴포넌트들을 페이지 내에서 적절히 배치하여 사용자가 보기 좋고 사용하기 편한 화면을 만듭니다.

이렇게 구성하면 마치 블록을 조립하듯이 여러 컴포넌트를 조합해서 원하는 페이지를 만들 수 있습니다.
### 3.1 레이아웃 구성 샘플코드  
```jsp  
<%@ page contentType="text/html;charset=UTF-8" language="java" %>  
  
<!DOCTYPE html>  
<html>  
<head>  
    <title>Home Page</title>  
    <!-- 태그 라이브러리 -->  
    <script src="${pageContext.request.contextPath}/js/router/index.js"></script>  
    <%@ taglib prefix="button" tagdir="/WEB-INF/tags/button" %>  
    <%@ taglib prefix="grid" tagdir="/WEB-INF/tags/grid" %>  
    <%@ taglib prefix="layout" tagdir="/WEB-INF/tags/layouts/button-group" %>  
</head>  
<body>  
  
<h1>Components</h1>  
<br>  
<div>  
    <button:basic id="tagBasicButton"  
                  cssClass="btn-primary"  
                  callback="handleTextChange"  
                  title="기본 버튼"/>  
  
    <button:basic id="tagLoadingButton"  
                  cssClass="btn-success"  
                  onClick="handleTagLoading()"  
                  title="저장 시작"/>  
  
    <button:basic id="routerButton"  
                title="메인 페이지 이동"  
                cssClass="btn-primary"  
                onClick="handleRouterButtonClick()"/>  
</div>  
  
<br>  
  
<div>  
    <grid:basic  
            id="tagTable"  
            striped="true"  
            bordered="true"  
            data="testData"  
            columns="columns"  
            pageSize="5"  
    />  
</div>  
<br>  
  
<div>  
    <layout:buttonGroup callback="handleTagButtonGroupCallBack"/>  
</div>  
  
<script>  
    let testData = [  
        { id: 1, name: "John", age: 30 },  
        { id: 2, name: "Jane", age: 25 },  
        // ... 더 많은 데이터  
    ];  
  
    let columns = [  
        { key: 'id', title: 'ID', align: 'center' },  
        { key: 'name', title: 'Name' },  
        { key: 'age', title: 'Age', align: 'right' }  
    ];  
  
    function handleTagButtonGroupCallBack() {  
        alert('콜백');  
        BUTTON_GROUP.setData('tagBasicButtonGroup', {  
            text: '새로운 텍스트'  
        });  
    }  
  
    function handleRouterButtonClick() {  
        // Router.router.redirect('/');  
        Router.back();  
    }  
  
    document.addEventListener("DOMContentLoaded", function () {  
        new TableTagComponent('tagTable', {  
            data: testData,  
            columns: columns,  
            striped: true,  
            bordered: true,  
            pageSize: 5  
        });  
    });  
  
    function handleTextChange(text) {  
        console.log('버튼 텍스트가 변경됨:', text);  
    }  
  
    // tag 방식  
    function handleTagLoading() {  
        if (!BUTTON_BASIC) {  
            console.error('ButtonTag not loaded');  
            return;  
        }  
  
        BUTTON_BASIC.setState('tagBasicButton', {  
            text: '새로운 텍스트'  
        });  
  
        const state = BUTTON_BASIC.getState('tagBasicButton');  
        console.log('현재 버튼 상태:', state);  
  
        setTimeout(function() {  
            BUTTON_BASIC.setText('tagLoadingButton', '저장완료');  
            // alert("태그 버튼 로딩 완료!");  
            BUTTON_BASIC.setText('tagBasicButton', '변경');  
        }, 1000);  
    }  
  
    function handleTagLoadingGroup() {  
        if (!BUTTON_BASIC) {  
            console.error('ButtonTag not loaded');  
            return;  
        }  
  
        BUTTON_BASIC.setState('tagBasicButtonGroup', {  
            text: '새로운 텍스트'  
        });  
    }  
  
</script>  
<script src="${pageContext.request.contextPath}/components/table/tag/table.js"></script>  
</body>  
</html> 
```
