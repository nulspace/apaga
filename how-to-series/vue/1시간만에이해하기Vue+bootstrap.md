### 1시간만에 끝내는 Vue.js(v2) 입문

[TOC]

출처: [한시간만에 끝내는 Vue.js 입문](https://www.youtube.com/watch?v=sqH0u8wN4Rs)

> 어떤 프레임워크를 선택할 것인가?
>
> - React
> - Vue
>
> 한가지를 확실히 파고 나면, 내부적으로 비슷한 원리라서 이해하기가 쉽다.



#### Vue.js 개요

- 웹프론트엔드 프레임워크이다.
- 컴포넌트 기반의 SPA를 구축할 수 있게 해 주는 프레임워크이지만 ...
  - 컴포넌트: 재사용 가능하도록 구조화 한 것
  - Single Page Application
    - 하나의 페이지 안에서 필요한 부분만 로딩되는 형태로 적은 트래픽 양
    - 최초 페이지 로딩시에 모든 js, 모든 css를 로딩하기때문에 사이즈가 커질 수록 느르게 되는 단점도 ...



#### Vue 개발 환경

###### Vue CLI 설치

프로젝트 기본 구조, 필요한 라이브러리 설치 및 webpack 설정의  boilerplate를 사용할 수 있게 하는 명령어이다.

```powershell
cmd> npm install -g @vue/cli

cmd> vue --version
@vue/cli 5.0.8
```

> 확장팩 설치 Vetur - VS Code용 VUE 툴



###### Vue 프로젝트 구조

```
[project-name]
    > node_modules
    ∨ public
    	favicon.ico
    	index.html
    ∨ src
    	∨ assets
    		logo.png
    	∨ components
    		HelloWorld.vue
    	App.vue
    	main.js					// entry-point 파일 (#app를 public/index.html에서 찾는다.)
    	.gitignore
    	babel.config.js
    	jsconfig.json
    	package-lock.json
    	package.json
    	README.md
    	vue.config.js
```





#### 프로젝트 개발 환경 구성

- VUE CLI는 v5 버전에서 v2를 선택함

  ```powershell
  cmd> vue create vue2
  Vue CLI v5.0.8
  ? Please pick a preset: (Use arrow keys)
    vue-project ([Vue 3] babel, router, vuex, eslint)
    Default ([Vue 3] babel, eslint)
  > Default ([Vue 2] babel, eslint)
    Manually select features
  
  cmd> cd vue2
  cmd> yarn serve
  
     <Ctrl + C>
  ```

- bootstrap을 사용하기 위해 모듈을 추가함

  ```powershell
  cmd> npm install vue-router@3 --save
  
  cmd> yarn add bootstrap bootstrap-vue
  
  cmd> yarn serve
  ```



#### 기본 레이아웃 작성

- main.js

  - 부트스트랩 관련한 코드 추가 (import ... 그리고 Vue.use ...)
  - 이때, 순서가 중요하다고 함.
  - 작성 가이드는 [여기](https://bootstrap-vue.org/docs#using-module-bundlers)에 자세히 있음

  ```vue	
  import Vue from 'vue'
  import { BootstrapVue, IconsPlugin } from 'bootstrap-vue'
  import App from './App.vue'
  
  // Import Bootstrap and BootstrapVue CSS files (order is important)
  import 'bootstrap/dist/css/bootstrap.css'
  import 'bootstrap-vue/dist/bootstrap-vue.css'
  
  // Make BootstrapVue available throughout your project
  Vue.use(BootstrapVue)
  // Optionally install the BootstrapVue icon components plugin
  Vue.use(IconsPlugin)
  
  Vue.config.productionTip = false
  
  new Vue({
    render: h => h(App),
  }).$mount('#app')
  
  ```

- App.vue

  첫화면에서 레이아웃을 구성하기에 앞서서

  1. 라우터를 만들고

     > 이때, 오류 `Component name "header" should always be multi-word vue/multi-word-component-names`가 발생하면 package.json 파일에서 `"rules": {   "vue/multi-word-component-names": 0  }`를 수정하고 restart 해야 한다.

  2. 라우터에 따른 페이지를 구성

  3. 마지막으로 App.vue에 추가될 헤더(Header.vue)를 작성한다. 내부는  [Navbar](https://getbootstrap.com/docs/5.2/components/navbar/) 예제를 그대로 사용하여 `Header.vue`에 다음과 같이 추가한다.

  >**소스** `components/layout/Header.vue`
  >
  >```vue
  ><template>
  >	<!-- 
  >		https://bootstrap-vue.org/docs/components/nav
  >	-->
  ></template>
  >
  ><script>
  >export default {
  >name: "header",
  >};
  ></script>
  >
  >```

  

  이제 `App.vue`를 다음과 같이  `Header.vue`를 import하고 `export ... components`를 통해 컴포넌트를 사용할 수 있게 한 후에, `template`에서 사용하면 된다. 

  ```vue
  <template>
    <div id="app">
      <Header />
    </div>
  </template>
  
  <script>
  import Header from './components/layout/Header.vue'
  
  export default {
    name: 'App',
    components: {
      Header
    }
  }
  </script>
  ```

  

#### 라우팅 설정하기

- 여기에 순서



- 라우터 사용

  - 작성한 router 파일을 임포트하고
  - 선언한 router를 App 컴포넌트가 사용할 수 있도록 `new Vue(...)`추가

  > (소스) main.js
  >
  > ```javascript
  > import Vue from 'vue'
  > import { BootstrapVue, IconsPlugin } from 'bootstrap-vue'
  > import App from './App.vue'
  > import router from "./router"
  > 
  > // Import Bootstrap and BootstrapVue CSS files (order is important)
  > import 'bootstrap/dist/css/bootstrap.css'
  > import 'bootstrap-vue/dist/bootstrap-vue.css'
  > 
  > // Make BootstrapVue available throughout your project
  > Vue.use(BootstrapVue)
  > // Optionally install the BootstrapVue icon components plugin
  > Vue.use(IconsPlugin)
  > 
  > Vue.config.productionTip = false
  > 
  > new Vue({
  > router, render: h => h(App),
  > }).$mount('#app')
  > 
  > ```
  >
  > 

- 라우터 정의

  >소스 `router.js`
  >
  >```javascript
  >import Vue from "vue";
  >import VueRouter from "vue-router";
  >
  >import Home from "./views/Home";
  >import About from "./views/About";
  >
  >Vue.use(VueRouter);
  >const router = new VueRouter({
  >mode: "history",
  >routers: [
  >{
  > path: '/',
  > component: Home
  >},
  >{
  > path: '/about',
  > component: About
  >}
  >]
  >});
  >
  >export default router;
  >```

- 라우팅 페이지

  >**소스** `views/Home.vue`
  >
  >```vue
  ><template>
  ><div>
  ><h1>Welcome to Home!</h1>
  ></div>
  ></template>
  >
  ><script>
  >export default {};
  ></script>
  >```
  >
  >
  >
  >**소스** `views/About.vue`
  >
  >```vue
  ><template>
  ><div><h1>About Page</h1></div>
  ></template>
  >
  ><script>
  >export default {};
  ></script>
  >```
  >
  >
  >
  >



### VUE 예제

#### 데이터핸들링

* 2-way 바인딩
* data()  메소드 내에서 return한  `{ 리터럴 } 객체`를 통해서 `<template>`영역에 있는 directive를 이용해 바인딩한다.
* 다양한[ Directies](https://vuejs.org/api/built-in-directives.html)는 홈페이지를 방문해서 자세히 볼 것



#### 이벤트핸들링

* oo



#### 사용자 정의 메소드

* oo



#### 컴포넌트 라이프사이클

* oo





### VUE 컴포넌트 라이프사이클 다이어그램

![VUE 컴포넌트 라이프사이클](https://velog.velcdn.com/images%2Fgudwnsepdy%2Fpost%2Ffb547463-bd0e-4555-acf0-db48e2fdd9b8%2Fimage.png)
