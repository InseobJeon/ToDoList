# 08/29, making todo list with Redux and React

오늘은 `Redux` 를 배운 것들을 활용하여, Todo List 를 만드는 날이다. 사실, Redux 의 공식문서의 [Usage With React](https://redux.js.org/basics/usage-with-react) 부분을 보고 마저 차근차근 응용해 보는 단계이지만, 최대한 개념을 이해했는지를, 그리고 사용할 수 있는지를 Redux 로 상태관리를 하고, React 에 기반한 To Do List 를 만들어보면서 공부하는 단계이기도 하여서 Redux 문서를 읽고 공부하는 부분과는 분리하게 되었다. 

---

# Usage With `React`

여태까지는 Redux 의 개념을 배우기만 했을 뿐, React 와 같이 사용하는 방법을 배우진 못했다. 이번 시간부터는 Redux 를 React와 함께 사용해보도록 하자. Redux 는 React 말고도 Angular, Vue, 그리고 Vanilla JS 와 같이 사용할 수도 있다...만, `React` 나 `Deku` 와 쿵짝이 "특히" 잘 맞는 것 또한 사실이다. 왜냐면, 이 두 개는 `function of state` 를 활용하여  UI 를 그리기 때문이다. `Redux` 도 이와 비슷하게 (순수)함수 + `state` 의 조합으로 상태관리를 하기에, 저 두 개와 특별히 궁합이 더 잘 맞을 수밖에 없다. 

# Installing React Redux

React Binding 이 기본적으로 `Redux`에 포함되어있지 않다. 앱을 처음 만들 때는 `npx create-react-app my-app --template redux` 를 통해,  이미 적용된 앱에는 `npm install react-redux` 을 통하여 설치해준다. 나같은 경우 밑바닥부터 새로 만들기 위해  `npx create-react-app my-app --template redux` 명령어를 통해 세팅을 했다. 

# Presentational and Container Components

- [관련 내용에 대한 추가 정리 in Nested Concepts](https://www.notion.so/08-29-making-todo-list-with-Redux-and-React-f1b9f41d846944eeb61fe479f1a4986b#aebaaab7c73f463fb33bf00f917d3330)

지금 사용하는 `React Bindings for Redux` 는 Presentational Component(state 와는 크게 관련이 없이 그저 그려내기만 하는 component) 와 Container Component(state 에 기반하여 변경되는 값에 대한 handling 이 들어가는 component) 를 구분할 것이다(separate presentational component  from container component) 이렇게 component 들을 용도에 따라 구분하게 된다면 코드의 재사용성을 높이고, 가독성 또한 높일 수 있다. 

우리가 작성하는 대부분의 component 들은 presentational 이다. 그러나 우리는 Redux store 와 연동하기 위해 container component 들도 어느정도는 만들어야 한다. 그렇다고 해서 또 지금 하는 말이 container component 가 우리의 component tree 최상단에 "무조건" 있어야 한다, 이런 건 아니다. 만약에 엄청나게 nested 된 구조에, 셀 수 없는 callback 호출이 일어나는 component tree 가 있다면, container component 하나를 더 넣어주는 것도 필요하다. 

container component 를 `store.subscribe()` 메서드를 통해 만들어 줄 순 있다. 그러나 Redux 공식문서에서는 추천하진 않는다고 한다. 왜냐면, Redux 단에서 매우매우 많은, 하나하나 수작업으로 해 주기는 어려운 수준의 성능 최적화가 이뤄지기 때문이다. 그렇기 때문에 container component 를 직접 수작업으로 만들어주기보다는, React-Redux 에서 제공하는 `connect()` 메서드를 이용하는 걸 권장한다고 한다. 

# Designing Component Hierarchy

우리가 Redux 에서 Reducer 를 만들어 줄 때 Hierarchy 를 고려해서 Reducer 를 작성했던 것처럼, 이제 UI 의 Hierarchy 를 잡아줄 때이다. 이 과정은 Redux "에만" 국한된 것이 아니다. React 문서의 [Thinking in React](https://ko.reactjs.org/docs/thinking-in-react.html) 는 이런 UI 상의 Hierarchy 를 잡아주는 과정에 대해서 아주 잘 설명해 주고 있다. 이해가 안 간다면, 저 부분을 읽고 스스로 작성한 [12 Concepts of React 의 이 부분](https://www.notion.so/12-Concepts-of-React-cbc7c903872646dab118a90d662b6a63#c6818757b8064edf9a09775946697207)을 다시 한 번 읽고올 수 있도록 하자.

우리가 앞으로 만들 건 간단하다. To do 목록들을 보여줄 것이고, 눌렀을 때는 체크 표시(crossed out)가 되면서 완료된 항목이라는 걸 보여줄 것이다. 그리고 사용자가 새로운 todo 항목을 만들 수 있는 영역도 만들어 주어야 하고, toggle 을 만들어서 "모두 보여주기", "완료된 항목만 보여주기", "미완료 항목들만 보여주기" 와 같이 todo 목록들에 filter 를 걸어줘야 할 것이다. 

## Designing Presentational Components

데이터(state) 와는 관계 없는, 보여주기만 하는 component 들을 구상해보자. 공식문서를 보기 전 내가 만든 presentational component 들은 다음과 같다

- `Todos` : todo 들을 담는 list, `Array` 형태
    - `Todo` : `{ id, text, isCleared }` 의 세 가지 property 로 구성
        - `id` : ToDo 항목에 부여되는 고유한 id
        - `text` : ToDo 항목의 내용
        - `isCleared` : 완료 여부

공식문서에서는 이런 식으로 구상을 하고있다. 내 구성과 차이점이 있다면 `onTodoClick` 과 같은 메서드까지도 다 구상해놓았다는 점이다. 

- **`TodoList`** is a list showing visible todos.
    - `todos: Array` is an array of todo items with `{ id, text, completed }` shape.
    - `onTodoClick(id: number)` is a callback to invoke when a todo is clicked.
- **`Todo`** is a single todo item.
    - `text: string` is the text to show.
    - `completed: boolean` is whether the todo should appear crossed out.
    - `onClick()` is a callback to invoke when the todo is clicked.
- **`Link`** is a link with a callback.
    - `onClick()` is a callback to invoke when the link is clicked.
- **`Footer`** is where we let the user change currently visible todos

이 컴포넌트들은 "보여주는 데" 초점을 맞추었다. 그러나 이 컴포넌트들에서는, "어디로부터" 정보가 오는지, 그리고 "어떻게" 그 정보를 바꿀 수 있는지는 알지 못 한다. 그냥 주는 값대로 화면에 그려줄 뿐이다. 이 Todo App에서 Redux 가 아닌 다른 걸 사용할 때도, 해당 컴포넌트들은 똑같은 기능을 할 것이다. 이유는 그저 Presentational Component, 그냥 "그려주기만 하는" 컴포넌트들이기 때문이다. 

## Designing Container Component

이제 Redux 와 연결하기 위해, "상태를 담는(container)" 컴포넌트들을 만들 차례이다. 예를 들자면, 우리가 방금 위에서 만든 Presentational Component 인 **`TodoList`** 는, **`VisibleTodoList`** 와 같이 Redux store 에 착 달라붙어 현재 visibility filter 의 state 를 받아올 수 있는 container container 가 필요하다. 

- `subscribe`, 직역하면 구독이지만 store 에 "구독" 을 한다는 표현은 적절치 못 하다고 생각해서 착 달라붙는 - 받아오는 과 같은 식으로 이해하고 그렇게 작성했다.

그리고 visibility filter 의 상태를 바꾸기 위해서는 **`FilterLink`** 라는,  click 을 했을 때 적절한 action 을 날려주는 `Link` 를 render 해주는 container component 가 필요하다. 

이번에도 공식문서에서 구상한 것들을 참고하자면, 아래와 같다. 

- **`VisibleTodoList`** filters the todos according to the current visibility filter and renders a `TodoList`.
- **`FilterLink`** gets the current visibility filter and renders a `Link`.
    - `filter: string` is the visibility filter it represents.

## Designing Other Component

Container component 도 아니고, Presentational Component 도 아닌 무언가가 가끔 존재한다. function 과 form 이 따로 가지 않고 같이 가야하는, 우리로 치면 사용자가 Todo 를 입력하는 걸 받고, 그걸 return 해줄 수 있는 component 같은 걸 의미한다. 

공식문서에서는 이를 `AddTodo` 라는, presentational 도 아니고 container 도 아닌 "other" 로 따로 분류했다

- `AddTodo` is an input field with an “Add” button

기술적으로 우리는 container 인지, 혹은 presentational 인지를 분류해 왔으나 이렇게 작은 건 뭐 괜찮다. 만약에 이 AddTodo 라는 component 의 스케일이 커진다면, 그 때가 되어서 또 나눠도 늦지 않을 듯 하다. 

# Implementing Components

이제 우리가 구상한 컴포넌트들을 한 번 작성해보자. 순서는 Presentational, 그리고 Container. Thinking in React 에서 권장한 순서대로 진행해 보자.

## Implementing Presentational Component

아직은 Redux 를 사용하지 않아도 된다. Redux 가 주는 값을 받을 때 그냥 그 값을 잘 받아 화면에 그려주기만 하면 되는 컴포넌트들을 먼저 구성해보자. local state 도, life cycle method 도 고려하지 말자. 

물론, 그렇다고 해서 이런 Presentational Component 가 무조건 function component  여야 할 필요는 없다. 그렇지만 state, life cycle method 를 고려하지 않고 작성해도 되기 때문에 이왕이면 더욱 표현이 간단한 function component 로 작성하자는 이야기이다. 

그러니 즉슨, 나중에 local state 나 life cycle method 나, 혹은 성능의 최적화를 고려할 때, 그 때는 class component 로 바꿔도 상관 없다. 

`components/Todo.js`

```jsx
import React from 'react'
import PropTypes from 'prop-types'

const Todo = ({ onClick, completed, text }) => (
  <li
    onClick={onClick}
    style={{
      textDecoration: completed ? 'line-through' : 'none'
    }}
  >
    {text}
  </li>
)

Todo.propTypes = {
  onClick: PropTypes.func.isRequired,
  completed: PropTypes.bool.isRequired,
  text: PropTypes.string.isRequired
}

export default Todo
```

`components/TodoList.js`

```jsx
import React from 'react'
import PropTypes from 'prop-types'
import Todo from './Todo'

const TodoList = ({ todos, onTodoClick }) => (
  <ul>
    {todos.map((todo, index) => (
      <Todo key={todo.id} {...todo} onClick={() => onTodoClick(todo.id)} />
    ))}
  </ul>
)

TodoList.propTypes = {
  todos: PropTypes.arrayOf(
    PropTypes.shape({
      id: PropTypes.number.isRequired,
      completed: PropTypes.bool.isRequired,
      text: PropTypes.string.isRequired
    }).isRequired
  ).isRequired,
  onTodoClick: PropTypes.func.isRequired
}

export default TodoList
```

`components/Link.js`

```jsx
import React from 'react'
import PropTypes from 'prop-types'

const Link = ({ active, children, onClick }) => (
  <button
    onClick={onClick}
    disabled={active}
    style={{
      marginLeft: '4px'
    }}
  >
    {children}
  </button>
)

Link.propTypes = {
  active: PropTypes.bool.isRequired,
  children: PropTypes.node.isRequired,
  onClick: PropTypes.func.isRequired
}

export default Link
```

`components/Footer.js`

```jsx
import React from 'react'
import FilterLink from '../containers/FilterLink'
import { VisibilityFilters } from '../actions'

const Footer = () => {
    <p>
        Show: <FilterLink filter={VisibilityFilters.SHOW_ALL}>All</FilterLink>
        {', '}
        <FilterLink filter={VisibilityFilters.SHOW_ACTIVE}>Active</FilterLink>
        {', '}
        <FilterLink filter={VisibilityFilters.SHOW_COMPLETED}>Completed</FilterLink>
    </p>
}

export default Footer
```

---

# Nested Concepts

## React Binding

> The recommended way to start new apps with React Redux is by using the official Redux+JS template for Create React App, which takes advantage of Redux Toolkit.

참조한 글 : 

[reduxjs/react-redux](https://github.com/reduxjs/react-redux#using-create-react-app)

React 를 개발할 때 Redux toolikit 을 백분 활용할 수 있게 해주는 방법이다. React 와 Redux 의 공식적인 최적화 된 조합을 사용하기 위해서 사용하는 방법이랄까? 

## Presentational Component and Container Component

참조한 글 : 

[[번역] 프레젠테이션 컴포넌트와 컨테이너 컴포넌트](https://blueshw.github.io/2017/06/26/presentaional-component-container-component/)

Redux 문서에 나오는 `Presentation Component` 라는 개념이 이해가 가지 않아 해당 키워드로 구글링을 해 보니, [어떤 사람이 Presentational Component 와 Container Component 에 대해 작성한 글](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)을 번역한 글이 나와 그 글을 참조하였다. 일단 생소한 개념이라 한 번 정독해보았다. 

### Presentational Component

- 어떻게 보여지는지와 관련있다.
- 프레젠테이션 컴포넌트와 컨테이너 컴포넌트가 모두 그 안에 들어가 있을것(**)이고, 일부 DOM 마크업과 스타일도 가지고 있다.
- 종종 this.props.children 을 통해서 노출된다.
- Flux 액션이나 stores 등과 같은 앱의 나머지 부분들에 의존적이지 않다.
- 데이터를 가져오거나 변경하는 방법에 대해서 관여할 필요가 없다.
- props 를 통해 배타적으로 callback 함수와 데이터를 받는다.
- 상태를 거의 가지고 있지 않다(만약 상태를 가지고 있다면, 데이터에 관한 것이 아닌 UI 상태에 관한 것이다).
- 만약 상태, 생명주기, hooks, 또는 퍼포먼스 최적화가 필요없다면, [유틸함수](https://facebook.github.io/react/blog/2015/10/07/react-v0.14.html#stateless-functional-components)로서 쓰여질것이다.
- 예를들면 페이지, 사이드바, 스토리, 유저정보, 리스트 등이 있다.

### Container Component

- 어떻게 동작하는지와 관련있다.
- 프레젠테이션 컴포넌트와 마찬가지로 프레젠테이션 컴포넌트와 컨테이너 컴포넌트 모두 가지고 있지만 감싼 divs 를 제외하고는 DOM 마크업을 가지고 있지 않는다. 스타일 역시 가지고 있지 않는다.
- 데이터와 기능(행동)을 프레젠테이션 컴포넌트와 다른 컴포넌트에 제공한다.
- Flux(or Redux) 액션을 호출하고, 프레젠테이션 컴포넌트에 콜백함수로써 제공한다.
- 데이터 소스 역할을 하기 때문에 상태가 자주 변경된다.
- 직접 만드는것 보단 대게 React Redux 의 connect() 함수, Relay 의 createContainer() 함수, Flux Utils 의 Container.create()와 같은 [Higher Order Components](https://medium.com/@dan_abramov/mixins-are-dead-long-live-higher-order-components-94a0d2f9e750)를 이용해서 만들어진다.
- 예를들면 유저페이지, 팔로워 사이드바, 스토리 컨테이너, 팔로우한 유저 리스트 등이 있다.

핵심을 적어보자면, **state 가 필요한 것과 state 가 필요하지 않은 것**에 대한 구분이겠다. 기술적이라기보다는 용도에 따른 차이이며, 밑에서도 "용도에 따른 차이" 라고 이야기한다.

Presentation Component, 말 그대로 "보여주는" 컴포넌트는 state 를 저장하는 stores(flux 나 redux 같은)에 의존적이지 않고 props 를 통해서 callback 함수와 데이터를 받으며 상태를 거의 가지고 있지 않다. 

반면 Container Component, 말 그대로 "담고 있는" 컴포넌트는 state 를 저장하는 store 와 관련되어 action 을 호출하고 데이터(state)를 Presentation Component 에게 callback 형식으로 제공하는 역할을 한다. 

 

저자는 이 두개를 명확히 구분하기 위해서 component 들을 구분한 뒤 다른 폴더에 생성한다고 한다. 이런 식으로 component 들을 용도에 따라 구분해서 얻을 수 있는 이점들은 다음과 같다고 한다

### Benefits for seperating them

- 이 방법으로 컴포넌트를 작성하면 당신의 앱(기능)과 UI 에 대한 구분을 이해하기가 더 수월하다.
- 재사용성이 더 뛰어나다. 완전히 서로 다른 상태값과 함께 같은 프레젠테이션 컴포넌트를 사용할 수 있고, 재사용 될 수 있는 별도의 컨테이너 컴포넌트로 변경할 수 있다.
- 프레젠테이션 컴포넌트는 말하자면 앱의 팔레트와 같다. 앱의 싱글페이지 위에서 앱의 로직을 건드리지 않고 디자이너에게 모든 변화를 조정하게 할 수 있다.
- 이것은 사이드바, 페이징, 컨텍스트메뉴와 같은 레이아웃 컴포넌트를 추출하도록 할것이고, 이것은 동일한 마크업이나 몇몇의 컨테이너 레이아웃을 반복해서 작성하는 대신 `this.props.children` 을 통해서 구현될 수 있다.

확실히 납득이 간다. 만약 state 를 관리하는 코드들과 state 가 딱히 필요 없는 코드들을 구분해서 관리하게 된다면 유지보수를 할 때 그렇게 머리가 아플 일도 없을 것이다. 저자는 "언제 Container Component 를 도입해야 하는가?" 에 대해서도 조언을 해 주고 있다. 

> 우선 앱을 만들때 Presentational Component 를 먼저 만드세요. 그러면 너무 많은 props 를 중간 Component로 보내야 한다는 것을 깨닫게 될것입니다. 전달받은 props 를 사용하지 않고 아래로 전달하기만 하는 Component나 자식 Component가 더 많은 데이터를 필요로 할때 모든 중간 Component를 재구성해야하는 Component들이 있다는 것을 알게 될것입니다. 바로 이 때 Container Component를 도입해야합니다. 데이터나 아무 상관없는 중간 컴포넌트에 대해 걱정이 없는 leaf Component의 행위가 담긴 props 를 얻을 수 있는 방법이 될 것입니다.

일단은 Presentational 위주로 애플리케이션을 구성하다가, props drilling 에서 "슬슬 골치가 아파지는데..."
 싶을 때, "이 component 는 단지 props 를 내려주기 위해 중간에 존재하는 component 일 뿐인데, 그냥 state 를 도입해서 코드 구조를 조금 더 간결하게 할 순 없을까?" 할 때 만들어 보라고 한다. 

## `Proptypes` in React

참고 문서 : 

[PropTypes와 함께 하는 타입 확인 - React](https://ko.reactjs.org/docs/typechecking-with-proptypes.html)

타입을 먼저 확인하는 React 가 기본제공하는 Type Check 라이브러리이다. 물론 TypeScript 를 사용하기도, Flow 를 사용해도 상관은 없다만, 이는 React 가 기본제공하는 라이브러리기 때문에 사용이 조금 더 용이할 수도 있다(실무에서는 어떻게 사용할 지 모르겠다만) 이렇게 타입을 체크하게 된다면, 내가 구현한 애플리케이션 스케일이 커졌을 때 오류를 잡기가 조금 더 쉬워진다. 

```jsx
//React 15.5 ver 부터 해당 라이브러리는 React.propTypes 가 아닌 prop-types 로 이동하였다
import PropTypes from 'prop-types';

class Greeting extends React.Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

//component 의 property 의 type 을 이런 식으로 확인해 볼 수 있다 
Greeting.propTypes = {
  name: PropTypes.string,
	//그리고 이렇게 requiredFunc 을 넣어서 적절한 타입의 prop 이 제공되지 않았을 때 경고를 띄울 수도 있다
	requiredFunc: PropTypes.func.isRequired
};

```

참고로, 저 `propTypes` 부분을 `requiredFunc` 이 아니라 `prop` 하나하나마다 요구할 수도 있다. 그렇게 요구한다면 밑에서 `requiredFunc` 를 통해 한 번에 나타내 주지 않아도 된다. 

```jsx
Greeting.propTypes = {
	//이렇게 PropTypes.specificType.isRequired 와 같이 표현해도 된다
  name: PropTypes.string.isRequired
};
```