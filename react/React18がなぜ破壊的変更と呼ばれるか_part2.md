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

---
# サスペンスの本質
* **Suspenseはただローディングを表示させるためだけの機能ではない**
  * Suspense 機能の説明において一般的に「API実行時のローディング状態の記述がラクに行えて、フォールバック画面などを代わりに表示させることが出来る」という説明がされがち
* Suspenseの本質を理解するためには以下のワードが関連してくる
  1. SSR  (サーバーサイドレンダリング)
  2. Streaming (ストリーミング)
  3. Hydration (ハイドレーション)

## CSRとSSR
* CSR (Client Side Rendering：クライアントサイドレンダリング)
* SSR (Server Side Rendering：サーバーサイドレンダリング)

### CSR

| 処理の流れ | ユーザーからの見え方 |
| :-: | :-: |
| ![image](https://github.com/user-attachments/assets/edcded94-0242-41e3-a481-a96e80d7f6d6) | ![image](https://github.com/user-attachments/assets/af5460f4-3558-4b94-b387-2799fab120f1)
 |

* クライアント側起点で処理が進行するイメージ
  * JavaScriptがリクエストをお願いするので概ねそのイメージで正しい
  * React単品のアプリは基本コチラに該当
* 画面は先行して表示しておいてデータがまだ届いていないのであればローディング画面を表示するなど
* 初期ロード時は JavaScript がまだ読み込まれていないため、最初の画面描画に時間を要する
  * クライアントのデバイス性能によって処理速度が左右されるといえる

### SSR

| 処理の流れ | ユーザーからの見え方 |
| :-: | :-: |
| ![image](https://github.com/user-attachments/assets/75e09ed6-b484-452c-899a-a30d27efee7a) | ![image](https://github.com/user-attachments/assets/164aac24-61b7-49eb-9918-2bb74862b5e4) |

* 一般的な処理の流れ
  * Rails などの MVC モデルもこちらに該当する
* ユーザー体験の観点からはサーバー側でビュー生成が完了するまでにユーザーを待たせることになる
* ページ遷移の度にリクエストが発生し、サーバー側で HTML を生成するためサーバーの負荷が大きい
  * ただサーバーが処理を担うのでクライアント側のスペックにはあまり左右されない
