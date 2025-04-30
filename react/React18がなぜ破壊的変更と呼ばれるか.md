# 概要
* いくつかの機能が追加されたりしたためかなり転換期となりうるアップグレードであった

---
以下、具体的なアップグレード内容
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


