# 概要
* いくつかの機能が追加されたりしたためかなり転換期となりうるアップグレードであった

## 前提
* React17，18 のルートDOMノードの書き方が若干異なる
* このバージョンによる書き方を誤るとそのバージョン本来の機能が使用できなくなったりするので注意が必要
  * その旨がコンソール上で表示されていたりはする

【React17】
```javascript
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById("root")
);
```

【React18】
```javascript
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App";

const root = ReactDOM.createRoot(
  document.getElementById("root") as HTMLElement
);
root.render(
  // <React.StrictMode>
  <App />
  // </React.StrictMode>
);
```

---
# 自動バッチング 
https://ja.legacy.reactjs.org/blog/2022/03/08/react-18-upgrade-guide.html#automatic-batching
## React17
`バッチ処理 ... 一定期間または一定量のデータをまとめて一括で処理する方式`
* React17においては State 更新などのイベントハンドラを一括で処理し、レンダリングはイベントハンドラが処理した時点では行われない
* しかし、イベントハンドラ以外ではバッチ処理などの最適化がされていなかった

```javascript
// イベントハンドラ内
const [state, setState] = useState(0);

const onClickUpdateButton = () => {
  console.log(state1); //=> 0 
  setState1((state1) => state1 + 1);
  console.log(state1); //=> 0
};

// イベントハンドラ外
const [todos, setTodos] = useState<Todo[] | null>(null);
const [isFinishApi, setIsFinishApi] = useState(false);
const [state3, setState3] = useState<String>("");

const onClickExecuteApi = () => {
  // fetch自体はReactの機能ではないのでイベントハンドラ外といえる
  fetch("https://jsonplaceholder.typicode.com/todos")
    .then((res) => res.json())
    .then((data) => {
      console.log("APIのレスポンスを受け取った");
      setTodos(data);       //=> レンダリング発生(1)
      setIsFinishApi(true); //=> レンダリング発生(2)
      setState3("update");  //=> レンダリング発生(3)
    });
};
```

## React18
* React18ではイベントハンドラ外であってもレンダリングが何度も発火せずバッチ処理で最適化してくれている
  * バッチ処理での最適化を**自動で**行っており、開発者による実装は不要

```javascript
// イベントハンドラ外
const [todos, setTodos] = useState<Todo[] | null>(null);
const [isFinishApi, setIsFinishApi] = useState(false);
const [state3, setState3] = useState<String>("");

const onClickExecuteApi = () => {
  // fetch内であるがバッチ処理としてレンダリングは処理が終わった後に発火する
  fetch("https://jsonplaceholder.typicode.com/todos")
    .then((res) => res.json())
    .then((data) => {
      console.log("APIのレスポンスを受け取った");
      setTodos(data);       //=> -
      setIsFinishApi(true); //=> -
      setState3("update");  //=> -
    });
};
```

### 補足：`flush`
* このバッチングは自動で適用されるが、もし自動バッチングを適用したくない場合は `flushSync` という関数が用意されている
* この関数によって部分的に自動バッチング処理を外すことが可能となる
* 余程使われることは無い

---
# トランジション 
https://ja.legacy.reactjs.org/blog/2022/03/29/react-v18.html#new-feature-transitions
## React17


```javascript

```


## React18


```javascript

```









