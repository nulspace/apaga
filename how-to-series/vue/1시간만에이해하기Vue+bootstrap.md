### 한시간만에 끝내는 부트스트랩 입문



- 웹페이지를 빠르고 안정적으로 구축할 수 있게 해주는 도구로 HTML, CSS 그리고 Javascript 세트이다.
- 웹페이지를 만들때 필요한 웹 UI 컴포넌트를 제공
- 안정적이라는 것은 다양한 디바이스 종류와 운영체제에 따라 최적의 UI를 제공... 단, 규칙에 맞는 작업을 해야
- 반응형 웹 제공, 자주 사용하는 패턴을 제공하여 최고의 `웹디자인/퍼블리싱` 프레임워크에 등극
- getbootstrap.com 



> 클래스명의 일정한 패턴을 찾는 것이 중요할 듯 - defical
>
> - 컴포넌트명
> - 컴포넌트명-색상명
> - 컴포넌트명-사이즈명



#### 반응형 웹이 가장 중요함

##### 12-Grid 시스템

- col-`md-4`: 한 `row`의 각 컬럼이 12개의 크기에서 해당 비중만큼 col이 차지하는 체계로,  `md`는 medium 크기이고 `sm`은 small, `lg`는 large  그리고 `xl`는 extra large 크기이다. 만약 최소 크기로 표현할 수 없다고 판단되면 원래 div의 특성대로 한행을 채워준다.
- 세부 내역은 [Bootstrap/Layout/Containers](https://getbootstrap.com/docs/5.2/layout/containers/)를 및 [Bootstrap/Layout/Grid Options](https://getbootstrap.com/docs/5.2/layout/grid/#grid-options) 참고하라.
- 또는 `[Examples](https://getbootstrap.com/docs/5.2/examples/)` 사이트를 먼저 참고해서 배워라.
- 단독적인 컴포넌트를 하나씩 보는 것은 어려운 접근법이라고 생각한다.
- 복합적으로 사용할 수 있다.
  - class = "col"
  - class = "col-lg-3"
  - class = "col-lg-3 col-md-4 col-sm-6"

##### Components

웹페이지를 작성할 때, 빈번하게 사용하는 UI를 미리 컴포넌트로 만들어 놓아서 개발자는 그냥 가져다 사용만 하면 됨. 제공되는 컴포넌트들

- Alerts: 사용자에게 메시지를 전달하는 컴포넌트들
- 색상은 `primary, secodary, danger, waring, info,light, info`등의 색상명을 사용하고 있음
- Badge: 사용자에게 카운터를 전달하는 컴포넌트들
- Breadcrumb: 사용자에게 지나온 경로를 제공하기 위한 컴포넌트
- Button/Button group
- Card/Carousel : 보통 제품을 소개할 때
- Collapse: 컨텐츠를 보여주거나 보이지 않게 할 때, FAQ, 아코디언
- Dropdown
- 

##### Forms

- form-control
- form-*

##### Groups and ...

- input group: 두 개이상의 컴포넌트가 하나로 합쳐진 경우, 가장 쉽게 `캘런더`
- Jumbotron
- Media object
- Modal: backdro 유/무에 따라서, scroll 바 유/무
- Navs: 링크 ...
- Navbar: 메뉴 ... 작은 화면에서는 햄버거 메뉴로 ...
- Pagination: 
- Popovers: `?`도움말 또는 툴팁
- Progress
- Scrollspy: 스크롤 되는 것을 알려주도록 하는 컴포넌트
- spinners: 저장 중 ... 
- Toasts: 이메일이 도착했었요, 시스템에 문제가 있어요....
- Tooltips

##### Utilities

- Color

- Display: 컨텐츠가 보이거나 보이지 않도록 하기 위한 클래스. 특히 스마트 폰에서는 보이지 않도록 

  > `.d-sm-none .d-md-block` 스마트폰에서는 보이지 않고 중간 사이즈에서는 블럭사이즈로 ...

- 프린트할 경우에도 제어가 가능함

- Interactions

  - Text selection

- Text: 왼쪽, 오른쪽, 중간 정렬 등



##### Themes

- 예시를 참고해서 사용하라. (구매 5~6만원; 구글 검색 또는 wrapbootstrap.com 등)
