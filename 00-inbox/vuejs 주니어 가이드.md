---
tags: 
created: 2025-02-28 01:06
modified: 2025-02-28 01:06
related: []
updated: 2025-03-01 01:09
---
## 목차

1. [Vue.js 소개](https://claude.ai/chat/cc4be903-1087-498c-97c1-b70c1326cdbf#1-vuejs-%EC%86%8C%EA%B0%9C)
2. [설계 철학](https://claude.ai/chat/cc4be903-1087-498c-97c1-b70c1326cdbf#2-%EC%84%A4%EA%B3%84-%EC%B2%A0%ED%95%99)
3. [주요 개념 및 문법](https://claude.ai/chat/cc4be903-1087-498c-97c1-b70c1326cdbf#3-%EC%A3%BC%EC%9A%94-%EA%B0%9C%EB%85%90-%EB%B0%8F-%EB%AC%B8%EB%B2%95)
4. [컴포넌트 시스템](https://claude.ai/chat/cc4be903-1087-498c-97c1-b70c1326cdbf#4-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8-%EC%8B%9C%EC%8A%A4%ED%85%9C)
5. [라우팅](https://claude.ai/chat/cc4be903-1087-498c-97c1-b70c1326cdbf#5-%EB%9D%BC%EC%9A%B0%ED%8C%85)
6. [상태 관리](https://claude.ai/chat/cc4be903-1087-498c-97c1-b70c1326cdbf#6-%EC%83%81%ED%83%9C-%EA%B4%80%EB%A6%AC)
7. [개발 환경 설정](https://claude.ai/chat/cc4be903-1087-498c-97c1-b70c1326cdbf#7-%EA%B0%9C%EB%B0%9C-%ED%99%98%EA%B2%BD-%EC%84%A4%EC%A0%95)
8. [API 호출과 비동기 처리](https://claude.ai/chat/cc4be903-1087-498c-97c1-b70c1326cdbf#8-api-%ED%98%B8%EC%B6%9C%EA%B3%BC-%EB%B9%84%EB%8F%99%EA%B8%B0-%EC%B2%98%EB%A6%AC)
9. [주요 팁 및 모범 사례](https://claude.ai/chat/cc4be903-1087-498c-97c1-b70c1326cdbf#9-%EC%A3%BC%EC%9A%94-%ED%8C%81-%EB%B0%8F-%EB%AA%A8%EB%B2%94-%EC%82%AC%EB%A1%80)
10. [흔한 실수와 해결 방법](https://claude.ai/chat/cc4be903-1087-498c-97c1-b70c1326cdbf#10-%ED%9D%94%ED%95%9C-%EC%8B%A4%EC%88%98%EC%99%80-%ED%95%B4%EA%B2%B0-%EB%B0%A9%EB%B2%95)
11. [실제 프로젝트 시작하기](https://claude.ai/chat/cc4be903-1087-498c-97c1-b70c1326cdbf#11-%EC%8B%A4%EC%A0%9C-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0)

## 1. Vue.js 소개

### 1.1 Vue.js란?

Vue.js는 사용자 인터페이스를 구축하기 위한 프로그레시브 JavaScript 프레임워크입니다. 다른 모놀리식 프레임워크와 달리 Vue는 점진적으로 채택할 수 있도록 설계되었습니다. 핵심 라이브러리는 뷰 레이어에만 초점을 맞추어 다른 라이브러리나 기존 프로젝트와 쉽게 통합할 수 있습니다.

### 1.2 주요 특징

- **선언적 렌더링**: HTML을 확장하는 템플릿 구문을 사용하여 데이터에 기반한 DOM 출력을 선언적으로 표현
- **반응형 시스템**: 데이터가 변경되면 자동으로 UI가 업데이트
- **컴포넌트 기반 아키텍처**: 재사용 가능한 컴포넌트로 애플리케이션 구축
- **가상 DOM**: 효율적인 렌더링 성능 제공
- **가벼운 크기**: 최소화 및 압축 후 약 20KB
- **점진적 프레임워크**: 라이브러리로 사용하거나 완전한 프레임워크로 확장 가능

## 2. 설계 철학

### 2.1 점진적 채택

Vue.js의 주요 철학 중 하나는 **점진적 채택**입니다. 기존 애플리케이션의 일부분에만 Vue를 적용할 수 있으며, 필요에 따라 확장할 수 있습니다.

### 2.2 접근성

Vue는 학습 곡선이 완만하여 초보자도 쉽게 배울 수 있습니다. HTML, CSS, JavaScript에 대한 기본 지식만 있으면 시작할 수 있습니다.

### 2.3 반응형 시스템

Vue의 핵심은 **반응형 시스템**입니다. 데이터 변경을 감지하고 연관된 DOM을 자동으로 업데이트합니다.

### 2.4 단방향 데이터 흐름

Vue는 상위에서 하위로 흐르는 단방향 데이터 흐름을 따릅니다. 이는 애플리케이션의 데이터 흐름을 추적하고 디버깅하기 쉽게 만듭니다.

## 3. 주요 개념 및 문법

### 3.1 Vue 인스턴스 생성

```javascript
const app = new Vue({
  el: '#app',          // Vue가 마운트될 DOM 요소
  data: {              // 애플리케이션 상태 데이터
    message: '안녕하세요 Vue!'
  },
  methods: {           // 메서드 정의
    reverseMessage() {
      this.message = this.message.split('').reverse().join('')
    }
  },
  computed: {          // 계산된 속성
    reversedMessage() {
      return this.message.split('').reverse().join('')
    }
  },
  watch: {             // 데이터 감시
    message(newVal, oldVal) {
      console.log(`message가 ${oldVal}에서 ${newVal}로 변경되었습니다.`)
    }
  },
  created() {          // 라이프사이클 훅
    console.log('Vue 인스턴스가 생성되었습니다.')
  }
})
```

### 3.2 템플릿 문법

**데이터 바인딩**

```html
<!-- 텍스트 보간법 -->
<span>{{ message }}</span>

<!-- HTML 출력 (XSS 주의) -->
<span v-html="rawHtml"></span>

<!-- 속성 바인딩 -->
<div v-bind:id="dynamicId"></div>
<!-- 약식 -->
<div :id="dynamicId"></div>

<!-- 조건부 렌더링 -->
<div v-if="seen">보입니다</div>
<div v-else-if="condition">다른 조건</div>
<div v-else>보이지 않습니다</div>

<!-- v-show (CSS display 속성을 토글) -->
<div v-show="seen">토글됩니다</div>

<!-- 리스트 렌더링 -->
<ul>
  <li v-for="(item, index) in items" :key="item.id">
    {{ index }}: {{ item.text }}
  </li>
</ul>

<!-- 이벤트 핸들링 -->
<button v-on:click="doSomething">클릭</button>
<!-- 약식 -->
<button @click="doSomething">클릭</button>

<!-- 이벤트 수정자 -->
<form @submit.prevent="onSubmit">...</form>

<!-- 키 수정자 -->
<input @keyup.enter="submit">

<!-- 양방향 바인딩 -->
<input v-model="message">
```

### 3.3 주요 디렉티브

|디렉티브|설명|
|---|---|
|`v-bind`|HTML 속성에 Vue 데이터 바인딩|
|`v-on`|이벤트 리스너 추가|
|`v-model`|양방향 데이터 바인딩|
|`v-if`, `v-else-if`, `v-else`|조건부 렌더링 (DOM에서 요소 추가/제거)|
|`v-show`|조건부 표시 (CSS display 속성 토글)|
|`v-for`|배열이나 객체를 기반으로 항목 반복|
|`v-html`|원시 HTML 출력 (XSS 취약점 주의)|
|`v-text`|텍스트 콘텐츠 설정 (`{{ }}` 대신 사용 가능)|
|`v-cloak`|Vue가 컴파일을 완료할 때까지 요소를 숨김|
|`v-pre`|컴파일 건너뛰기|
|`v-once`|일회성 보간 (데이터가 변경되어도 업데이트 안 함)|

### 3.4 계산된 속성과 감시자

**계산된 속성(Computed Properties)**

```javascript
computed: {
  // 계산된 getter
  reversedMessage() {
    return this.message.split('').reverse().join('')
  },
  
  // getter와 setter 모두 가진 계산된 속성
  fullName: {
    get() {
      return this.firstName + ' ' + this.lastName
    },
    set(newValue) {
      const names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
```

**감시자(Watchers)**

```javascript
watch: {
  // 기본 감시자
  message(newVal, oldVal) {
    console.log(`message가 변경됨: ${oldVal} -> ${newVal}`)
  },
  
  // 객체의 깊은 속성 감시
  'user.profile': {
    handler(newVal) {
      console.log('프로필이 업데이트됨')
    },
    deep: true
  },
  
  // 즉시 실행하는 감시자
  searchQuery: {
    handler(newVal) {
      this.fetchResults(newVal)
    },
    immediate: true
  }
}
```

## 4. 컴포넌트 시스템

### 4.1 컴포넌트 정의

**전역 컴포넌트**

```javascript
Vue.component('button-counter', {
  data() {
    return {
      count: 0
    }
  },
  template: `
    <button @click="count++">
      클릭 횟수: {{ count }}
    </button>
  `
})
```

**지역 컴포넌트**

```javascript
const ButtonCounter = {
  data() {
    return {
      count: 0
    }
  },
  template: `
    <button @click="count++">
      클릭 횟수: {{ count }}
    </button>
  `
}

new Vue({
  el: '#app',
  components: {
    'button-counter': ButtonCounter
  }
})
```

### 4.2 단일 파일 컴포넌트 (SFC)

```vue
<!-- ButtonCounter.vue -->
<template>
  <button @click="count++">
    클릭 횟수: {{ count }}
  </button>
</template>

<script>
export default {
  name: 'ButtonCounter',
  data() {
    return {
      count: 0
    }
  }
}
</script>

<style scoped>
button {
  background-color: #f5f5f5;
  border: none;
  padding: 10px;
  border-radius: 4px;
}
</style>
```

### 4.3 Props (속성)

```vue
<!-- ChildComponent.vue -->
<template>
  <div>
    <h3>{{ title }}</h3>
    <p>{{ message }}</p>
  </div>
</template>

<script>
export default {
  props: {
    // 기본 타입 체크
    title: String,
    // 더 자세한 정의
    message: {
      type: String,
      required: true,
      default: '기본 메시지'
    },
    // 객체/배열 기본값은 팩토리 함수에서 반환해야 함
    author: {
      type: Object,
      default() {
        return { name: '익명' }
      }
    },
    // 사용자 정의 유효성 검사
    propE: {
      validator(value) {
        return ['success', 'warning', 'danger'].includes(value)
      }
    }
  }
}
</script>
```

**Props 사용**

```vue
<template>
  <div>
    <child-component 
      title="환영합니다" 
      message="Vue.js 학습하기"
      :author="{ name: '홍길동' }"
      propE="success"
    />
  </div>
</template>
```

### 4.4 이벤트 발신 및 수신

**자식 컴포넌트에서 이벤트 발신**

```vue
<template>
  <button @click="incrementCounter">증가</button>
</template>

<script>
export default {
  methods: {
    incrementCounter() {
      this.$emit('increment', 1)  // 이벤트명, 추가 데이터
    }
  }
}
</script>
```

**부모 컴포넌트에서 이벤트 수신**

```vue
<template>
  <div>
    <p>카운터: {{ total }}</p>
    <child-button @increment="updateTotal"></child-button>
  </div>
</template>

<script>
import ChildButton from './ChildButton.vue'

export default {
  components: {
    ChildButton
  },
  data() {
    return {
      total: 0
    }
  },
  methods: {
    updateTotal(value) {
      this.total += value
    }
  }
}
</script>
```

### 4.5 슬롯(Slots)

**기본 슬롯**

```vue
<!-- BaseLayout.vue -->
<template>
  <div class="container">
    <header>
      <slot name="header">기본 헤더 콘텐츠</slot>
    </header>
    <main>
      <slot>기본 콘텐츠</slot>
    </main>
    <footer>
      <slot name="footer">기본 푸터 콘텐츠</slot>
    </footer>
  </div>
</template>
```

**슬롯 사용**

```vue
<template>
  <base-layout>
    <template v-slot:header>
      <h1>여기는 헤더입니다</h1>
    </template>
    
    <p>메인 콘텐츠</p>
    
    <template v-slot:footer>
      <p>연락처 정보</p>
    </template>
  </base-layout>
</template>
```

**범위 슬롯(Scoped Slots)**

```vue
<!-- UserList.vue -->
<template>
  <ul>
    <li v-for="user in users" :key="user.id">
      <slot :user="user">
        {{ user.name }}
      </slot>
    </li>
  </ul>
</template>
```

**범위 슬롯 사용**

```vue
<template>
  <user-list :users="users">
    <template v-slot:default="slotProps">
      <strong>{{ slotProps.user.name }}</strong>
      <em>{{ slotProps.user.email }}</em>
    </template>
  </user-list>
</template>
```

## 5. 라우팅

### 5.1 Vue Router 설치

```bash
npm install vue-router@3.x
```

### 5.2 기본 라우터 설정

```javascript
// router/index.js
import Vue from 'vue'
import VueRouter from 'vue-router'
import Home from '../views/Home.vue'
import About from '../views/About.vue'

Vue.use(VueRouter)

const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home
  },
  {
    path: '/about',
    name: 'About',
    component: About
  },
  {
    path: '/user/:id',
    name: 'User',
    component: () => import('../views/User.vue')  // 지연 로딩
  },
  {
    path: '*',
    component: () => import('../views/NotFound.vue')
  }
]

const router = new VueRouter({
  mode: 'history',  // HTML5 History API 사용 (해시 제거)
  base: process.env.BASE_URL,
  routes
})

export default router
```

### 5.3 라우터 사용

```vue
<template>
  <div id="app">
    <nav>
      <router-link to="/">홈</router-link> |
      <router-link to="/about">소개</router-link> |
      <router-link :to="{ name: 'User', params: { id: 123 }}">사용자</router-link>
    </nav>
    
    <!-- 라우터 뷰 -->
    <router-view/>
  </div>
</template>
```

### 5.4 프로그래밍 방식 네비게이션

```javascript
// 문자열 경로
this.$router.push('/about')

// 객체
this.$router.push({ path: '/about' })

// 이름을 가진 라우트
this.$router.push({ name: 'User', params: { id: 123 } })

// 쿼리 파라미터
this.$router.push({ path: '/search', query: { q: 'vue' } })

// 현재 라우트 파라미터 접근
const userId = this.$route.params.id
```

### 5.5 네비게이션 가드

```javascript
// 전역 가드
router.beforeEach((to, from, next) => {
  if (to.matched.some(record => record.meta.requiresAuth)) {
    // 인증이 필요한 페이지
    if (!auth.isLoggedIn()) {
      next({
        path: '/login',
        query: { redirect: to.fullPath }
      })
    } else {
      next()
    }
  } else {
    next()
  }
})

// 라우트 단위 가드
const routes = [
  {
    path: '/admin',
    component: Admin,
    beforeEnter: (to, from, next) => {
      // ...
    }
  }
]

// 컴포넌트 내부 가드
export default {
  beforeRouteEnter(to, from, next) {
    // 컴포넌트가 렌더링되기 전 호출
    // `this`에 접근할 수 없음
    next(vm => {
      // vm을 통해 컴포넌트 인스턴스에 접근
    })
  },
  beforeRouteUpdate(to, from, next) {
    // 같은 컴포넌트를 재사용할 때 호출
    // `this`에 접근 가능
    next()
  },
  beforeRouteLeave(to, from, next) {
    // 현재 라우트에서 떠날 때 호출
    // `this`에 접근 가능
    const answer = window.confirm('정말 떠나시겠습니까?')
    if (answer) {
      next()
    } else {
      next(false)
    }
  }
}
```

## 6. 상태 관리

### 6.1 Vuex 소개

Vuex는 Vue.js 애플리케이션을 위한 상태 관리 패턴 + 라이브러리입니다. 중앙 집중식 스토어 역할을 하며, 예측 가능한 방식으로 상태를 변경할 수 있습니다.

### 6.2 Vuex 설치

```bash
npm install vuex@3.x
```

### 6.3 기본 스토어 설정

```javascript
// store/index.js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
  state: {
    count: 0,
    todos: []
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    },
    doneTodosCount: (state, getters) => {
      return getters.doneTodos.length
    }
  },
  mutations: {
    // 동기적으로만 작동해야 함
    increment(state, payload = 1) {
      state.count += payload
    },
    addTodo(state, todo) {
      state.todos.push(todo)
    }
  },
  actions: {
    // 비동기 작업을 포함할 수 있음
    incrementAsync({ commit }) {
      setTimeout(() => {
        commit('increment')
      }, 1000)
    },
    async fetchTodos({ commit }) {
      try {
        const response = await fetch('/api/todos')
        const todos = await response.json()
        todos.forEach(todo => {
          commit('addTodo', todo)
        })
      } catch (error) {
        console.error('할 일 목록을 가져오는 중 오류 발생:', error)
      }
    }
  },
  modules: {
    // 스토어 모듈화
    user: {
      namespaced: true,
      state: { /* ... */ },
      getters: { /* ... */ },
      mutations: { /* ... */ },
      actions: { /* ... */ }
    }
  }
})
```

### 6.4 컴포넌트에서 Vuex 사용

```vue
<template>
  <div>
    <p>카운트: {{ count }}</p>
    <button @click="increment">증가</button>
    <button @click="incrementAsync">비동기 증가</button>
    
    <h3>할 일 목록</h3>
    <ul>
      <li v-for="todo in todos" :key="todo.id">
        {{ todo.text }}
      </li>
    </ul>
    <p>완료된 할 일: {{ doneTodosCount }}</p>
  </div>
</template>

<script>
import { mapState, mapGetters, mapMutations, mapActions } from 'vuex'

export default {
  computed: {
    // 개별 상태 접근
    countPlusLocal() {
      return this.$store.state.count + this.localCount
    },
    
    // 여러 상태와 게터 매핑
    ...mapState(['count', 'todos']),
    ...mapGetters(['doneTodos', 'doneTodosCount'])
  },
  data() {
    return {
      localCount: 5
    }
  },
  methods: {
    // 개별 mutation/action 호출
    manualIncrement() {
      this.$store.commit('increment', 10)
    },
    manualFetch() {
      this.$store.dispatch('fetchTodos')
    },
    
    // 여러 mutation/action 매핑
    ...mapMutations(['increment']),
    ...mapActions(['incrementAsync', 'fetchTodos'])
  },
  created() {
    this.fetchTodos()
  }
}
</script>
```

## 7. 개발 환경 설정

### 7.1 Vue CLI

Vue CLI는 Vue.js 개발을 위한 전체 시스템을 제공합니다.

**설치**

```bash
npm install -g @vue/cli
# 또는
yarn global add @vue/cli
```

**프로젝트 생성**

```bash
vue create my-project
```

**옵션 선택**

- Babel
- TypeScript
- Router
- Vuex
- CSS Pre-processors (Sass, Less, Stylus)
- Linter / Formatter
- Unit Testing
- E2E Testing

### 7.2 Vue UI

```bash
vue ui
```

이 명령어는 브라우저에서 Vue 프로젝트를 생성하고 관리할 수 있는 그래픽 인터페이스를 엽니다.

### 7.3 프로젝트 구조

```
my-project/
├── public/              # 정적 파일 (index.html 등)
├── src/                 # 소스 코드
│   ├── assets/          # 이미지, 폰트 등의 자산
│   ├── components/      # Vue 컴포넌트
│   ├── views/           # 페이지 컴포넌트
│   ├── router/          # Vue Router 설정
│   ├── store/           # Vuex 스토어
│   ├── services/        # API 서비스
│   ├── utils/           # 유틸리티 함수
│   ├── App.vue          # 루트 컴포넌트
│   └── main.js          # 엔트리 포인트
├── tests/               # 테스트 파일
├── .eslintrc.js         # ESLint 설정
├── .gitignore
├── babel.config.js      # Babel 설정
├── package.json
└── vue.config.js        # Vue CLI 설정
```

## 8. API 호출과 비동기 처리

### 8.1 Axios 설치

```bash
npm install axios
```

### 8.2 API 서비스 모듈

```javascript
// services/api.js
import axios from 'axios'

const apiClient = axios.create({
  baseURL: 'https://api.example.com',
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
    'Accept': 'application/json'
  }
})

// 요청 인터셉터
apiClient.interceptors.request.use(
  config => {
    const token = localStorage.getItem('token')
    if (token) {
      config.headers.Authorization = `Bearer ${token}`
    }
    return config
  },
  error => Promise.reject(error)
)

// 응답 인터셉터
apiClient.interceptors.response.use(
  response => response,
  error => {
    if (error.response && error.response.status === 401) {
      // 인증 오류 처리
    }
    return Promise.reject(error)
  }
)

export default {
  getTodos() {
    return apiClient.get('/todos')
  },
  getTodo(id) {
    return apiClient.get(`/todos/${id}`)
  },
  createTodo(todo) {
    return apiClient.post('/todos', todo)
  },
  updateTodo(id, todo) {
    return apiClient.put(`/todos/${id}`, todo)
  },
  deleteTodo(id) {
    return apiClient.delete(`/todos/${id}`)
  }
}
```

### 8.3 컴포넌트에서 API 호출

```vue
<template>
  <div>
    <div v-if="loading">로딩 중...</div>
    <div v-else-if="error">오류 발생: {{ error.message }}</div>
    <div v-else>
      <h2>{{ todo.title }}</h2>
      <p>{{ todo.description }}</p>
    </div>
  </div>
</template>

<script>
import TodoService from '@/services/api'

export default {
  data() {
    return {
      todo: null,
      loading: true,
      error: null
    }
  },
  async created() {
    try {
      const id = this.$route.params.id
      const response = await TodoService.getTodo(id)
      this.todo = response.data
    } catch (error) {
      this.error = error
    } finally {
      this.loading = false
    }
  }
}
</script>
```

## 9. 주요 팁 및 모범 사례

### 9.1 컴포넌트 명명 규칙

- **PascalCase**: 컴포넌트 정의 및 import 시
    
    ```javascript
    import MyComponent from './MyComponent.vue'
    ```
    
- **kebab-case**: 템플릿에서 사용 시
    
    ```html
    <my-component></my-component>
    ```
    
- **단일 단어 피하기**: HTML 태그와 충돌 방지
    
    ```javascript
    // 좋음
    TodoItem, AppButton, UserProfile
    
    // 나쁨
    Todo, Button, Item
    ```
    

### 9.2 컴포넌트 구성 팁

- **컴포넌트를 작게 유지**
    
    - 한 가지 작업만 수행하는 컴포넌트 작성
    - 파일이 300줄 이상이면 분리 고려
- **props 유효성 검사**
    
    - 항상 `type` 및 필요한 경우 `required`, `default` 지정
- **computed vs methods**
    
    - 결과를 캐싱해야 하는 경우 computed 속성 사용
    - 이벤트 핸들러나 비동기 작업에는 methods 사용
- **data는 함수여야 함**
    
    ```javascript
    // 좋음
    data() {
      return {
        count: 0
      }
    }
    
    // 나쁨 (컴포넌트 재사용 시 데이터 공유 문제)
    data: {
      count: 0
    }
    ```
    

### 9.3 성능 최적화

- **v-for에 항상 key 추가**
    
    ```html
    <li v-for="item in items" :key="item.id">{{ item.text }}</li>
    ```
    
- **무거운 계산은 computed 속성에**
    
    - 계산된 결과를 캐싱하여 재계산 방지
- **큰 목록 렌더링**
    
    - 가상 스크롤 사용 고려 (`vue-virtual-scroller` 등)
- **컴포넌트 지연 로딩**
    
    ```javascript
    const AsyncComponent = () => import('./AsyncComponent.vue')
    ```
    

### 9.4 재사용성 향상

- **믹스인(Mixins)** -
