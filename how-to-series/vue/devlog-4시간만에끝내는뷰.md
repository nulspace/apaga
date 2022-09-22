출처: [개품-4시간만에완벽하게끝내는 Vue.js입문](https://www.youtube.com/watch?v=b0ImUEsqaAA&t=2727s)



VSCODE에 Vetur 확장팩 설치

### VSCODE 개발환경 구성

##### Linter모듈 설치 후, eslint+Standard 코드 컨벤션으로 사용을 선택한 경우

* Prettier - Code formatter: 저장할 때마다 자동 변경 .... 문장 종료는 ; 쌍따옴표 등등

* Settings 에서 forma 검색어로

  * Prettier - Code formatter 옵션을 변경해야 함.

  * package.json 파일 위치에 .prettierrc 파일을 생성하고 옵션을 재정의한다.

    ```json
    {
        "semi": false,
        "bracketSpacing": true,
        "singleQuote": true,
        "useTabs": false,
        "trailingComma": "none",
        "printWidth": 80
    }
    ```

  * pakcage.json 파일의 rules 속성에

    ```json
    {
        "rules": { "space-before-function-paren": "off"}
    }
    ```

##### Vue User Snippets 등록

1. File > Preferences > Configure User Snippets 메뉴 이동

2. 검색창에 vue 입력  → vue 선택

3. 코드 입력

   ```
   "Generate Basic Vue Code": {
   		"prefix":"vue-start",
   		"body": [
   			"<template>\n<div></div>\n</template>\n<script>\n\nexport default{ \n\tname:'',\n\tcomponents:{},\n\tdata(){\n\t\treturn{\n\t\t\tsampleData:''\n\t\t};\n\t},\n\tsetup(){},\n\tcreated(){},\n\tmounted(){},\n\tunmounted(){},\n\tmethods:{}\n}\n</script>"
   		],
   		"description": "Generate Basic Vue Code"
   	}
   ```

   



### 프로젝트 생성



#### 시작하기

아래의 `vue`명령어를 통해 프로젝트를 생성할 때, 프로젝트에 포함되는 성격에 따라서 node 모듈을 선택하는데 기본적으로 [Vue 3] babel, router, vuex, eslint을 포함시킨다. (Linter는 빼먹었네 ^^)

```powershell
cmd> vue create vue4

Vue CLI v5.0.8
? Please pick a preset: vue-project ([Vue 3] babel, router, vuex, eslint)

...

$ cd vue4
$ yarn serve
```



기본 프로젝트에 `router`모듈을 넣으면 `Welcome`페이지에 Home | About 을 자동으로 추가한 예제를 제공한다. 추가된 예시는 다음과 같다.

<img src="4시간만에완벽하게끝내는Vue입문-v3.assets/image-20220922093511497.png" alt="image-20220922093511497" style="zoom:67%;" />

> 실행된 프로젝트 종료하려면 `터미널`창에서 Ctrl+C 키를 누르면 종료된다.



#### VUE 프로젝트 구조



1. 동작메커니즘 개요

   1. `yarn serve`명령어는 main.js 파일에 종속성 있는 번들을 public/index.html에 인젝션한다.
   2. main.js는 위의 index.html 내에 있는 id가  `app`인 div 엘리먼트에 App 컴포넌트를 마운트한다.
   3. App 컴포넌트는 첫페이지의 화면을 구성하고, 여기에서 발생하는 라우팅을 `rounter`에게 위임한다.
   4. `router`는 정의된 path에 따라 `VUE`컴포넌트를 mian.js에 정해진 위치(`<router-view/>`)에 대체시킨다.

2. 어플리케이션 시작점 - main.js

   `yarn serve`라는 명령어를 실행하면 제일 먼저 실행되는 파일이다.

   - (1행) vue 모듈에서 createApp라는 함수를 가져온다.

   - (2행) 현재 위치의 App.vue 파일을 가져와 App 선언하고 이를 코드에서 접근한다.

   - (5행)  public 폴더 아래에 있는 index.html의 id가 `#app`를 가진 div 엘리먼트에 `App` 컴포넌트를 추가(mount)하라. 이때, `store`와 `router`를 사용한다.

     > src/main.js

     ```vue
     import { createApp } from 'vue'
     import App from './App.vue'
     import router from './router'
     import store from './store'
     
     createApp(App).use(store).use(router).mount('#app')
     ```

     VUE는 SPA 프로그램 형태로 main.js에서 사용하는 모든 리소스(여기에서는 `vue`, `App.vue`, `router`, `store`)들을 webpack이라는 번들러를 이용해 외부 라이브러리는 `chunk-vendors.js`파일로 묶고 직접 작성한 코드는 `app.js`파일에 묶어 배포한다.  이를 확인하기 위해서 public 폴더 아래에 있는 index.html 파일과 실행된 앱을 브라우저에서 소스 보기를 하면 다음과 같은 차이를 볼 수 있다.

     ```html
     <!DOCTYPE html>
     <html lang="">
       <head>
              ...
       	  <script defer src="/js/chunk-vendors.js">
           </script><script defer src="/js/app.js"></script>
       </head>
       <body>
          <div id="app"></div>
          <!-- built files will be auto injected -->
       </body>
     </html>
     ```

     

3. 첫 웹페이지의 구성 - App 컴포넌트

   - `VUE`프레임워크의 기본 구조는 `<template>`, `<script>` 그리고 `<style>`로 구성되는데 tempalte는 웹 페이지에서 보여질 화면을 컴포넌트로 구성하는 것이다.

   - 여기에서는 `<nav>`태그를 이용해서 메뉴가 구성되었고 그 안에는 `<router-link>`컴포넌트를 이용해서 표현한다.

   - `<router-view/>`컴포넌트를 이용해서 라우터에 의해 선택된 VUE 컴포넌트가 해당 위치에 표현된다. 이에 대한 정보는 `router/index.js`에 프로그램되어 있다.

     > App.vue

     ```vue
     <template>
         <nav>
             <router-link to="/">Home</router-link> |
             <router-link to="/about">About</router-link>
         </nav>
         <router-view/>
     </template>
     <style>
         ...
     </style>
     ```

4. SPA에서 화면 전환 제어 - router 컴포넌트(?) 동작

   - `router`라는 변수는 path, name 그리고 componet 속성을 가지고 있는데,

     - path: 는 `App` 컴포넌트의 to속성에 있는 이름과 동일하게 유지하여
     - name: 메뉴가 많을 경우, 식별할 수 있는
     - component: VUE 컴포넌트와 연결할 수 있는 뷰로 정적 또는 동적으로 설정할 수 있음. 이때, 동적 설정은 webpack에서 필요한 시점에 js파일을 network 상에서 로딩하도록 함

     > router/index.js

     ```javascript
     import { createRouter, createWebHistory } from 'vue-router'
     import HomeView from '../views/HomeView.vue'
     
     const routes = [
       {
         path: '/',
         name: 'home',
         component: HomeView
       },
       {
         path: '/about',
         name: 'about',
         component: () => 
            import(/* webpackChunkName: "about" */ '../views/AboutView.vue')
       }
     ]
     
     const router = createRouter({
       history: createWebHistory(process.env.BASE_URL),
       routes
     })
     
     export default router
     ```

     

     개발자 도구>Network>JS에서 확인해 보면

     ![image-20220922114000393](4시간만에완벽하게끝내는Vue입문-v3.assets/image-20220922114000393.png)

     * chunk-vendors.js는 개발에 필요한 자바스크립트
     * app.js는 빌드시에 모든 자바스크립트를
     * about.js는 클릭했을 경우 필요한 시점에 자바스크립트를 로딩

     > 여기에서 아래와 같이 옵션(', webpackPrefetch:true')을 변경하면 status는 200 Size는 prefetch cache 가 변경될 것을 알 수 있는데, 이는 main.js가 다운 받았을 경우 미리 받아 놨다가 나중에 About을 클릭했을 때는 캐시에서 가져와서 네트워크 부하를 줄일 수 있다.

     ```javascript
     component: () => import(/* webpackChunkName: "about", webpackPrefetch:true */ '../views/AboutView.vue')
     ```

     

     (캡처)

      ![image-20220922132643822](4시간만에완벽하게끝내는Vue입문-v3.assets/image-20220922132643822.png)



### Vue 컴포넌트

Vue 프로젝트는 다음 폴더 구조를 기본으로 의 기본 문법은

* components 폴더에는 재사용가능한 컴포넌트를
* views 폴더에는 화면 전체 하나 하나에 해당 하는 컴포넌트를 , 그리고 네이밍 룰로 `-View`를 접미사로 붙인다. (최신 V3.x에서 추가됨)  >>>  Page가 좋을 듯 함.



이해를 돕기 위해서 `views/1_databinding`폴더를 생성하고 나서

#### 데이터 바인딩

서버에서 가져온 데이터든, 사용자가 입력한 데이터이든 html과 바인딩하기 위해서는 data()함수에서 반드시 return 뒤에 리터럴 형식의 객체로 리턴해 주어야 하고 이를 사용하는 `<template>`에서는 데이터 타입에 따라 적절한 표기법(`string`의 경우 `{{return literal.variable}}`를)을 사용해야 한다.

* 단방향 바인딩: javascript >> html
* 양방향 바인잉: javascript >> html >> javascript



> emmet 활용하기
>
> - temp  >>  `<template> html.vue`를 선택, html 코드를 작성한다.
> - scrip   >> `<script> javascript.vue`를 선택, export default() {} 내에 함수 정의에는 `function`이라는 키워드를 사용하지 않는다.
> - styl     >> `<style> css-scoped.vue`를 선택, 해당 컴포넌트에만 스타일이 적용되며 전체에 적용되도록 하기 위해서는 `assets` 폴더에 정의한다.



##### StringView.vue



```vue
<template>
  <div>
    <h1>Hello {{userName}}</h1>
    <p></p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      userName: 'John Doe',
      message: '',
      arr: [],
      obj: {}
    }
  }
}
</script>

<style scoped>
.text-red {
  color: red;
}
</style>
```



이를 확인하기 위해서, 먼저 router에 해당 view를 추가해야 한다.



---

##### HtmlView.vue

innerText가 아닌 innerHtml를 사용하는 것

`VUE`에서 추가적으로 HTML엘리먼트 속성에 추가해서 사용하는 v-* 지시어(directive)가 있다. 만일 없었다면 `<div id="html"></div>`로 정의하고 document.queryS()를 사용해서 ...

> v- 디렉티브
>
> v-html="변수명"



> 소스
>
> ```vue
> <template>
>   <div>
>     <p>{{htmlString}}</p>
>     <div v-html="htmlString"></div>
>   </div>
> </template>
> <script>
> export default {
>   name: 'htmlView',
>   components: {},
>   data() {
>     return {
>       htmlString: '<p style="color:red;">빨간색 문자</p>'
>     }
>   }
> }
> </script>
> 
> ```
>
> 

##### InputView.vue



* 양방향 데이터 바인딩: `v-model`이라는 지시어 사용
* vue에서는 id 또는 name과 같은 HTML 엘리먼트 속성을 사용할 필요가 없다.
* 디버깅을 위해 `@event="user-defined-function();"`를 사용하고, 이를 export default { method: {}}에 정의함
* HTML에서 input 태그의 value속성은 무조건 String 형태임
* 하지만, VUE에서는 v-model.`number`라는 지시어를 이용해서 쉽게 숫자로 변환할 수 있다.



> 소스
>
> ```vue
> <template>
>   <div>
>     <input type="text" v-model="userId" />
>     <button @click="myFunction()">클릭</button>
>     <button @click="changeData()">변경</button>
>     <br />
>     <input type="text" v-model="num1" /> +
>     <input type="text" v-model="num2" /> =
>     <span>{{num1+num2}}</span>
>     <br />
>     <input type="text" v-model.number="num3" /> +
>     <input type="text" v-model.number="num4" /> =
>     <span>{{num3+num4}}</span>
>   </div>
> </template>
> <script>
> export default {
>   name: 'inputView',
>   components: {},
>   data() {
>     return {
>       userId: 'demian.choe',
>       num1: 0,
>       num2: 0,
>       num3: 0,
>       num4: 0
>     }
>   },
>   methods: {
>     myFunction() {
>       // this는 VUE 컴포넌트로 data()에 있는 것이 모두 등록해 놓은 상태
>       console.log(this.userId)
>     },
> 
>     changeData() {
>       this.userId = 'CNK'
>     }
>   }
> }
> </script>
> 
> ```



##### SelectView.vue



* 



##### CheckboxView.vue



* 원래 HTML에서는 체크박스를 하나의 그룹으로 묶기 위해서는 name속성에 동일하게 하면되었다
* VUE에서는 v-model속성에 동일한 변수를 사용하면 된다. 이때 변수는 배열로 정의이어야 한다.
* 특이한 점은 input 엘리먼트의 text 또는 select와 같은 경우 변경된 값이 value에 바인딩 되지만
* checkbox의 경우에는 checked라는 속성을 바꾸는 행위만을 하기에  `true`로 변경된 HTML 에리먼트의 `value` 값을 가져와서 변수와 바인딩하는 개념



> radio 와 동일
>
> 다만, model 변수만 문자열로 선언



##### AttributeView.vue



* HTML 속성들과 바인딩
* v-bind:[HTML element attribue] 지시어를 이용하면 단방향 바인딩, [v-bind]는 생략 가능함
* 





##### ListView.vue



* `v-for` 지시어를 가지고 배열의 item을 꺼내고 이를 사용하여 `속성 바인딩` 또는 `문자열 바인딩`을 이용

  (형식) 

  v-for="city in cities"

* 배열을 

  * select에 바인딩
  * table 엘리먼트에 바인딩



> 소스
>
> ```vue
> <template>
>   <div>
>     <div>
>       <select name="" id="">
>         <option :value="city.code" :key="city.code" v-for="city in cities">
>           {{ city.title }}
>         </option>
>       </select>
>     </div>
>     <div>
>       <table>
>         <thead>
>           <tr>
>             <th>제품번호</th>
>             <th>제품명</th>
>             <th>가격</th>
>             <th>주문수량</th>
>             <th>합계</th>
>           </tr>
>         </thead>
>         <tbody>
>           <tr :key="idx" v-for="(drink, idx) in drinkList">
>             <td>{{drink.drinkId}}</td>
>             <td>{{drink.drinkName}}</td>
>             <td>{{drink.price}}</td>
>             <td><input type="number" v-model="drink.qty" ></td>
>             <td>{{drink.price * drink.qty}}</td>
>           </tr>
>         </tbody>
>       </table>
>     </div>
>   </div>
> </template>
> <script>
> export default {
>   components: {},
>   data() {
>     return {
>       cities: [
>         { title: '서울', code: '02' },
>         { title: '대전', code: '42' },
>         { title: '세종', code: '044' }
>       ],
>       drinkList: [
>         {
>           drinkId: '1',
>           drinkName: '코카콜라',
>           price: 700,
>           qty: 1
>         },
>         {
>           drinkId: '2',
>           drinkName: '오렌지주스',
>           price: 1200,
>           qty: 1
>         },
>         {
>           drinkId: '3',
>           drinkName: '커피',
>           price: 500,
>           qty: 1
>         }
>       ]
>     }
>   }
> }
> </script>
> 
> ```
>
> 



##### ClassView.vue



- `속성 바인딩`에서  :class 바인딩에는 "{'class-name': true | false,  class-name': true | false,}" 형태로 사용된다.
- 리터럴 클래스 형태...



> (소스)
>
> ```vue
> 
> ```
>
> 





##### StyleView.vue



- `속성 바인딩`에서  :class 바인딩에는 "{'class-name': true | false,  class-name': true | false,}" 형태로 사용된다.
- class 속성명에 하이픈('-')을 사용했을 경우 변수에는 CamelCase를 사용한다.



> (소스)
>
> ```vue
> 
> ```
>
> 
