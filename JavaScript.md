# JavaScript

---

## モジュールバンドラー・トランスパイラ

### モジュールバンドラーとは

JSファイルやCSSファイル等をまとめてくれるツール。現在の主流はwebpack。

### トランスパイラとは

JavaScriptの記法をブラウザで実行できる形に変換してくれるツール。現在主流はBabel。

<br>

---

## ES2015の書き方

- モダンJavaScript
  - let、constを用いた変数宣言
  - アローファンクション
  - Class構文
  - 分割代入
  - テンプレート文字列（`${}`を使った書き方）
  - スプレッド構文
  - Promise

<br>

### var, let, const

- var・・・上書きや再宣言が可能
- let・・・上書きは可能。再宣言は不可。
- const・・・上書きも再宣言も不可（ただし、オブジェクト型は例外）

<br>

### テンプレート文字列

```javascript
const name = "小堺";
const age = 28;
const message = `私の名前は${name}です。年齢は${age}歳です。`;

console.log(message); // 私の名前は小堺です。年齢は28です。
```

<br>

### アロー関数

```javascript
// 従来の書き方1
function func(value) {
    return value;
};

// 従来の書き方2
const func = function(value) {
    return value;
};

// アロー関数
const func = value => {
    return value;
};

// 単一行処理ならreturnと{}を省略
const func = (value1, value2) => value1 + value2;

// 複数行でも単一行として扱う
const func = (value1, value2) => (
    {
        name: value1,
        age: value2,
    }
);
```

<br>

### 分割代入{}[]

```javascript
const myProfile = {
    name: "小堺",
    age: 28,
};

// オブジェクトの分割代入
const {name ,age} = myProfile;
// const {name:newName ,age:newAge} = myProfile; // プロパティに別名を付ける場合は:で定義
const message = `私の名前は${name}です。年齢は${age}歳です。`;

// 配列でも可能（要素の順番に合わせるだけ）
```

<br>

### デフォルト値

```javascript
const sayHello = (name = "ゲスト") => console.log(`こんにちは！${name}さん！`);

sayHello(); // こんにちは！ゲストさん！
sayHello("小堺"); // こんにちは！小堺さん！

// 分割代入などでも、同様にできる
```

<br>

### スプレッド構文

...を付与することで、配列内要素を順番に展開してくれる。

```javascript
const array = [1, 2];
console.log(...array); // 1 2

const sum = (num1, num2) => console.log(num1 + num2);

// 普通に要素を渡す場合
sum(array[0], array[1]);

// スプレッド構文
sum(...array);
```

スプレッド構文で要素のコピー、結合

```javascript
const arr1 = [1, 2];
const arr2 = [3, 4];

// コピー
const arr3 = [...arr1]; // [1, 2]

// 結合
const arr4 = [...arr1, ...arr2]; // [1, 2, 3, 4]
```

=（イコール）でコピーしてはいけない理由  
イコールでのコピーは、参照値も引き継がれるため。

```javascript
const arr1 = [1, 2];
const arr2 = arr1;
arr2[0] = 100;

console.log(arr1); // [100, 2] // ！元のオブジェクト内容も更新される
console.log(arr2); // [100, 2]
```

<br>

### オブジェクトの省略記法

オブジェクトのプロパティ名と設定する変数名が同一の場合、:を省略できる。

```javascript
const name = "小堺";
const age = 28;

const user = {
    name,
    age,
};

console.log(user); // {name: "小堺", age: 28}
```

<br>

### map, filter

- map・・・配列を順番に処理して、処理した結果を配列として返す
- filter・・・条件に合致するものだけ返す

```javascript
/* map */
const names = ["小堺", "れもん"];
const copy = names.map(name => {
    return name;
});

console.log(copy); // ["小堺", "れもん"]

names.map(name => console.log(name));
// 小堺
// れもん

/* filter */
const nums = [1, 2, 3, 4, 5];
console.log(nums.filter(num => {
    return num % 2 === 1;
}));
// [1, 3, 5]

/* indexも扱える */
names.map((name, index) => console.log(`${index + 1}番目は、${name}です`));
// 1番目は小堺です
// 2番目はれもんです
```

<br>

    ### 論理演算子 && || の本当の意味

- &&は、左側がtrueなら、右側を返す
- ||は、左側がfalseなら、右側を返す

```javascript

console.log(true && "trueです"); // trueです
console.log(false || "falseです"); // falseです
```
