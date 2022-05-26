# プロを目指す人のためのTypeScript入門
## 注意
- x 本書のまとめ
- o 僕が知らなかったことのまとめ

## 1章 イントロダクション
### 1.1
- TSは「JS+型」（と呼ばれる）
### 1.2
- TSの型システムはJSの後付け
  - TSが活きるのはコンパイル時のみ
  - 関数オーバーロードはできない
- トランスパイル(JSへの変換)の挙動
  - 型注釈(+TS特有キーワード)の除去
  - 新しい構文を古い構文に変換
    - 古いブラウザ等に対応させるため
    - TS以外だとBabel, esbuild, SWCとか
- ECMAScript
  - JSのもう一つの名前
  - ECMAScript仕様書によって言語仕様が定義
  - ES1, ES2, ES3, ES5, ES2015, ES2016...2022
    - ES2015からモダンJSの代名詞 
    - ES2015以降は年1の小規模アップデート
  - プロポーザル
    - ESの追加機能
    - ステージ1-4がある
    - ステージ3でTSサポート
    - ステージ4でES仕様書入り
- TS独自機能
  - enum, namespaceとか
  - これらは初期に追加されたもの
  - 「TS独自機能はTSの方針(JS+型)にそぐわない、使うべきではない」という声がある
  - 本書は↑に賛成派
### 1.3
- 本書ではNode上で動くものを書く
- tsconfig.json
  - コンパイラの設定
    - コマンドライン引数でも可能ではある
  - ESversionの変更など
  - （jsonなのに）コメント可
  - 主な設定項目
    - ES version
    - commonjs -> esnext
      - ES Modules
        - ES2015より誕生した公式モジュールシステム
        - モジュールシステム→import, export周り
      - CommonJS
        - ES Modules普及前のモジュールシステム
        - importがrequireだったりする
    - コンパイル対象
    - outディレクトリ
- スクリプトとモジュール
  - TS, JSはこの2種類に分類できる
  - スクリプト
    - デフォルト
    - 変数・型のスコープがプロジェクト全体に広がる
  - モジュール
    - type="module"指定時
    - import, exportが可
      - 他ファイルの変数は、これを用いて使用
  - tsの場合、import/exportがあると自動でmoduleになる
  - 筆者はモジュール推奨
    - 他モジュールからimportした型をスクリプト内で使いたい時に困る
    - 必要ない型がスコープにあるのは良くない
  - export {};
    - お手軽モジュール化
- コンパイル
  - $ npx tsc とか
    - tsconfig.jsonに従ってコンパイル
  - 1個のtsファイルが1個のjsファイルに
    - 1個のjsにまとめるのも可能だが非推奨
  - TSのNodeを書く際は、JSにコンパイルしてからNode実行が基本的な流れ
### npm, package.json周り(他章・書籍外含)
- npm
  - オンライン上のパッケージレジストリ
  - node付属のパッケージ操作CLI
    - 似たものにyarnがあるが、npmも改善されてきたので、スタンダードなnpmでおｋ
  - プロジェクト1つ開発 = npmパッケージ1つ開発
- npx
  - npm5.2から同梱されてるもの
  - npmとの違い
    - 未インストールのパッケージも実行可
    - package.jsonを変更しない
- package(lock).json
  - package.json
    - インストールすべきパッケージのバージョンの範囲
    - 編集可
  - package-lock.json
    - $ npm iで実際に入ったバージョン
    - 編集NG
    - $ npm ci
  - $ npm i
    - package-lock.jsonがなければpackage.jsonを参照してインストール
    - lockがあればlockが参照されるが、矛盾があればpackage.jsonを参照
  - $ npm ci
    - lockを参照
    - lockは更新されない
    - node_modulesをすべて削除してからパッケージをインストール
## 2章 基本的な文法・型
### 2.1
- TSにおける改行は空白の一種
- 変数は小文字、型名は大文字で始めるのが慣習
### 2.2
- let
  - 宣言時に値を代入しなくても良い
    - undefindが入る
  - 極力使うべきではない
    - 変数の9割以上はconst
    - 読む人の負担になる
### 2.3
- プリミティブ
  - primitive(原始的な)
  - 基本的な値のこと
    - 文字列
    - 数値
    - 真偽値
    - BigInt
    - null
    - undefined
    - シンボル(難しいので書籍対象外)
    - Records, Tuples(導入予定)
      - 書籍執筆時点でステージ2
  - オブジェクトはプリミティブの組み合わせ
- number型
  - 整数と小数の区別がない
  - 0xff 0b1010 等の2進数・16進数表記も可
  - 1e8, 4e-5等の指数表記も可
  - 1_000_000 等のハイフン区切りも可
  - IEEE 754倍精度浮動小数点数
    - 仮数部53ビット
      - 2^53まで表現可能
    - 有限精度
      - 0.1 + 0.2 -> 0.3000...4
- BigInt
  - 精度の制限は無い
  - numberより遅い
  - 123n のようにnを書く
  - 整数に丸められる
    - 5n / 2n -> 2n
  - 普通の数値(number)を混ぜるとエラー
- 文字列
  - ""と''の差はない
  - バッククォート
    - 改行が可能
    - 式を埋め込める
      - \`変数str1: ${str1}\`
      - \`${123 + 456}\`
        - 789
        - 文字列に変換される
  - エスケープシーケンス
    - \n \t など
    - \を入れるには\\\\
    - UTF-16
      - \\u{796d} -> 祭
- null, undefined
  - 筆者はundefined推奨
    - TSの言語仕様上ではサポートが厚い
- プリミティブ型同士の変換
  - "1234" + 5678 -> "12345678"
  - Number
    - NaNに注意
    - Number("123") -> 123
    - Number(true) -> 1
    - Number(false) -> 0
    - Number(null) -> 0
    - Number(undefined) -> NaN
  - BigInt
    - BigInt("1234") -> 1234n
    - BigInt(1234) -> 1234n
    - BigInt(true) -> 1n
    - BigInt("fooooo") -> エラー
      - BigIntにNaNがないため
  - String
    - String(1234.5) -> "1234.5"
    - String(true) -> "true"
    - String(null) -> "null"
    - String(undefined) -> undefined
  - Boolean
    - Boolean(123) -> true
    - Boolean(0) -> false
    - Boolean(NaN) -> false
    - Boolean(1n) -> true
    - Boolean(0n) -> false
    - Boolean("") -> false
    - Boolean("foo") -> true
    - Boolean(null) -> false
    - Boolean(undefined) -> false
### 2.4
- 演算子
  - Number(str)は+strでも書ける
  - 文字列連結
    - これは古い
      - こんにちは+name+さん
    - これが主流
      - \`こんにちは${str1}さん\`
  - 一致判定
    - ==, !=は基本使わない
      - 型変換によりtrueになったりする
      - "3" == 3 true
      - "3" === 3 false
      - 例外
        - 以下が等価なので、短く書きたい人によってはアリ
        - x == null
        - x === null || x === underfined
    - NaNに注意
      - 常にfalseを返す
        - x <= 10も、x > 10も
        - x === NaNすらも
        - Number.isNanを使おう
    - Boolean(式)を!!式で短縮する派閥もある
  - 論理演算子
    - &&
      - "foo" && "bar" → "bar"
      - 0 && 123 → 0
    - ||
      - "foo"|| "bar" → "foo"
      - 0 || 123 → 123
      - デフォルト値によく使う
    - ??
      - ES2020で登場
      - x ?? y
        - null, undefinedならy, 違うならx
        - ||だと空文字、0、falseもyに
      - process.env.環境変数名
        - 環境変数名が無いとundefined
        - 使用例の一つ
  - 条件演算子
    - 3項演算子
      - constの代入とかで使う(if分岐だとlet)
  - 論理演算子に対応した代入演算子
    - &&=, ||=, ??= の3つ
    - ES2021で導入
    - x ||= yとx = x || yは**ほぼ**同じ
      - 短絡評価がある
      - x = x || y
        - xにxが代入される
      - x ||= y
        - 代入すら起こらない
        - setter等、代入による副作用が起こらない
  - ビット演算
    - ~
      - ビット反転
      - ~0b0101
        - -6(-5-1)
  - void
    - void 式
    - undefinedを返す
    - 型名としても使える
## 3章 オブジェクトの基本とオブジェクトの型
### 3.1
- オブジェクトは連想配列
- プロパティ名と変数名が同じ場合、省略可能
  - ```
    const name = "hoge"
    const user = {
      name: name
    }
    const user2 = {
      name
    }
- プロパティ名に文字列も利用可能
  - ```
      const obj = {
        "foo bar" = 123
      }
    - 識別子に使えない文字列も使用可
- 数値も可
- 動的な値も可
  - ```
      const propName = "foo";
      const obj = {
        [propName]: 123
      };
      
      console.log(obj.foo) // 123
- constで宣言されたオブジェクトのプロパティの値は変更可
  - ただし型が違う値はダメ
- スプレッド構文
  - プロパティを別オブジェクトからコピーできる
  - ```
    const obj1 = {
      bar: 456,
      baz: 789
    };
    
    const obj2 = {
      foo: 123,
      ...obj1
    };
  - プロパティが重複してる場合、後の記述が採用
    - ビタ書きプロパティをスプレッド構文で上書くとコンパイルエラー
    - 無意味なため
  - 参照ではなくコピー
- オブジェクトはいつ"同じ"なのか
  - オブジェクトの代入は参照渡し
    - ```
      const foo = {num: 1234};
      const bar = foo;
      bar.num = 0; // foo.numも0になる
  - オブジェクトのコピー方法
    - スプレッド構文
      - ```
        const foo = {num: 1234};
        const bar = {...foo};
      - プロパティ内のオブジェクトは参照渡しなので注意
      - ネストしたオブジェクトも含めたコピーの標準的方法は無い
        - ネストしたオブジェクトにもスプレッド記法を使えば不可能ではない（記述は多いが）
  - 参照渡しされてるなら、比較でtrue
### 3.2 オブジェクト型の記法
- 型注釈
  - ```
    const obj: {
      foo: number;
      bar: string;
    } = {
      foo: 123,
      bar: "Hello"
    };
  - ↑をtypeでやるのが一般的
    ```
    type FooBarObj = {
      foo: number;
      bar: string;
    };
    
    const obj: FooBarObj = {
      foo: 123,
      bar: "Hello"
    };
  - interface宣言でも可
    - typeでほぼ代用可なので、使わない流儀が多い
    - 2014年以前は、type文が無くこれが用いられた
- インデックスシグネチャ
  - プロパティ名を指定しない
    ```
    type PriceData = {
      [key: string]: number
    }
    
    const data: PriceData = {
      apple: 200,
      coffee: 120,
      bento: 500
    };
  - 型安全性が破壊されるので注意
    ```
    type MyObj = {[key: string]: number };
    const obj: MyObj = {foo: 123};
    
    const num: number = obj.bar; // undefined
    ```
    - undefinedはnumber型ではない
    - インデックスシグネチャでない場合は、エラーになる
  - 上記理由により、推奨できない
    - Mapオブジェクトで代替できる
- オプショナルなプロパティ
- 

## 5章
### 5.1
- クラスのプロパティは初期値が必要
  - 宣言時 or コンストラクタ
- オプショナルなプロパティ
  - ```
    class User {
      name?: string;
    }
- 読み取り専用プロパティ
  - ```
    class User {
      readonly name: string = "";
    }
- クラスは変数
  - ```
    class User {name: string = ""}
    const obj = {cl: User};
    const uhyo = new obj.cl();
