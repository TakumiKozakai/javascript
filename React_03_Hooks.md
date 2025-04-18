# React_Hooks

## React Hooks

状態やライフサイクルを扱う関数コンポーネント。

- Reactに標準で搭載されている各種Hook
  - useState
  - useEffect
  - useCallback
  - useMemo
  - useContext
  - useRef
  - useReducer
  - useLayoutEffect
  - useImperaTiveHandle
  - useDebugValue

## State と useState

### State

コンポーネントの状態を表す値。例えば、以下のような画面の状態を保持する。

- エラーがあるか
- モーダルウィンドウを開いているか
- ボタンを押せるか
- テキストボックスに何を入力したか

### useState

状態を扱うためのフック。「state」と「stateを更新する関数」を作る関数。  
更新関数を呼び出すと状態が変化して、フックがあるコンポーネントは再描画される。  
更新関数を呼ぶには、引数に値を渡す方法と関数を渡す方法の2種類があり、  
値を渡した場合はその値が次の状態となり、関数を渡した場合は関数の戻り値が次の状態になる。

```javascript
// const [状態, 更新関数] = useState(初期値)
const [state, setState] = useState(initialState);
```

※State値ではなくProps値を変更すれば良いのでは？と思うが、仕様上Props値は呼び出し元から任意のタイミングで変更される可能性があるため、呼び出されるコンポーネント側で変更してはならない。

ex1.カウンター（引数に値を渡す方法）

```javascript
const Counter = (props: CounterProps) => {
    const { initialValue } = props
    const [count, setCount] = useState(initialValue)

    return (
        <>
            <p>Count: {count}</p>
            <button onClick={() => setCount(count - 1)}>-</button>
            <button onClick={() => setCount(prev => prev + 1)}>+</button>
                                            // prevには更新直前の値が渡される
        </>
    )
}
```

ex2.子コンポーネントから親コンポーネントへの情報伝達（引数に関数を渡す方法）

```javascript StateParent.js
import { useState } from 'react';
import StateCounter from './StateCounter';

export default function StateParent() {
    const [count, setCount] = useState(0);
    const update = step => setCount(prev => prev + step);

    return (
        <>
            <p>総カウント：{count}</p>
            <StateCounter step={1} onUpdate={update} />
            <StateCounter step={-1} onUpdate={update} />
        </>
    );
}
```

```javascript StateCounter.js
export default function StateCounter({ step, onUpdate }) {
    const handleClick = () => onUpdate(step);

    return (
        <button onClick={handleClick}>
            <span>{step}</span>
        </button>
    )
}
```

## useReducer

状態を扱うためのフック。複雑な状態遷移をシンプルに記述できる。  
配列やオブジェクトなどの複数のデータをまとめたものを状態として扱う場合に用いる。  
useStateでは更新関数に次の状態を直接渡していたが、useReducerでは更新関数（dispatch）にactionと呼ばれるデータを渡している。  
現在の状態とactionを渡すと次の状態を返すreducerという関数を用いる。

```javascript
reducer(現在の状態, action) {
    return 次の状態
}

const [現在の状態, dispatch] = useReducer(reducer, reducerに渡される初期状態)
```

ex.カウントを2倍にする機能、カウントをリセットする機能

```javascript
import { useReducer } from 'react'

type Action = 'INCREMENT' | 'DECREMENT' | 'DOUBLE' | 'RESET'

const reducer = (currentCount: number, action: Action) => {
    switch(action) {}
        case 'INCREMENT':
            return currentCount + 1
        case 'DECREMENT':
            return currentCount - 1
        case 'DOUBLE':
            return currentCount * 2
        case 'RESET':
            return 0
        default:
            return currentCount
    }
}

type CounterProps = {
    initialValue: number
}

const Counter = (props: CounterProps) => {
    const { initialValue } = props
    const [count, dispatch] = useReducer(reducer, initialValue)

    return (
        <>
            <p>Count: {count}</p>
            <button onClick={() => dispatch('DECREMENT')}>-</button>
            <button onClick={() => dispatch('INCREMENT')}>+</button>
            <button onClick={() => dispatch('DOUBLE')}>*2</button>
            <button onClick={() => dispatch('RESET')}>Reset</button>
        </>
    )
}

export defaullt Counter
```

上記のように、setState()と比べて状態の更新を呼び出す側は具体的な状態に依存しないためコードをシンプルに保てる。  
また、状態を更新するロジックをコンポーネント外の関数に切り出しているため、テストが容易になる。

## useEffect と 再レンダリング

### useEffect

コンポーネントの副作用を制御するフック。「ある値が変わった時に限り、処理を実行する」関数。

```javascript
// useEffect(実行する関数, [依存する値1, 依存する値2]);
// num の値が変化した時に alert() を実行する
useEffect(() => { alert() }, [num1, num2]);
```

```javascript
import { useState } from "react";
const Effect = () => {
    const [num, setNum] = useState(0);

    useEffect(() => {
        alert();
    }, [num]);
}
```

useEffectは、依存配列に指定している値が変わった時に加え、コンポーネントのマウント時（初回レンダリング時）にも実行される。  
そのため、userEffectの第二引数に[]を設定すると、「コンポーネントを表示した初回のみ実行するような処理」を記述できる。  
この挙動は、画面を表示して初期データを取得する時などによく用いられる。

なぜこの機能があるかというと、コンポーネントは再レンダリングを何度も繰り返すためStateの数が多くなると再レンダリングのコストが無駄にかかる。  
そのため、特定の値が変わった時にだけ再レンダリングするよう制御することによってコストを減らせる。

### 再レンダリング

イベントをきっかけに画面表示内容が変わるのは、コンポーネントが再レンダリングされているためである。

```javascript
export const App = () => {
    console.log("レンダリング");
    const [num, setNum] = useState(0);
    ・・・
}
```

上記のコードを実行すると、初回画面表示時に「レンダリング」と出力され、numをカウントアップする度に「レンダリング」が追加で出力される。  
つまり、Stateが更新される度に毎回関数コンポーネントは再び頭から処理が実行される。  
（毎回と言っても、初回レンダリング（マウント）時と再レンダリング時は処理が異なり、useStateの初期値はマウント時だけ適用され、毎回初期化される訳ではない）。

## メモ化用フック - useCallback、useMemo -

メモ化用のフック。値や関数を保持し、必要のない子要素のレンダリングや計算を抑制するために使用する。

### メモ化とは

メモ化とは、ある関数の計算結果を保持し、同一の呼出があった場合は保持しておいた結果を返すといった計算結果を再利用する最適化手法のこと。

- Reactコンポーネントの再描画タイミング
  - propsや内部状態が更新された時
  - コンポーネント内で参照しているContextの値が更新された時
  - 親コンポーネントが再描画された時

親コンポーネントが再描画されると無条件に子コンポーネントが描画される。このため、上位のコンポーネントが再描画されるとそれ以下の全てのコンポーネントが再描画されるためパフォーマンスに良くない。  
この再描画の伝搬を止めるためにメモ化コンポーネントを使用する。  
メモ化コンポーネントは、親コンポーネントで再描画が発生してもpropsやcontextの値が変化しない場合は親コンポーネントによる再描画が発生しない。

ex.

```javascript
import React, { memo, useState } from 'react'

type FizzProps = {
    isFizz: boolean
}

// Fizzは通常の関数コンポーネント
// isFizzがtrueの場合はFizzと表示して、それ以外は何も表示しない
// isFizzの変化に関わらず、親が再描画されるとFizzも再描画される
const Fizz = (props: FizzProps) => {
    const { isFizz } = props
    console.log(`Fizzが再描画されました, isFizz=${isFizz}`)
    return <span>{isFizz ? 'Fizz' : ''}</span>
}

type BuzzProps = {
    isBuzz: boolean
    onClick: () => void
}

// Buzzはメモ化した関数コンポーネント
// isBuzzがtrueの場合はBuzzと表示して、それ以外は何も表示しない
// 親コンポーネントが再描画されても、isBuzzが変化しない限りはBuzzは再描画しない
const Buzz = memo<BuzzProps>((props) => {
    const {isBuzz, onClick } = props
    console.log(`Buzzが再描画されました, isBuzz=${isBuzz}`)
    return (
        <span onClick={onClick}>{isBuzz ? 'Buzz' : ''}</span>
    )
})

// この形式でexportした時は、import { Parent } from ...で読み込む
export const Parent = () => {
    const [count, setCount ] = useState(1)
    const isFizz = count % 3 === 0
    const isBuzz = count % 5 === 0

    // この関数はParentの再描画の度に作成される
    // メモ化コンポーネントに関数やオブジェクトを渡すと、親の再描画によってコンポーネントの再描画が発生する
    const onBuzzClick = () => {
        console.log(`Buzzがクリックされました, isBuzz=${isBuzz}`)
    }
    console.log(`Parentが再描画されました, count=${count}`)
    return (
        <div>
            <button onClick={() => setCount((c) => c+1)}>+1</button>
            <p>{`現在のカウント: ${count}`}</p>
            <p>
                <Fizz isFizz={isFizz} />
                // 再描画の度にParentで新しく作られた関数がBuzzに渡されるため、再描画してしまう
                // この問題はuseCallbackやuseMemoで関数や値をメモ化することにより回避できる
                <Buzz isBuzz={isBuzz} onClick={onBuzzClick}/>
            </p>
        </div>
    )
}
```

### useCallback

関数をメモ化するためのフック。

```javascript
const f = useCallback(() => { 処理 }
```

```javascript
import React, { useState, useCallback } from "react";

type ButtonProps = {
  onClick: () => void;
};

// DecrementButtonは通常の関数コンポーネントでボタンを表示する
const DecrementButton = (props: ButtonProps) => {
  const { onClick } = props;
  console.log("DecrementButtonが再描画されました");
  return <button onClick={onClick}>Decrement</button>;
};

// IncrementButtonはメモ化した関数コンポーネントでボタンを表示する
const IncrementButton = React.memo((props: ButtonProps) => {
  const { onClick } = props;
  console.log("IncrementButtonが再描画されました");
  return <button onClick={onClick}>Increment</button>;
});

// DoubleButtonはメモ化した関数コンポーネントでボタンを表示する
const DoubleButton = React.memo((props: ButtonProps) => {
  const { onClick } = props;
  console.log("DoubleButtonが再描画されました");
  return <button onClick={onClick}>Double</button>;
});

export const UseCallback = () => {
  const [count, setCount] = useState(0);

  const decrement = () => {
    setCount((c) => c - 1);
  };
  const increment = () => {
    setCount((c) => c + 1);
  };
  // useCallbackを使って関数をメモ化する
  const double = useCallback(() => {
    setCount((c) => c * 2);
    // 第二引数はから配列なので、useCallbackは常に同じ関数を返す
  }, []);

  return (
    <div>
      <p>Count: {count}</p>
      {/* コンポーネントに関数を渡す */}
      <DecrementButton onClick={decrement} />
      {/* メモ化コンポーネントに関数を渡す */}
      <IncrementButton onClick={increment} />
      {/* メモ化コンポーネントにメモ化した関数を渡す */}
      <DoubleButton onClick={double} />
    </div>
  );
};

```

### useMemo

値のメモ化をするフック。第一引数に値を生成する関数を、第二引数に依存配列を渡す。
