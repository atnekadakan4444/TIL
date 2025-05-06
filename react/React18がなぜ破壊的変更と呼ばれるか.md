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
* **React17には無い完全な新機能**

## 概要
* **ユーザーインターフェースの状態遷移（例えば、UIの切り替え、データの読み込み表示など）を、緊急度の高い更新と低い更新に分けて管理する**ための仕組み
* 例）検索したい条件のボタンを押下した際に、選択したボタンが強調表示のため色が塗られ、選択された条件の表示内容のみが表示される
  * React18以前）どれも同じタイミングで処理を開始していたため処理優先度のような仕様はなかった。その結果として、先の例においてボタン押下時の「ボタンの強調表示」「選択された条件の表示」が同時に処理されてしまい、比較的処理の軽いボタンの強調表示さえも遅れて処理される
  * React18より）緊急度の低い更新（例えば、検索結果のフィルタリング、遅延ローディングなど）をトランジションとしてマークすることで、Reactは緊急度の高い更新（例えば、入力フィールドへの即時反映）を優先的に処理します。これにより、UIが途中でブロックされるのを防ぎ、より滑らかな操作感を実現している。

## React18
### `startTransaction()`
* `startTransaction()` で優先度の高くない処理を指定する

```javascript
import { startTransition } from 'react';

// Urgent: Show what was typed
setInputValue(input);

// Mark any state updates inside as transitions
startTransition(() => {
  // Transition: Show the results
  setSearchQuery(input);
});
```

### `startTransaction()`
* `startTransaction()` のstate管理を可能にした上位互換の関数
  * `isPending` という State で `startTransition()` に該当する処理の処理状況を管理している

```javascript
import React, { useState, useTransition } from 'react';

function Example() {
  const [text, setText] = useState('初期テキスト');
  const [isPending, startTransition] = useTransition();

  const handleClick = () => {
    // 緊急度の低い更新としてマーク
    startTransition(() => {
      setText('新しいテキスト (少し遅れて表示)');
    });
  };

  return (
    <div>
      <p>テキスト: {text}</p>
      <button onClick={handleClick} disabled={isPending}>
        テキストを変更
      </button>
      {isPending && <p>更新中...</p>}
    </div>
  );
}

export default Example;
```

### `useDeferredValue()`
* useTransition との違いは？
  * `useTransition` : 特定の状態更新（setState の呼び出し）をトランジションとしてマークし、その更新全体を緊急度の低いものとして扱います。トランジションの状態 (isPending) を監視できます
  * `useDeferredValue` : 値そのものを遅延させます。ある値が頻繁に更新される場合に、その値に依存する処理を遅らせるために使います。トランジションの状態を直接知ることはできません






