# react-hooks

## useState

It is the most important and often used hook. The purpose of this hook to handle reactive data, any data that changes in the application is called state, when any of the data changes, React re-renders the UI.

```javascript
const [count, setCount] = React.useState(0);
```

## useEffect

It allows us to implement all of the lifecycle hooks from within a single function API.

```javascript
// this will run when the component mounts and anytime the stateful data changes
React.useEffect(() => {
  alert("Hey, Nads here!");
});

// this will run, when the component is first initialized
React.useEffect(() => {
  alert("Hey, Nads here!");
}, []);

// this will run only when count state changes
React.useEffect(() => {
  fetch("nads").then(() => setLoaded(true));
}, [count]);

// this will run when the component is destroyed or before the component is removed from UI.
React.useEffect(() => {
  alert("Hey, Nads here");

  return () => alert("Goodbye Component");
});
```

## useContext

This hook allows us to work with React's Context API, which itself a mechanism to allow us to share data within it's component tree without passing through props. It basically removes prop-drilling

```javascript
const ans = {
  right: "✅",
  wrong: "❌",
};

const AnsContext = createContext(ans);

function Exam(props) {
  return (
    // Any child component inside this component can access the value which is sent.
    <AnsContext.Provider value={ans.right}>
      <RightAns />
    </AnsContext.Provider>
  );
}

function RightAns() {
  // it consumes value from the nearest parent provider.
  const ans = React.useContext(AnsContext);
  return <p>{ans}</p>;
  // previously we were required to wrap up inside the AnsContext.Consumer
  // but this useContext hook, get rids that.
}
```

## useRef

This hook allows us to create a mutable object. It is used, when the value keeps changes like in the case of useState hook, but the difference is, it doesn't trigger a re-render when the value changes.

The common use case of this, is to grab HTML elements from the DOM.

```javascript
function App() {
  const myBtn = React.useRef(null);
  const handleBtn = () => myBtn.current.click();
  return <button ref={myBtn} onChange={handleBtn}></button>;
}
```

## useReducer

It does very similiar to setState, It's a different way to manage state using Redux Pattern. Instead of updating the state directly, we dispatch actions, that go to a reducer function, and this function figure out, how to compute the next state.

```javascript
function reducer(state, dispatch) {
  switch (action.type) {
    case "increment":
      return state + 1;
    case "decrement":
      return state - 1;
    default:
      throw new Error();
  }
}

function useReducer() {
  // state is the state we want to show in the UI.
  const [state, dispatch] = React.useReducer(reducer, 0);

  return (
    <>
      Count : {state}
      <button onClick={() => dispatch({ type: "decrement" })}>-</button>
      <button onClick={() => dispatch({ type: "increment" })}>+</button>
    </>
  );
}
```

## useMemo

This hook will help you to optimize computational cost or improve performance. It mostly used when we're needed to make expensive calculations.

```javascript
function useMemo() {
  const [count, setCount] = React.useState(60);

  const expensiveCount = useMemo(() => {
    return count ** 2;
  }, [count]); // recompute when count changes.
}
```

Works great for memoizing returned values, but in other CSSNamespaceRule, we want to memoize the whole function, in that case we can use this hook

## useCallback

The React useCallback Hook returns a memoized callback function.
The useCallback Hook only runs when one of its dependencies update.
This can improve performance.

```javascript
///////////////////////////
// index.js
///////////////////////////
import { useState, useCallback } from "react";
import ReactDOM from "react-dom/client";
import Todos from "./Todos";

const App = () => {
  const [count, setCount] = useState(0);
  const [todos, setTodos] = useState([]);

  const increment = () => {
    setCount((c) => c + 1);
  };
  const addTodo = useCallback(() => {
    setTodos((t) => [...t, "New Todo"]);
  }, [todos]);

  return (
    <>
      <Todos todos={todos} addTodo={addTodo} />
      <hr />
      <div>
        Count: {count}
        <button onClick={increment}>+</button>
      </div>
    </>
  );
};

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<App />);

///////////////////////////
// Todos.js
///////////////////////////
import { memo } from "react";

const Todos = ({ todos, addTodo }) => {
  console.log("child render");
  return (
    <>
      <h2>My Todos</h2>
      {todos.map((todo, index) => {
        return <p key={index}>{todo}</p>;
      })}
      <button onClick={addTodo}>Add Todo</button>
    </>
  );
};

export default memo(Todos);
```

## useImperativeHandle

This hook is use to modify the exposed ref and it is rarely used.

```javascript
function useImperativeHandleDemo(props, ref) {
  const myBtn = useRef(null);

  React.useImperativeHandle(ref, () => ({
    click: () => {
      console.log("clicking button!");
      myBtn.current.click();
    },
  }));
}
```

## useLayoutEffect

It works same as useEffect hook with one difference, the callback will run after rendering the component but before the actual updates have been painted to the screen.

⚠️ : Blocks visual updates until your callback is finished.

```javascript
function useLayoutEffectDemo() {
  const myBtn = React.useRef(null);

  React.useLayoutEffect(() => {
    const rect = myBtn.current.getBoundingClientRect();
    // scroll position before the dom is visually updated
    console.log(rect.height);
  });
}
```

## useDebugValue

This hook doesn't make much sense, but it allows us to define our own custom labels in React Dev Tools, which are useful for debugging. Suppose we have n number of components which uses the same logic, then we can separately define our own function and that can be used in other components, but the key thing here is we can debug things

```javascript
function useDisplayName() {
  const [displayName, setDisplayName] = React.useState();

  React.useEffect(() => {
    const data = fetchFromDatabase(props.userId);
    setDisplayName(data.displayName);
  }, []);

  React.useDebugValue(displayName ?? "loading...");
  return displayName;
}
```

The return value can be used in other components or else where in the application like this 👇🏽

```javascript
function App() {
  const displayName = useDisplayName();

  return <button>{displayName}</button>;
}
```

# References

1. https://dev.to/abhisheknaiidu/10-react-hooks-explained-3ino
