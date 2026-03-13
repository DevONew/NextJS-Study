# react-foundations

### 2장 UI 렌더링

사용자가 웹페이지를 방문하면 서버는 브라우저로 html 파일을 반환한다.

브라우저는 html을 읽고 dom을 구성한다.

**DOM - Document Object Model**

1. html 요소를 객체로 표현한다.
> html은 그냥 단순 텍스트 파일이다. 따라서 JS가 접근 할 수가 없다.
2. 객체기 때문에 UI와 JS를 연결해준다.
> 그래서 JS는 DOM을 조작할 수 있다. (dom조작, node(dom 트리의 단위)조작)
> 따라서 화면 조작이 가능하다.
3. 부모 자식 관계를 가진 트리 구조다.

JS는 DOM을 조작하여 UI의 특정 요소를 선택,추가, 업데이트 및 삭제를 할 수 있다.
JS 특정 대상을 지목할 수도 있고 스타일과 내용을 변경 할 수도 있다.

- 코드


    HTML

    ```html
    <div id="box">
      <p>안녕</p>
    </div>
    ```

    DOM / JS

    ```jsx
    // 브라우저가 만들어주는 객체 (개념적 표현)
    {
      tagName: "div",
      id: "box",
      children: [
        {
          tagName: "p",
          textContent: "안녕",
          children: [],
          parentNode: /* 위의 div */,
          // ...
        }
      ],
      parentNode: /* body */,
      // ...
    }

    // 속성 읽기
    document.getElementById("box").id        // "box"

    // 속성 바꾸기
    document.getElementById("box").style.color = "red"

    // 메서드 호출
    document.getElementById("box").remove()

    // 자식 탐색
    document.getElementById("box").children  // [<p>]
    ```


### 3장 자바스크립트를 이용한 UI 업데이트

명령적 프로그래밍(Imperative)과 선언적 프로그래밍(declarative)

명령적 프로그래밍은 하나하나 단계적으로 장황하게 얘기함.

선언적 프로그래밍은 dom이 알아서 해석하도록 함.
> React는 선언적 프로그래밍

### 4장 React 시작하기

ReactDOM.createRoot()는 특정 DOM 요소를 대상으로 지정하고 React 컴포넌트를 표시할 root를 생성하는 메소드
root.render()은 리액트 코드를 DOM에 렌더링 하는 메소드

**JSX**
js를 html처럼 사용하는 문법.

**JSX의 규칙**
1. 여러요소를 반환하고 싶다면 **반드시 모든 요소를 부모 태그**로 묶어야함.

```jsx
<div>
  <h1>Hedy Lamarr's Todos</h1>
  <img
    src="https://i.imgur.com/yXOvdOSs.jpg"
    alt="Hedy Lamarr"
    class="photo"
  >
  <ul>
    ...
  </ul>
</div>
```

만약 불필요한 태그를 늘리고 싶지 않다면 **<> </> (프레그먼트)**로 감싸도 된다.
프레그먼트는 dom에서 흔적이 없다.
2. **태그는 무조건 닫아야한다.** <img /> 가능!
3. 무조건 **카멜 케이스다** -, . 같은 이름 금지
**번외) class는 className이다**

**Babel**
기본적으로 브라우저는 jsx를 이해하지 못하므로 JS 컴파일러가 필요하다.

### 5장 컴포넌트를 사용하여 UI 구축하기

React 애플리케이션 개발을 시작할려면 숙지해야 할 React의 핵심 개념이 있다.
1. component
2. props
3. states

**Components**
UI를 구성하는 요소들
리액트에서 컴포넌트는 함수형이다(클래스형도 있긴함)
1. 컴포넌트는 JS 및 HTML과 구분하기 위해 반드시 대문자로 시작하여야 한다 ex) function Header() {}
2. 컴포넌트는 일반 HTML 태그와 마찬가지로 꺽쇠괄호(<>)를 사용하여야한다.
3. nesting 컴포넌트 - html처럼 컴포넌트 안에 컴포넌트를 중첩할 수 있다. 단 정의는 밖에서 사용은 안에서

### 6장 Props를 사용하여 데이터 표시하기

**props**
다른 텍스트를 전달하고 싶거나 외부에서 데이터를 받는거처럼 정보를 알 수 없을때
html 요소에는 속성이 있어 동작을 변경하는 정보를 전달하는데(src의 img, a태그에 href 같이)
컴포넌트에 정보를 전달하는 것을 props라 할 수 있다.

참고로 리액트는 트리를 따라 아래로 흐른다. 이를 단방향 데이터라고 한다.

props는 객체기 때문에 객체구조 할당을 사용하여 명시적으로 이름을 지정할 수 있다.
그대로 넣는다면 문자열로 인식 하기 때문에 {}에 넣어서 알려준다. (JSX 문법)
1. 점 표기법으로 객체 속성 표현 `<h1>{props.title}</h1>`
2. 템플릿 리터럴 `<h1>{Cool ${title}}</h1>`
3. 함수 반환 값: `<h1>{createTitle(title)} </h1>`
4. 삼항 연산자 `<h1>{title ? title : 'Default Title'}</h1>`

**key prop**
React는 배열의 항목을 고유하게 식별하는 데 필요한 속성
DOM에서 어떤 요소를 업데이트해야 하는지 알 수 있음.

### 7장 State를 이용한 인터렉션 추가

**State와 이벤트 핸들러**

버튼을 클릭했을 때 특정 동작을 수행하도록 하고 싶다면 OnClick 이벤트를 넣음.
반드시 이벤트명은 camelCase로 지어야함.

이벤트 종류
- onClick
- onChange
- onSubmit
- onMouseOver(마우스 감지)
- onKeyDown (키 누름)
자주 쓰는 이벤트 객체 속성
e.target.value (입력값)
e.target
e.preventDefault() (기본동작 막기)
e.stopPropagation() (이벤트 버블링 막기)

함수에서 handleClick은 전달 handleClick()은 즉시실행 <button onClick={handleClick}>

**Hook**

useState
상태관리할 때 쓰는 훅
1. 배열을 반환, 배열 구조 분해를 사용하여 컴포넌트 내에서 해당 배열안에 값에 접근 사용 가능
2. 배열의 첫 항목은 value , 두번째 항목은 update 해당 값을 호출 하는 함수(set+name)
3. useState()에 초기값을 넣을수 있다. 안넣으면 undefined(0,"",[],true,null)
ex) const [likes, setLikes] = useState(0);

props vs state
props는 부모가 자식에게 전달하는 것 state은 컴포넌트 내에서 이루어지는 것 업데이트 로직도 컴포넌트 안에 있어야한다.

### 10장 서버 및 클라이언트 컴포넌트

서버 컴포넌트 : 서버에서 실행 (DB 조회, 데이터 가져오기)
클라이언트 컴포넌트 : 브라우저에서 실행 (클릭, 상태관리)

기본적으로 next.js는 서버 컴포넌트기 때문에 useState같은 브라우저단에서 움직이는 훅을 가진 컴포넌트 는 위에 **'use client'**가 필수

**RSC(react server component)**
서버에서만 실행되는 컴포넌트.
DB에 직접 접근하거나 데이터 페칭을 서버에서 처리할 수 있음
해당 JS 코드가 브라우저에 전송되지 않아서 번들 사이즈를 줄이고 성능을 향상
