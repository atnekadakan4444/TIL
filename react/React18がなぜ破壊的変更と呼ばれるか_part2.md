* React18 より強化された Suspense は重要な機能となるため別ページとした
* `Suspense` ... コンポーネントのレンダリングが完了するまでの間、フォールバックと呼ばれる代替画面を表示する機能を提供するもの

https://github.com/reactwg/react-18/discussions/37
> アプリケーションのユーザーはコンテンツをより早く表示し、はるかに迅速に操作を開始できるようになります。アプリの最も遅い部分が、高速な部分を引きずることはありません。

---
# サスペンスについて
https://ja.legacy.reactjs.org/blog/2022/03/29/react-v18.html#new-suspense-features
* React16.6 時点で既に実装されていた機能であった
* 必要でないリソースも併せて一括でロードしなければならず読込時間がかかっていた所を、そのコンポーネントが読み込まれた際にロードするといった必要な分だけ読み込めるようにしたのがこの機能であった

# 概要
* コンポーネントがロードを待機している間に代替のUIを表示する仕組み
  * 主に React.lazy() で遅延ロードされたコンポーネントやデータフェッチライブラリ（React Queryなど）の非同期処理の結果を待つ間にローディング表示などを実現するために使用される
    * 故に axios と併せて使われたりする
* これまでであれば Promise のメソッドチェーンで命令的に「どのようなタイミングでどのような代替 UI を表示するか」を制御についての記述が必要であったが、Suspense は **宣言的に** 「どの代替 UI を表示させるか」を指定するだけで使える

<details><summary>例）命令的な場合</summary>
 
```javascript
import React, { useState, useEffect } from 'react';

function MyComponent() {
  const [data, setData] = useState(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch('https://api.example.com/data');
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const result = await response.json();
        setData(result);
      } catch (error) {
        setError(error);
      } finally {
        setIsLoading(false);
      }
    };

    fetchData();
  }, []);

  if (isLoading) {
    return <div>Loading...</div>;
  }

  if (error) {
    return <div>Error: {error.message}</div>;
  }

  if (data) {
    return <div>Data: {JSON.stringify(data)}</div>;
  }

  return null; // まだデータがない場合などの初期状態
}

export default MyComponent;
```

</details>

```javascript
import React, { Suspense } from 'react';

// 遅延処理をシミュレートする関数
const delayData = () => {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve('データがロードされました！');
    }, 2000); // 2秒後に解決
  });
};

// Promiseをラップするコンポーネント
const Resource = React.lazy(() => delayData().then(data => ({ default: () => <div>{data}</div> })));

function MyComponent() {
  return (
    <div>
      <h1>Suspenseのサンプル</h1>
      <Suspense fallback={<div>Loading...</div>}>
        <Resource />
      </Suspense>
    </div>
  );
}

export default MyComponent;
```

## Suspense のエラーハンドリングについて
* Suspense のエラー版みたく、予期しないJavaScriptエラーがコンポーネントツリーのどこかで発生した場合に、アプリケーション全体がクラッシュするのを防ぎ、フォールバックUIを表示するためのライブラリは存在する
  * `componentDidCatch(error, info)` ... サードパーティライブラリに頼らずにエラー境界を実装するための機能
  * `react-error-boundary` ... サードパーティライブラリ



