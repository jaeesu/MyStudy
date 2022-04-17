### redux
react는 각각의 컴포넌트마다 자신의 state를 가질 수 있다.<br>
부모 컴포넌트로 부터 state를 props로 상속 받을 수도 있고
이를 lifting-state-up을 통해 부모의 state를 변경할 수도 있다.<br>
redux를 이용하여 store에 변수를 저장하여 아무데서나 사용할 수 있다. 


### reducer란?

리액트(React) 상태(State) 생성자(Producer)<br>
reducer 는 현재 상태와 액션 객체를 파라미터로 받아와서 새로운 상태를 반환해주는 함수<br>
reducer가 반환하는 값이 곧 새롭게 변수에 부여될 

```
const [state, dispatch] = useReducer(reducer, initialState);
```

state : 앞으로 컴포넌트에서 사용 할 수 있는 상태를 가리킴<br>
dispatch : 액션을 발생시키는 함수<br>

리덕스(Redux)는 액션(Action)이 날라오면 리듀서(Reducer)가 스토어(Store)의 상태(State)르 변경시키는 방식으로 동작하므로 얼추 의미가 통한다고 생각한다.<br>

1. store 설치
2. reducers 작성
3. component 작성(react)
4. containers 작성(react-redux)