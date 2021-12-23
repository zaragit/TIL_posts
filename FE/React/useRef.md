---
title: useRef
date: "2021. 12. 22"
summary: useRef는 언제 사용하는가?
---


### 공식문서 정의

```jsx
const refContainer = useRef(initialValue);
```

useRef는 .current 프로퍼티로 전달된 인자(initialValue)로 초기화된 변경 가능한 ref 객체를 반환합니다. 반환된 객체는 컴포넌트의 전 생애주기를 통해 유지될 것입니다.   
   
일반적인 유스케이스는 자식에게 명령적으로 접근하는 경우입니다.

```jsx
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` points to the mounted text input element
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```

본질적으로 useRef는 .current 프로퍼티에 변경 가능한 값을 담고 있는 "상자"와 같습니다.

[링크](https://ko.reactjs.org/docs/hooks-reference.html#useref)

해당 내용을 이해하기 위해서는 ref가 무엇인지 알아야 한다.

<br>

### ref 객체
ref는 render() 메서드에 의해서 생성된 DOM 노드나 React 엘리먼트에 **직접 접근**할 수 있게 해준다.   
일반적으로 React에서 DOM을 변경하기 위해서는 컴포넌트의 state를 수정해야 하지만 개발을 하다보면 state만으로 해결할 수 없는 직접적으로 DOM을 컨트롤 해야하는 경우가 있다.   

#### 대표적인 사례 (from [공식문서](https://ko.reactjs.org/docs/refs-and-the-dom.html#when-to-use-refs))
- 포커스, 텍스트 선택영역, 혹은 미디어의 재생을 관리할 때.
- 애니메이션을 직접적으로 실행시킬 때.
- 서드 파티 DOM 라이브러리를 React와 같이 사용할 때.

useRef() Hook은 이런 ref 객체를 생성해 준다.   
useRef로 생성된 ref 객체는 컴포넌트의 생명주기동안 유지되며 .current 값이 변경된다고 해도 render()메서드가 다시 호출되지 않는다.   

리액트에서 ref 객체를 생성하는 방법은 2가지가 있다. 
1. React.createRef() -> class형 컴포넌트 사용
2. useRef() -> 함수형 컴포넌트 사용

ref 객체는 React 시스템에서 벗어나 직접 JavaScript로 DOM을 제어해야 하는 경우나 컴포넌트 생명주기 내에서 사용하지만 값이 변경되어도 render()가 호출되면 안 되는 경우에만 사용하도록 한다.