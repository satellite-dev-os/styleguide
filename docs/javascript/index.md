# SATELLITE JavaScript スタイルガイド

## 1 はじめに

このドキュメントは、JavaScriptソースファイルに対するSATELLITEの完全なコーディング標準を定めるものである。JavaScriptソースファイルは、ここに記載されたすべてのルールに従っている場合にのみ「SATELLITE Style」に準拠しているとみなされる。

このガイドは、フォーマットの美学とより広範なコーディング規約の両方を扱い、普遍的に強制可能なルールに焦点を当て、主観的なアドバイスを避けている。

### 1.1 用語に関する注記

「コメント」という用語は、もっぱら実装コメントを指す。ドキュメンテーションコメントには、`/** … */` ブロック内のコンテンツに対して「JSDoc」という用語を使用する。

このガイドではRFC 2119の用語を適用する。「しなければならない（must）」「してはならない（must not）」「すべきである（should）」「すべきでない（should not）」「してもよい（may）」はそれぞれ標準的な意味で使用される。「推奨する（prefer）」と「避ける（avoid）」はそれぞれ「すべきである」と「すべきでない」に対応する。

### 1.2 ガイドに関する注記

このガイドに示されるコード例は非規範的である。つまり、正しいスタイルで記述されているが、例が唯一の有効なアプローチを示しているわけではない。例で行われている任意のフォーマット選択を強制ルールとしてはならない。

## 2 ソースファイルの基本

### 2.1 ファイル名

ファイル名は小文字を使用し、任意でアンダースコアまたはダッシュを含めることができるが、その他の句読点は使用しない。プロジェクトは一貫した命名規則に従うべきである。すべてのファイルには `.js` 拡張子が必要である。

### 2.2 ファイルエンコーディング: UTF-8

ソースファイルはUTF-8でエンコードしなければならない。

### 2.3 特殊文字

#### 2.3.1 空白文字

行末文字を除き、ソースファイルに出現するのはASCII水平スペース文字（0x20）のみである。これは以下を意味する：

- 文字列リテラル内の他のすべての空白はエスケープしなければならない
- タブ文字はインデントに使用してはならない

#### 2.3.2 特殊エスケープシーケンス

特殊エスケープシーケンス（`\'`、`\"`、`\\`、`\b`、`\f`、`\n`、`\r`、`\t`、`\v`）を使用し、`\x0a` や `\u000a` のような数値エスケープは使用しないこと。レガシーの8進数エスケープは決して許可されない。

#### 2.3.3 非ASCII文字

残りの非ASCII文字については、実際のUnicode文字（例：`∞`）または16進数/Unicodeエスケープ（例：`\u221e`）のうち、可読性に基づいて選択すること。

ベストプラクティス — 実際のUnicodeはコードをより明確にする：

```javascript
const units = 'μs';
```

コメント付きで許容される：

```javascript
const units = '\u03bcs'; // 'μs'
```

非印刷文字に適している：

```javascript
return '\ufeff' + content;  // バイトオーダーマークを先頭に付加
```

不適切なアプローチ：

```javascript
const units = '\u03bcs';  // 読者はこの文字を特定できない
```

注：非ASCIIの処理に対する懸念からコードの可読性を下げてはならない。壊れたプログラムは修正されるべきであり、コードを制限すべきではない。

## 3 ソースファイルの構造

すべての新しいソースファイルは、`goog.module` ファイル（`goog.module` 呼び出しを含む）またはECMAScriptモジュール（`import` と `export` を使用する）のいずれかでなければならない。

ファイルには以下のセクションが**順番に**含まれる：

1. ライセンスまたは著作権情報（存在する場合）
2. `@fileoverview` JSDoc（存在する場合）
3. `goog.module` 文（goog.moduleファイルの場合）
4. ES `import` 文（ESモジュールの場合）
5. `goog.require` および `goog.requireType` 文
6. ファイルの実装

存在する各セクションは**正確に1つの空行**で区切られる。ただし、実装セクションの前には1〜2行の空行を置いてもよい。

### 3.1 ライセンスまたは著作権情報

該当する場合、この情報はファイルの先頭に記載される。

### 3.2 `@fileoverview` JSDoc

標準的なJSDocフォーマットルールに従う。

### 3.3 `goog.module` 文

ファイルは1行で正確に1つの `goog.module` 名を宣言しなければならない。これらの行は80カラム制限の例外であり、折り返してはならない。

モジュール引数は名前空間を定義する。パッケージ名（ディレクトリ構造を反映する）に加えて、任意でメインのクラス/列挙型/インターフェイスを `lowerCamelCase` で含める：

```javascript
goog.module('search.urlHistory.urlHistoryService');
```

#### 3.3.1 階層

モジュール名前空間は、別のモジュールの名前空間の直接の子であってはならない：

```javascript
// 非推奨：
goog.module('foo.bar');
goog.module('foo.bar.baz');
```

#### 3.3.2 `goog.module.declareLegacyNamespace`

`goog.module` 文の後に任意で `goog.module.declareLegacyNamespace();` を続けてもよい。可能な限りこれを避けること。

```javascript
goog.module('my.test.helpers');
goog.module.declareLegacyNamespace();
goog.setTestOnly();
```

#### 3.3.3 `goog.module` のエクスポート

シンボルは `exports` オブジェクトを介してエクスポートされる。シンボルは `exports` に直接定義するか、ローカルで宣言してから別途エクスポートしてもよい。外部使用を目的としたシンボルのみをエクスポートすること。エクスポートされないモジュールローカルシンボルには `@private` アノテーションを付けない。

例：

```javascript
const /** !Array<number> */ exportedArray = [1, 2, 3];

const /** !Array<number> */ moduleLocalArray = [4, 5, 6];

/** @return {number} */
function moduleLocalFunction() {
  return moduleLocalArray.length;
}

/** @return {number} */
function exportedFunction() {
  return moduleLocalFunction() * 2;
}

exports = {exportedArray, exportedFunction};
```

```javascript
/** @const {number} */
exports.CONSTANT_ONE = 1;

/** @const {string} */
exports.CONSTANT_TWO = 'Another constant';
```

`exports` に `@const` アノテーションを付けないこと。コンパイラが自動的に定数として扱う。

非推奨：

```javascript
// デフォルトエクスポートを使用しない
exports = FancyClass;
```

### 3.4 ESモジュール

ESモジュールは `import` と `export` キーワードを使用する。

#### 3.4.1 インポート

インポート文は折り返してはならず、80カラム制限の例外である。

##### 3.4.1.1 インポートパス

ESモジュールファイルは `import` 文を使用して他のESモジュールをインポートしなければならない。ESモジュールに対して `goog.require` を使用してはならない。

```javascript
import './sideeffects.js';

import * as goog from '../closure/goog/goog.js';
import * as parent from '../parent.js';

import {name} from './sibling.js';
```

###### 3.4.1.1.1 ファイル拡張子

`.js` 拡張子は必須であり、常に含めなければならない：

```javascript
// 不正：
import '../directory/file';

// 正しい：
import '../directory/file.js';
```

##### 3.4.1.2 同じファイルの複数回インポート

同じファイルを複数回インポートしないこと。これは集約インポートを不明瞭にする。

```javascript
// 非推奨 - 同じパス：
import {short} from './long/path/to/a/file.js';
import {aLongNameThatBreaksAlignment} from './long/path/to/a/file.js';
```

##### 3.4.1.3 インポートの命名

###### 3.4.1.3.1 モジュールインポート名

モジュールインポート名（`import * as name`）は、インポートされるファイル名から導出された `lowerCamelCase` を使用する：

```javascript
import * as fileOne from '../file-one.js';
import * as fileTwo from '../file_two.js';
import * as fileThree from '../filethree.js';
```

###### 3.4.1.3.2 デフォルトインポート名

デフォルトインポート名はファイル名から導出され、標準的な命名規則に従う：

```javascript
import MyClass from '../my-class.js';
import myFunction from '../my_function.js';
import SOME_CONSTANT from '../someconstant.js';
```

注：デフォルトエクスポートは一般的に禁止されている。デフォルトインポートは非準拠コードをインポートする場合にのみ出現する。

###### 3.4.1.3.3 名前付きインポート名

名前付きインポートは元の名前を保持すべきである。衝突を解決するには、エイリアス（`import {SomeThing as SomeOtherThing}`）よりもモジュールインポートまたはエクスポート自体のリネームを推奨する：

```javascript
import * as bigAnimals from './biganimals.js';
import * as domesticatedAnimals from './domesticatedanimals.js';

new bigAnimals.Cat();
new domesticatedAnimals.Cat();
```

リネームが必要な場合は、モジュールのファイル名またはパスのコンポーネントを使用する：

```javascript
import {Cat as BigCat} from './biganimals.js';
import {Cat as DomesticatedCat} from './domesticatedanimals.js';

new BigCat();
new DomesticatedCat();
```

#### 3.4.2 エクスポート

外部使用を目的としたシンボルのみをエクスポートすること。エクスポートされないモジュールローカルシンボルには `@private` アノテーションを付けない。

##### 3.4.2.1 名前付きエクスポート vs デフォルトエクスポート

名前付きエクスポートのみを使用すること。宣言に `export` を適用するか、`export {name};` 構文を使用する：

```javascript
// 正しい - 名前付きエクスポート：
export class Foo { ... }

// 別のスタイル：
class Foo { ... }
export {Foo};

// 不正 - デフォルトエクスポート：
export default class Foo { ... }
```

##### 3.4.2.2 エクスポートの可変性

エクスポートされた変数は、モジュール初期化の外部で変更してはならない。代替手段として、可変フィールドを持つ定数オブジェクト参照のエクスポートや、アクセサ関数のエクスポートがある。

##### 3.4.2.3 export from

`export from` 文は折り返してはならない。80カラム制限の例外である：

```javascript
export {specificName} from './other.js';
export * from './another.js';
```

#### 3.4.3 循環依存

仕様が許可していても、ESモジュール間のサイクルを作成してはならない。

#### 3.4.4 Closureとの相互運用

##### 3.4.4.1 googの参照

Closureの `goog.js` をインポートして `goog` 名前空間を参照する：

```javascript
import * as goog from '../closure/goog/goog.js';

const {compute} = goog.require('a.name');

export const CONSTANT = compute();
```

##### 3.4.4.2 ESモジュールでの goog.require

`goog.require` はESモジュールでも `goog.module` ファイルと同様に機能する。

##### 3.4.4.3 ClosureモジュールIDの宣言

`goog.declareModuleId` はESモジュール内で `goog.module` のようなIDを宣言する。

### 3.5 `goog.setTestOnly`

`goog.module` ファイルでは、`goog.setTestOnly()` は任意でモジュール文と `goog.module.declareLegacyNamespace()` の後に続けてもよい。

ESモジュールでは、`goog.setTestOnly()` は任意でインポート文の後に続けてもよい。

### 3.6 `goog.require` および `goog.requireType` 文

依存関係は `goog.require` および `goog.requireType` でインポートされる。`goog.require` でインポートされた名前はコードと型アノテーションの両方で使用でき、`goog.requireType` の名前は型アノテーションでのみ使用できる。

これらの文は、`goog.module` 宣言の後に1つの空行を挟んで、空行のない連続したブロックを形成する。各文は単一の定数エイリアスに割り当てるか、複数のエイリアスに分割代入する。

`goog.require` で単一のエイリアスに割り当てる場合、エイリアスは名前空間の最後のドット区切りコンポーネントと一致しなければならない：

```javascript
const asserts = goog.require('goog.asserts');
```

分割代入されたエイリアスは、コロンの後にスペースを必要とする：

```javascript
const {MyClass} = goog.require('some.package');
const {MyClass: NsMyClass} = goog.require('other.ns');
```

### 3.7 ファイルの実装

すべての依存関係宣言の後（少なくとも1行の空行で区切って）、ファイルの実際の実装が続く。モジュールローカル宣言とエクスポートされたシンボルで構成される。

## 4 フォーマット

**用語の注記**：*ブロック風の構造体*は、クラス、関数、メソッド、または波括弧で区切られたコードブロックの本体を指す。配列リテラルやオブジェクトリテラルも任意でブロック風の構造体として扱ってもよい。

ヒント：`clang-format` を使用すること。JavaScriptコミュニティはclang-formatがJavaScriptファイルに対して正しく動作するよう努力してきた。`clang-format` はいくつかの一般的なエディタとの統合がある。

### 4.1 波括弧

#### 4.1.1 すべての制御構造に波括弧を使用する

波括弧はすべての制御構造（`if`、`else`、`for`、`do`、`while`、その他）に必須であり、本体に単一の文しか含まれない場合も同様である。非空ブロックの最初の文はそれ自身の行で始まらなければならない。

非推奨：

```javascript
if (someVeryLongCondition())
  doSomething();

for (let i = 0; i < foo.length; i++) bar(foo[i]);
```

例外：`else` がなく、完全に1行に収まる単純なif文は、可読性が向上する場合に限り、波括弧なしで1行に保持してもよい。

```javascript
if (shortCondition()) foo();
```

#### 4.1.2 非空ブロック：K&Rスタイル

波括弧は非空ブロックおよびブロック風の構造体に対してKernighan and Ritchieスタイル（エジプト括弧）に従う：

- 開き波括弧の前に改行なし
- 開き波括弧の後に改行
- 閉じ波括弧の前に改行
- 閉じ波括弧の後に改行（その波括弧が文、関数またはクラス文の本体を終了する場合）。ただし、波括弧の後に `else`、`catch`、`while`、カンマ、セミコロン、または閉じ丸括弧が続く場合は改行しない

例：

```javascript
class InnerClass {
  constructor() {}

  /** @param {number} foo */
  method(foo) {
    if (condition(foo)) {
      try {
        // 注意：これは失敗する可能性がある
        something();
      } catch (err) {
        recover();
      }
    }
  }
}
```

#### 4.1.3 空ブロック：簡潔にしてもよい

空のブロックまたはブロック風の構造体は、開いた直後に閉じてもよい（`{}`）。ただし、複数ブロック文（`if`/`else` や `try`/`catch`/`finally`）の一部である場合は除く。

```javascript
function doNothing() {}
```

非推奨：

```javascript
if (condition) {
  // …
} else if (otherCondition) {} else {
  // …
}

try {
  // …
} catch (e) {}
```

### 4.2 ブロックインデント：+2スペース

新しいブロックまたはブロック風の構造体が開かれるたびに、インデントは2スペース増加する。ブロックが終了すると、インデントは前のインデントレベルに戻る。

#### 4.2.1 配列リテラル：任意でブロック風

配列リテラルは任意で「ブロック風の構造体」としてフォーマットしてもよい。以下はすべて有効である：

```javascript
const a = [
  0,
  1,
  2,
];

const b =
    [0, 1, 2];

const c = [0, 1, 2];

someMethod(foo, [
  0, 1, 2,
], bar);
```

#### 4.2.2 オブジェクトリテラル：任意でブロック風

オブジェクトリテラルも任意で「ブロック風の構造体」としてフォーマットしてもよい。

```javascript
const a = {
  a: 0,
  b: 1,
};

const b =
    {a: 0, b: 1};

const c = {a: 0, b: 1};

someMethod(foo, {
  a: 0, b: 1,
}, bar);
```

#### 4.2.3 クラスリテラル

クラスリテラル（宣言式または式のいずれでも）はブロックとしてインデントする。メソッドの後やクラス宣言の閉じ波括弧の後にセミコロンを追加しないこと。継承には `extends` キーワードで十分だが、スーパークラスがテンプレート化されている場合は除く。

例：

```javascript
/** @template T */
class Foo {
  /** @param {T} x */
  constructor(x) {
    /** @type {T} */
    this.x = x;
  }
}

/** @extends {Foo<number>} */
class Bar extends Foo {
  constructor() {
    super(42);
  }
}
```

#### 4.2.4 関数式

関数呼び出しの引数リスト内で無名関数を宣言する場合、関数の本体は前のインデント深度から2スペース多くインデントする。

```javascript
prefix.something.reallyLongFunctionName('whatever', (a1, a2) => {
  // 関数本体を上の行の'prefix'文のインデント深度に対して+2でインデント
  if (a1.equals(a2)) {
    someOtherLongFunctionName(a1);
  } else {
    andNowForSomethingCompletelyDifferent(a2.parrot);
  }
});

some.reallyLongFunctionCall(arg1, arg2, arg3)
    .thatsWrapped()
    .then((result) => {
      // 関数本体を'.then()'呼び出しのインデント深度に対して+2でインデント
      if (result) {
        result.use();
      }
    });
```

#### 4.2.5 switch文

他のブロックと同様に、switchブロックの内容は+2でインデントする。

switchラベルの後に改行を入れ、インデントレベルは+2に増加する。次のswitchラベルは前のインデントレベルに戻る。

`break` と次のcaseの間の空行は任意である。

```javascript
switch (animal) {
  case Animal.BANDERSNATCH:
    handleBandersnatch();
    break;

  case Animal.JABBERWOCK:
    handleJabberwock();
    break;

  default:
    throw new Error('Unknown animal');
}
```

### 4.3 文

#### 4.3.1 1行に1つの文

各文の後に改行を入れる。

#### 4.3.2 セミコロンは必須

すべての文はセミコロンで終了しなければならない。自動セミコロン挿入に頼ることは禁止されている。

### 4.4 カラム制限：80

JavaScriptコードには80文字のカラム制限がある。以下に記載されている例外を除き、この制限を超える行は折り返さなければならない。

例外：

1. `goog.module`、`goog.require`、`goog.requireType` 文
2. ES モジュールの `import` および `export from` 文
3. カラム制限に従うことが不可能または発見性を妨げる行。例えば：
   - ソース内でクリック可能であるべき長いURL
   - コピーペースト用のシェルコマンド
   - 全体として検索される必要がある長い文字列リテラル

### 4.5 行の折り返し

**用語の注記**：*行の折り返し*は、そうでなければ合法的に1行に収まるコードのチャンクを、カラム制限に従うために複数行に分割することである。

すべての状況で正確にどのように行を折り返すかを示す包括的で決定論的な公式はない。同じコードを折り返す有効な方法が複数存在することが多い。

ヒント：メソッドやローカル変数の抽出により、行の折り返しなしに問題を解決できる場合がある。

#### 4.5.1 折り返す位置

行の折り返しの主要な指針は、**より高い構文レベル**で折り返すことを優先することである。

推奨：

```javascript
currentEstimate =
    calc(currentEstimate + x * currentEstimate) /
        2.0;
```

非推奨：

```javascript
currentEstimate = calc(currentEstimate + x *
    currentEstimate) / 2.0;
```

演算子の折り返しルール：

1. 行が演算子で折り返される場合、シンボルの後で折り返す（Javaのスタイルとは異なる）
   - ドット（`.`）には適用されない。ドットは実際には演算子ではない
2. メソッドまたはコンストラクタ名は、続く開き丸括弧（`(`）に付けたままにする
3. カンマ（`,`）は前のトークンに付けたままにする
4. `return` と返却値の間に改行を追加しない。コードの意味が変わるため
5. 型名を持つJSDocアノテーションは `{` の後で折り返す

#### 4.5.2 継続行は少なくとも+4スペースインデント

行を折り返す場合、最初の行の後の各行（各継続行）は、ブロックインデントのルールに該当する場合を除き、元の行から少なくとも+4でインデントする。

複数の継続行がある場合、+4を超えてインデントを変えてもよい。一般に、より深い構文レベルの継続行は4のより大きな倍数でインデントされ、構文的に並行する要素で始まる場合にのみ2つの行が同じインデントレベルを使用する。

### 4.6 空白

#### 4.6.1 垂直方向の空白

1つの空行が以下の場所に出現する：

1. クラスまたはオブジェクトリテラル内の連続するメソッドの間
   - 例外：2つの連続するプロパティ定義の間の空行は任意
2. メソッド本体内で、文のグループを論理的にまとめるために控えめに使用
3. クラスまたはオブジェクトリテラルの最初のメソッドの前または最後のメソッドの後（推奨も非推奨もしない）

連続する複数の空行は許容されるが、必須ではない。

#### 4.6.2 水平方向の空白

先頭の空白（インデント）は他で扱う。末尾の空白は禁止されている。

言語や他のスタイルルールで要求される場合、およびリテラル、コメント、JSDocを除き、単一の内部ASCIIスペースは以下の場所**のみ**に出現する：

1. `function` と `super` を除く予約語（`if`、`for`、`catch` など）と、その行で続く開き丸括弧（`(`）の間
2. 予約語（`else` や `catch` など）と、その行で先行する閉じ波括弧（`}`）の間
3. 開き波括弧（`{`）の前。ただし以下の2つの例外あり：
   - 関数の最初の引数またはリテラル配列の最初の要素であるオブジェクトリテラルの前（例：`foo({a: [{c: d}]})`）
   - テンプレート展開内（言語によって禁止されている。例：有効 `` `ab${1 + 2}cd` ``、無効 `` `xy$ {3}z` ``）
4. 二項演算子または三項演算子の両側
5. カンマ（`,`）またはセミコロン（`;`）の後。これらの文字の前にスペースは許可されない
6. オブジェクトリテラルのコロン（`:`）の後
7. 行末コメントを開始するダブルスラッシュ（`//`）の両側
8. ブロックコメント文字の開きの後と閉じの両側（例：`this.foo = /** @type {number} */ (bar)` や `function(/** string */ foo) {`）

#### 4.6.3 水平方向の整列：非推奨

水平方向の整列は許可されるが、SATELLITE Styleでは**一般的に非推奨**である。

```javascript
{
  tiny: 42, // これは良い
  longer: 435, // これも良い
};

{
  tiny:   42,  // 許可されるが、将来の編集で
  longer: 435, // 整列が崩れる可能性がある
};
```

#### 4.6.4 関数の引数

すべての関数の引数を関数名と同じ行に配置することを推奨する。80カラム制限を超える場合、引数は読みやすい方法で折り返さなければならない。

```javascript
// 引数は新しい行から始まり、4スペースインデント。推奨。
doSomething(
    descriptiveArgumentOne, descriptiveArgumentTwo, descriptiveArgumentThree) {
  // …
}

// 引数リストが長い場合、80で折り返す
doSomething(veryDescriptiveArgumentNumberOne, veryDescriptiveArgumentTwo,
    tableModelEventHandlerProxy, artichokeDescriptorAdapterIterator) {
  // …
}

// 4スペース、引数ごとに1行
doSomething(
    veryDescriptiveArgumentNumberOne,
    veryDescriptiveArgumentTwo,
    tableModelEventHandlerProxy,
    artichokeDescriptorAdapterIterator) {
  // …
}
```

### 4.7 グルーピング括弧：推奨

任意のグルーピング括弧は、著者とレビュアーの両方が、括弧なしでコードが誤解される合理的な可能性がなく、括弧があってもコードが読みやすくならないと合意した場合にのみ省略される。

`delete`、`typeof`、`void`、`return`、`throw`、`case`、`in`、`of`、`yield` に続く式全体の周りに不要な括弧を使用しないこと。

型キャストには括弧が必要である：`/** @type {!Foo} */ (foo)`

### 4.8 コメント

このセクションは実装コメントを扱う。JSDocは別途扱われる。

#### 4.8.1 ブロックコメントスタイル

ブロックコメントは周囲のコードと同じレベルにインデントする。`/* … */` または `//` スタイルのいずれでもよい。複数行の `/* … */` コメントでは、後続の行は前の行の `*` と揃えた `*` で始めなければならない。

```javascript
/*
 * これは
 * 問題ない。
 */

// これも
// 問題ない。

/* これも問題ない。 */
```

実装コメントにJSDoc（`/** … */`）を使用しないこと。

#### 4.8.2 パラメータ名コメント

値とメソッド名が意味を十分に伝えず、メソッドをより明確にリファクタリングすることが不可能な場合、「パラメータ名」コメントを使用すべきである。推奨されるフォーマットは値の前に `=` を付ける：

```javascript
someFunction(obviousParam, /* shouldRender= */ true, /* name= */ 'hello');
```

## 5 言語機能

### 5.1 ローカル変数宣言

#### 5.1.1 `const` と `let` を使用する

すべてのローカル変数は `const` または `let` のいずれかで宣言する。変数を再代入する必要がない限り、デフォルトで `const` を使用すること。`var` キーワードは使用してはならない。

#### 5.1.2 宣言ごとに1つの変数

すべてのローカル変数宣言は1つの変数のみを宣言する。`let a = 1, b = 2;` のような宣言は使用しない。

#### 5.1.3 必要なときに宣言し、できるだけ早く初期化

ローカル変数は、含むブロックの先頭で習慣的に宣言するのではなく、最初に使用される場所の近くで宣言し、スコープを最小化し、できるだけ早く初期化する。

#### 5.1.4 必要に応じて型を宣言する

JSDoc型アノテーションは宣言の上の行に追加するか、他のJSDocがない場合は変数名の前にインラインで追加してもよい。

```javascript
const /** !Array<number> */ data = [];

/**
 * 何らかの説明。
 * @type {!Array<number>}
 */
const data = [];
```

### 5.2 配列リテラル

#### 5.2.1 末尾カンマを使用する

最後の要素と閉じ括弧の間に改行がある場合は、末尾カンマを含める。

```javascript
const values = [
  'first value',
  'second value',
];
```

#### 5.2.2 可変長引数の `Array` コンストラクタを使用しない

コンストラクタは引数が追加または削除されるとエラーが発生しやすい。代わりにリテラルを使用すること。

非推奨：

```javascript
const a1 = new Array(x1, x2, x3);
const a2 = new Array(x1, x2);
const a3 = new Array(x1);
const a4 = new Array();
```

代わりに：

```javascript
const a1 = [x1, x2, x3];
const a2 = [x1, x2];
const a3 = [x1];
const a4 = [];
```

指定された長さの配列を `new Array(length)` で明示的に確保することは、適切な場合に許可される。

#### 5.2.3 非数値プロパティ

配列に（`length` 以外の）非数値プロパティを定義または使用しないこと。代わりに `Map`（または `Object`）を使用すること。

#### 5.2.4 分割代入

配列リテラルは代入の左辺で分割代入に使用してもよい。最後のrest要素を含めてもよい（`...` と変数名の間にスペースなし）。未使用の要素は省略すべきである。

```javascript
const [a, b, c, ...rest] = generateResults();
let [, b,, d] = someArray;
```

分割代入は関数パラメータにも使用できる。分割代入された配列パラメータが任意の場合は常にデフォルト値として `[]` を指定し、左辺にデフォルト値を提供すること：

```javascript
/** @param {!Array<number>=} param1 */
function optionalDestructuring([a = 4, b = 2] = []) { … };
```

非推奨：

```javascript
function badDestructuring([a, b] = [4, 2]) { … };
```

#### 5.2.5 スプレッド演算子

配列リテラルにはスプレッド演算子（`...`）を含めて、1つ以上の他のイテラブルから要素を展開してもよい。スプレッド演算子は `Array.prototype` のより煩雑な構造よりも推奨される。`...` の後にスペースは入れない。

```javascript
[...foo]   // Array.prototype.slice.call(foo) より推奨
[...foo, ...bar]   // foo.concat(bar) より推奨
```

### 5.3 オブジェクトリテラル

#### 5.3.1 末尾カンマを使用する

最後のプロパティと閉じ波括弧の間に改行がある場合は、末尾カンマを含める。

#### 5.3.2 `Object` コンストラクタを使用しない

`Object` には `Array` と同じ問題はないが、一貫性のために同様に禁止されている。代わりにオブジェクトリテラル（`{}` または `{a: 0, b: 1, c: 2}`）を使用すること。

#### 5.3.3 クォートありとなしのキーを混在させない

オブジェクトリテラルは構造体（クォートなしのキーやシンボル）またはディクショナリ（クォートありまたは計算されたキー）のいずれかを表す。単一のオブジェクトリテラル内でこれらのキータイプを混在させないこと。

非推奨：

```javascript
{
  width: 42, // 構造体スタイルのクォートなしキー
  'maxWidth': 43, // ディクショナリスタイルのクォートありキー
}
```

#### 5.3.4 計算プロパティ名

計算プロパティ名（例：`{['key' + foo()]: 42}`）は許可され、ディクショナリスタイル（クォートあり）のキーとみなされる。ただし、計算プロパティがシンボル（例：`[Symbol.iterator]`）の場合は例外である。

#### 5.3.5 メソッドの短縮構文

メソッドはオブジェクトリテラルでメソッドの短縮構文（`{method() {… }}`）を使用して定義できる。

```javascript
return {
  stuff: 'candy',
  method() {
    return this.stuff;  // 'candy' を返す
  },
};
```

注：メソッドの短縮構文または `function` 内の `this` はオブジェクトリテラル自体を参照するが、アロー関数内の `this` はオブジェクトリテラルの外側のスコープを参照する。

#### 5.3.6 プロパティの短縮構文

オブジェクトリテラルでプロパティの短縮構文を使用してもよい。

```javascript
const foo = 1;
const bar = 2;
const obj = {
  foo,
  bar,
  method() { return this.foo + this.bar; },
};
assertEquals(3, obj.method());
```

#### 5.3.7 分割代入

オブジェクトの分割代入パターンは、代入の左辺で値を展開するために使用してもよい。

分割代入されたオブジェクトは関数パラメータとしても使用できるが、できるだけシンプルに保つべきである。パラメータの分割代入ではネストの深いレベルや計算プロパティは使用しないこと。デフォルト値は左辺で指定する（`{str = 'some default'} = {}` とする。`{str} = {str: 'some default'}` としない）。

```javascript
/**
 * @param {string} ordinary
 * @param {{num: (number|undefined), str: (string|undefined)}=} param1
 *     num: 何かを行う回数。
 *     str: 処理する文字列。
 */
function destructured(ordinary, {num, str = 'some default'} = {}) {}
```

#### 5.3.8 列挙型

列挙型は `@enum` アノテーションをオブジェクトリテラルに追加して定義する。列挙型はモジュールローカルであるか、`exports` に直接割り当てなければならない。

定義後に列挙型にプロパティを追加してはならない。列挙型は定数でなければならない。すべての列挙値は文字列リテラルまたは数値のいずれかでなければならない。

```javascript
/**
 * サポートされる温度スケール。
 * @enum {string}
 */
const TemperatureScale = {
  CELSIUS: 'celsius',
  FAHRENHEIT: 'fahrenheit',
};
```

### 5.4 クラス

#### 5.4.1 コンストラクタ

コンストラクタは任意である。サブクラスのコンストラクタは、フィールドの設定や `this` へのアクセスの前に `super()` を呼び出さなければならない。インターフェイスはコンストラクタ内でメソッド以外のプロパティを宣言すべきである。

#### 5.4.2 フィールド

具象オブジェクトのすべてのフィールド（メソッド以外のすべてのプロパティ）をコンストラクタ内で定義すること。再代入されないフィールドには `@const` を付ける。非公開フィールドには適切な可視性アノテーション（`@private`、`@protected`、`@package`）を付ける。`@private` フィールドの名前は任意でアンダースコアで終了してもよい。

```javascript
class Foo {
  constructor() {
    /** @private @const {!Bar} */
    this.bar_ = computeBar();

    /** @protected @const {!Baz} */
    this.baz = computeBaz();
  }
}
```

#### 5.4.3 計算プロパティ

計算プロパティは、プロパティがシンボルの場合にのみクラスで使用してもよい。論理的にイテラブルなクラスには `[Symbol.iterator]` メソッドを定義すべきである。

#### 5.4.4 静的メソッド

可読性を妨げない場合、プライベート静的メソッドよりもモジュールローカル関数を優先すること。

静的メソッドの動的ディスパッチに依存するコードは書かないこと。静的メソッドはベースクラス自体でのみ呼び出すべきである。メソッドを定義していないサブクラスで静的メソッドを呼び出してはならない。静的メソッド内で `this` にアクセスしてはならない。

#### 5.4.5 旧スタイルのクラス宣言

ES6クラスが推奨されるが、ES6クラスが実行不可能な場合がある（例：既存のサブクラスがES6クラス構文に即座に変更できない場合）。

#### 5.4.6 `prototype` を直接操作しない

`class` キーワードは `prototype` プロパティの定義よりも明確で読みやすいクラス定義を可能にする。通常の実装コードではこれらのオブジェクトを操作する必要はない。ミックスインや組み込みオブジェクトのプロトタイプの変更は明示的に禁止されている。

例外：フレームワークコード（PolymerやAngularなど）は `prototype` を使用する必要がある場合がある。

#### 5.4.7 ゲッターとセッター

JavaScriptのゲッターおよびセッタープロパティを使用しないこと。潜在的に驚きがあり、推論が困難であり、コンパイラのサポートが限られている。代わりに通常のメソッドを提供すること。

例外：データバインディングフレームワーク（AngularやPolymerなど）や、調整できない外部APIとの互換性のために避けられない場合がある。その場合のみ、`get` および `set` 短縮メソッドキーワードで定義されたゲッターとセッターを注意して使用してもよい。ゲッターは観測可能な状態を変更**してはならない**。

非推奨：

```javascript
class Foo {
  get next() { return this.nextId++; }
}
```

#### 5.4.8 toStringのオーバーライド

`toString` メソッドはオーバーライドしてもよいが、常に成功しなければならず、目に見える副作用があってはならない。

#### 5.4.9 インターフェイス

インターフェイスは `@interface` または `@record` で宣言してもよい。`@record` で宣言されたインターフェイスは、クラスまたはオブジェクトリテラルによって明示的に（`@implements` を介して）または暗黙的に実装できる。

#### 5.4.10 抽象クラス

適切な場合に抽象クラスを使用すること。抽象クラスとメソッドは `@abstract` で注釈しなければならない。`goog.abstractMethod` は使用しないこと。

#### 5.4.11 静的コンテナクラスを作成しない

名前空間のために静的メソッドやプロパティのみを持つコンテナクラスを使用しないこと。代わりに個別の定数と関数をエクスポートすること。

#### 5.4.12 ネストされた名前空間を定義しない

別のモジュールローカル名にネストされた型（クラス、typedef、列挙型、インターフェイスなど）を定義しないこと。

### 5.5 関数

#### 5.5.1 トップレベル関数

トップレベル関数は `exports` オブジェクトに直接定義するか、ローカルで宣言して任意でエクスポートしてもよい。

#### 5.5.2 ネストされた関数とクロージャ

関数はネストされた関数定義を含んでもよい。関数に名前を付けることが有用な場合、ローカルの `const` に割り当てるべきである。

#### 5.5.3 アロー関数

アロー関数は簡潔な関数構文を提供し、ネストされた関数の `this` のスコーピングを簡素化する。ネストされた関数には `function` キーワードよりもアロー関数を優先すること。

`f.bind(this)`、`goog.bind(f, this)`、`const self = this` のような他の `this` スコーピングアプローチよりもアロー関数を優先すること。

右辺にはデフォルトでブロック文が含まれる。本体が暗黙的に返す単一の式でもよい。プログラムロジックが値を返す必要がある場合、または `void` 演算子が単一の関数呼び出しの前にある場合。

```javascript
/**
 * @param {number} numParam 加算する数値。
 * @param {string} strParam 文字列である別の数値。
 * @return {number} 2つのパラメータの合計。
 */
const moduleLocalFunc = (numParam, strParam) => numParam + Number(strParam);

// プログラムロジックが値を返す必要がないため、`void` を使用した単一式構文
getValue((result) => void alert(`Got ${result}`));
```

非推奨：

```javascript
/**
 * プログラムロジックが値を返す必要がなく、`void` 演算子が欠けているため、
 * この単一式本体の使用は不正である。
 */
const moduleLocalFunc = () => anotherFunction();
```

#### 5.5.4 ジェネレータ

ジェネレータは多くの有用な抽象化を可能にし、必要に応じて使用してもよい。

ジェネレータ関数を定義する場合、`*` を `function` キーワードに付け、関数名からはスペースで区切る。委譲yieldを使用する場合、`*` を `yield` キーワードに付ける。

```javascript
/** @return {!Iterator<number>} */
function* gen1() {
  yield 42;
}

/** @return {!Iterator<number>} */
const gen2 = function*() {
  yield* gen1();
}

class SomeClass {
  /** @return {!Iterator<number>} */
  * gen() {
    yield 42;
  }
}
```

#### 5.5.5 パラメータと返却値の型

関数のパラメータと返却値の型は通常JSDocアノテーションで文書化すべきである。

##### 5.5.5.1 デフォルトパラメータ

任意パラメータはパラメータリストで等号演算子を使用して許可される。任意パラメータは等号の両側にスペースを含め、必須パラメータと同じように命名し（`opt_` プレフィックスを付けない）、JSDoc型で `=` サフィックスを使用し、必須パラメータの後に来て、観測可能な副作用を生じる初期化子を使用しないこと。具象関数のすべての任意パラメータはデフォルト値を持たなければならない（その値が `undefined` であっても）。抽象メソッドとインターフェイスメソッドはデフォルトパラメータ値を省略しなければならない。

```javascript
/**
 * @param {string} required このパラメータは常に必要。
 * @param {string=} optional このパラメータは省略可能。
 * @param {!Node=} node 別の任意パラメータ。
 */
function maybeDoSomething(required, optional = '', node = undefined) {}
```

デフォルトパラメータは控えめに使用すること。自然な順序のない多数の任意パラメータがある場合は、分割代入を使用して読みやすいAPIを作成することを推奨する。

##### 5.5.5.2 残余パラメータ

`arguments` にアクセスする代わりに残余パラメータを使用すること。残余パラメータはJSDocで `...` プレフィックスで型付けする。残余パラメータはリストの最後のパラメータでなければならない。`...` とパラメータ名の間にスペースは入れない。残余パラメータに `var_args` と命名しないこと。

```javascript
/**
 * @param {!Array<string>} array 通常のパラメータ。
 * @param {...number} numbers 残りの引数はすべて数値。
 */
function variadic(array, ...numbers) {}
```

#### 5.5.6 ジェネリクス

必要に応じて、関数またはメソッド定義の上のJSDocで `@template TYPE` を使用してジェネリック関数およびメソッドを宣言すること。

#### 5.5.7 スプレッド演算子

関数呼び出しでスプレッド演算子（`...`）を使用してもよい。配列またはイテラブルが可変引数関数の複数パラメータに展開される場合、`Function.prototype.apply` よりもスプレッド演算子を優先すること。

```javascript
function myFunction(...elements) {}
myFunction(...array, ...iterable, ...generator());
```

### 5.6 文字列リテラル

#### 5.6.1 シングルクォートを使用する

通常の文字列リテラルはダブルクォート（`"`）ではなくシングルクォート（`'`）で区切る。

通常の文字列リテラルは複数行にまたがってはならない。

#### 5.6.2 テンプレートリテラル

複雑な文字列連結（特に複数の文字列リテラルが関わる場合）にはテンプレートリテラル（`` ` `` で区切る）を使用すること。テンプレートリテラルは複数行にまたがってもよい。

```javascript
function arithmetic(a, b) {
  return `Here is a table of arithmetic operations:
${a} + ${b} = ${a + b}
${a} - ${b} = ${a - b}
${a} * ${b} = ${a * b}
${a} / ${b} = ${a / b}`;
}
```

#### 5.6.3 行の継続を使用しない

通常の文字列リテラルでもテンプレート文字列リテラルでも、行の継続（文字列リテラルの内部でバックスラッシュで行を終了する）を使用しないこと。

非推奨：

```javascript
const longString = 'This is a very long string that far exceeds the 80 \
    column limit. It unfortunately contains long stretches of spaces due \
    to how the continued lines are indented.';
```

代わりに：

```javascript
const longString = 'This is a very long string that far exceeds the 80 ' +
    'column limit. It does not contain long stretches of spaces since ' +
    'the concatenated strings are cleaner.';
```

### 5.7 数値リテラル

数値は10進数、16進数、8進数、または2進数で指定してもよい。16進数、8進数、2進数にはそれぞれ小文字の `0x`、`0o`、`0b` プレフィックスを使用すること。`x`、`o`、`b` が直後に続かない限り、先頭のゼロを含めてはならない。

### 5.8 制御構造

#### 5.8.1 forループ

ES6では3種類の `for` ループがある。すべて使用してよいが、可能な場合は `for`-`of` ループを優先すべきである。

`for`-`in` ループはディクショナリスタイルのオブジェクトにのみ使用でき、配列の反復に使用してはならない。`for`-`in` ループでは `Object.prototype.hasOwnProperty` を使用して不要なプロトタイププロパティを除外すべきである。可能な場合は `for`-`of` と `Object.keys` を `for`-`in` よりも優先すること。

#### 5.8.2 例外

例外は言語の重要な部分であり、例外的なケースが発生するたびに使用すべきである。常に `Error` またはそのサブクラスをスローすること。文字列リテラルやその他のオブジェクトをスローしてはならない。`Error` を構築する際は常に `new` を使用すること。

カスタム例外は、関数から追加のエラー情報を伝達する優れた方法を提供する。ネイティブの `Error` 型が不十分な場合に定義して使用すべきである。

アドホックなエラーハンドリングアプローチ（エラーコンテナ参照型の受け渡しや、エラープロパティを持つオブジェクトの返却など）よりも例外のスローを優先すること。

##### 5.8.2.1 空のcatchブロック

キャッチされた例外に対して何もしないことが正しいケースは非常にまれである。catchブロックで何のアクションも取らないことが本当に適切な場合、その理由をコメントで説明すること。

```javascript
try {
  return handleNumericResponse(response);
} catch (ok) {
  // 数値ではない。問題なし、続行する
}
return handleTextResponse(response);
```

非推奨：

```javascript
try {
  shouldFail();
  fail('expected an error');
} catch (expected) {
}
```

#### 5.8.3 switch文

##### 5.8.3.1 フォールスルー：コメント付き

switchブロック内の各文グループは、突然終了するか（`break`、`return`、またはスローされた例外により）、次の文グループに実行が続く可能性があることを示すコメントでマークされなければならない。フォールスルーの概念を伝えるコメントであれば十分である（通常 `// fall through`）。switchブロックの最後の文グループではこの特別なコメントは不要である。

```javascript
switch (input) {
  case 1:
  case 2:
    prepareOneOrTwo();
  // fall through
  case 3:
    handleOneTwoOrThree();
    break;
  default:
    handleLargeNumber(input);
}
```

##### 5.8.3.2 `default` ケースは存在すること

各switch文にはコードを含まない場合でも `default` 文グループを含めること。`default` 文グループは最後でなければならない。

### 5.9 this

`this` はクラスのコンストラクタとメソッド、クラスのコンストラクタとメソッド内で定義されたアロー関数、または直接囲んでいる関数のJSDocで明示的に `@this` が宣言されている関数内でのみ使用すること。

グローバルオブジェクト、`eval` のコンテキスト、イベントのターゲットを参照するため、または不必要に `call()` や `apply()` された関数のために `this` を使用してはならない。

### 5.10 等価チェック

以下に記載されるケースを除き、同一性演算子（`===`/`!==`）を使用すること。

#### 5.10.1 型強制が望ましい例外

`null` と `undefined` の両方をキャッチする場合：

```javascript
if (someObjectOrPrimitive == null) {
  // nullのチェックはオブジェクトとプリミティブの両方で
  // nullとundefinedの両方をキャッチするが、
  // 0や空文字列のような他のfalsy値はキャッチしない。
}
```

### 5.11 禁止される機能

#### 5.11.1 with

`with` キーワードを使用しないこと。コードの理解を困難にし、ES5以降のstrictモードで禁止されている。

#### 5.11.2 動的コード評価

`eval` または `Function(...string)` コンストラクタを使用しないこと（コードローダーを除く）。これらの機能は潜在的に危険であり、CSP環境では動作しない。

#### 5.11.3 自動セミコロン挿入

常に文をセミコロンで終了すること（上記のように関数およびクラス宣言を除く）。

#### 5.11.4 非標準機能

非標準機能を使用しないこと。これには削除された古い機能、まだ標準化されていない新機能、または一部のブラウザでのみ実装されているプロプライエタリ機能が含まれる。現在のECMA-262またはWHATWG標準で定義されている機能のみを使用すること。

#### 5.11.5 プリミティブ型のラッパーオブジェクト

プリミティブオブジェクトラッパー（`Boolean`、`Number`、`String`、`Symbol`）に対して `new` を使用してはならず、型アノテーションに含めてもならない。

非推奨：

```javascript
const /** Boolean */ x = new Boolean(false);
if (x) alert(typeof x);  // 'object' を警告する — 想定外の動作
```

ラッパーは型強制（`+` の使用や空文字列の連結よりも推奨される）やシンボル作成のために関数として呼び出してもよい。

```javascript
const /** boolean */ x = Boolean(0);
if (!x) alert(typeof x);  // 期待通り 'boolean' を警告する
```

#### 5.11.6 組み込みオブジェクトの変更

組み込み型を変更してはならない（コンストラクタやプロトタイプにメソッドを追加するなど）。これを行うライブラリに依存することも避けること。

#### 5.11.7 コンストラクタ呼び出し時の `()` 省略

`new` 文でコンストラクタを呼び出す際、括弧 `()` を省略してはならない。

非推奨：

```javascript
new Foo;
```

代わりに：

```javascript
new Foo();
```

## 6 命名

### 6.1 すべての識別子に共通するルール

識別子はASCII文字と数字のみを使用し、まれにアンダースコアとドル記号を使用する（フレームワーク（Angularなど）で必要な場合）。

名前は合理的な範囲でできるだけ説明的であるべきである。水平スペースの節約よりも、即座の理解可能性が重要である。プロジェクト外の人に馴染みのない曖昧な略語を避け、内部の文字を削除して略語にしないこと。

適切な例：`errorCount`、`dnsConnectionIndex`、`referrerUrl`、`customerId`

不適切な例：`n`、`nErr`、`nCompConns`、`wgcConnections`、`pcReader`、`cstmrId`、`kSecondsPerDay`

例外：10行以下のスコープの変数（エクスポートされないAPI引数を含む）は1文字の名前を使用してもよい。

### 6.2 識別子の種類別ルール

#### 6.2.1 パッケージ名

すべて小文字の `lowerCamelCase`（例：`my.exampleCode.deepSpace`、`my.examplecode.deepspace` ではない）。

#### 6.2.2 クラス名

クラス、インターフェイス、レコード、typedefは `UpperCamelCase` を使用する。エクスポートされないクラスは `@private` マーキングなしでローカルに留まる。

型名は通常名詞または名詞句である：`Request`、`ImmutableView`、`VisibilityMode`。インターフェイス名は形容詞でもよい：`Readable`。

#### 6.2.3 メソッド名

`lowerCamelCase` で記述する。プライベートメソッドは任意で末尾アンダースコアを持ってもよい。

名前は通常動詞または動詞句である：`sendMessage`、`stop_`。ゲッターとセッターのメソッド（使用する場合）は `getFoo()`、ブール値の場合は `isFoo()`、`hasFoo()`、または `setFoo(value)` のパターンに従う。

JsUnitテストメソッド名ではアンダースコアが論理コンポーネントを区切る。一般的なパターン：`test<テスト対象メソッド>_<状態>_<期待される結果>`（例：`testPop_emptyStack_throws`）。

#### 6.2.4 列挙型名

`UpperCamelCase`、通常は単数名詞。個々の項目は `CONSTANT_CASE` を使用する。

#### 6.2.5 定数名

`CONSTANT_CASE`：すべて大文字でアンダースコアで単語を区切る。

##### 6.2.5.1 「定数」の定義

すべての定数は `@const` 静的プロパティまたはモジュールローカルの `const` 宣言である。定数ケースを選択する前に、そのフィールドが*深く不変な*定数を表すかどうかを確認すること。観測可能な状態が変化する場合、それは定数ではない。

定数の例：

```javascript
const NUMBER = 5;
/** @const */ exports.NAMES = goog.debug.freeze(['Ed', 'Ann']);
/** @enum */ exports.SomeEnum = { ENUM_CONSTANT: 'value' };
```

非定数の例：

```javascript
let letVariable = 'non-const';

class MyClass {
  constructor() { /** @const {string} */ this.nonStatic = 'non-static'; }
};
MyClass.staticButMutable = 'not @const, can be reassigned';

const /** Set<string> */ mutableCollection = new Set();
const logger = log.getLogger('loggers.are.not.immutable');
```

##### 6.2.5.2 ローカルエイリアス

完全修飾名よりも可読性が向上する場合、ローカルエイリアスを使用すること。`goog.require` と同じルールに従い、エイリアス名の最後の部分を維持すること。エイリアスは関数内で使用でき、`const` でなければならない。

```javascript
const staticHelper = importedNamespace.staticHelper;
const CONSTANT_NAME = ImportedClass.CONSTANT_NAME;
const {assert, assertInstanceof} = asserts;
```

#### 6.2.6 非定数フィールド名

`lowerCamelCase` で記述し、プライベートフィールドには任意で末尾アンダースコアを付ける。

通常名詞または名詞句：`computedValues`、`index_`。

#### 6.2.7 パラメータ名

`lowerCamelCase` で記述する。パラメータがコンストラクタを期待する場合も同様。

公開メソッドでは1文字のパラメータ名を使用すべきでない。

例外：サードパーティフレームワークで必要な場合、パラメータ名は `$` で始まってもよい。この例外はローカル変数やプロパティには適用されない。

#### 6.2.8 ローカル変数名

`lowerCamelCase` で記述する。ただし、モジュールローカルのトップレベル定数は例外。変数がコンストラクタを保持する場合でも `lowerCamelCase` を使用する。

#### 6.2.9 テンプレートパラメータ名

簡潔な単一単語または単一文字の識別子をすべて大文字で使用する：`TYPE`、`THIS`。

#### 6.2.10 モジュールローカル名

エクスポートされないモジュールローカル名は暗黙的にプライベートである。`@private` でマークしない。これはクラス、関数、変数、定数、列挙型、その他のモジュールローカル識別子に適用される。

### 6.3 キャメルケース：定義

英語のフレーズをキャメルケースに変換する場合（特に頭字語やIPv6、iOSのような特殊な構造の場合）：

1. プレーンASCIIに変換し、アポストロフィを削除する（例：Müller's → Muellers）
2. 単語に分割する（スペースやハイフンなどの句読点で分割）
   - 一般的な用法でキャメルケースの慣例がある場合、構成部分に分割する（AdWords → ad words）。例外：iOSは慣例に反する
3. すべて小文字にし、次の最初の文字のみ大文字にする：
   - `UpperCamelCase` の場合は各単語
   - `lowerCamelCase` の場合は最初の単語を除く各単語
4. すべての単語を1つの識別子に結合する

| 散文形式 | 正しい | 不正 |
|---|---|---|
| XML HTTP request | `xmlHttpRequest` | `XMLHTTPRequest` |
| new customer ID | `newCustomerId` | `newCustomerID` |
| inner stopwatch | `innerStopwatch` | `innerStopWatch` |
| supports IPv6 on iOS? | `supportsIpv6OnIos` | `supportsIPv6OnIOS` |
| YouTube importer | `youTubeImporter` | `youtubeImporter` |

## 7 JSDoc

JSDocはすべてのクラス、フィールド、メソッドに使用される。

### 7.1 一般形式

基本的なJSDocフォーマット：

```javascript
/**
 * 複数行のJSDocテキストはここに記述し、
 * 通常通り折り返す。
 * @param {number} arg 何かを行う数値。
 */
function doSomething(arg) { … }
```

単一行の例：

```javascript
/** @const @private {!Foo} 短いJSDoc。 */
this.foo_ = foo;
```

複数行コメントが溢れる場合は `/**` と `*/` をそれぞれ独自の行に置かなければならない。

ツールはコード検証と最適化のためにJSDocコメントからメタデータを抽出するため、コメントは整形式でなければならない。

### 7.2 Markdown

JSDocはMarkdownで記述され、必要に応じてHTMLも許可される。

### 7.3 JSDocタグ

SATELLITE Styleではjsm JSDocタグのサブセットが許可される。ほとんどのタグは独自の行を占め、行の先頭にタグを配置しなければならない。

非推奨：

```javascript
/**
 * "param" タグは独自の行を占め、結合してはならない。
 * @param {number} left @param {number} right
 */
function add(left, right) { ... }
```

追加データのない単純なタグ（`@private`、`@const`、`@final`、`@export`）は同じ行に結合してもよい：

```javascript
/**
 * 複雑なアノテーションは独自の行に配置する。
 * 複数の単純なタグは1行に結合してもよい。
 * @export @final
 * @implements {Iterable<TYPE>}
 * @template TYPE
 */
class MyClass {
  /**
   * @param {!ObjType} obj あるオブジェクト。
   * @param {number=} num 任意の数値。
   */
  constructor(obj, num = 42) {
    /** @private @const {!Array<!ObjType|number>} */
    this.data_ = [obj, num];
  }
}
```

### 7.4 行の折り返し

折り返されたブロックタグは4スペースインデントする。折り返された説明テキストは前の行に揃えてもよいが、この水平方向の整列は非推奨である：

```javascript
/**
 * 長いparam/return説明の行折り返しの例。
 * @param {string} foo 1行に収まらない説明を持つ
 *     パラメータ。
 * @return {number} 1行に収まらない説明を持つ
 *     返却値。
 */
exports.method = function(foo) {
  return 5;
};
```

### 7.5 トップレベル/ファイルレベルコメント

ファイルにはトップレベルのファイル概要を含めてもよい。著作権通知、著者情報、デフォルトの可視性レベルは任意である。ファイル概要は一般的に複数クラスファイルに推奨される。

```javascript
/**
 * @fileoverview ファイルの説明、その用途および
 * 依存関係に関する情報。
 * @package
 */
```

### 7.6 クラスコメント

クラス、インターフェイス、レコードは説明と、テンプレートパラメータ、実装インターフェイス、可視性、その他適切なタグで文書化しなければならない。`class` キーワードを使用する場合、ジェネリッククラスを拡張しない限り `@constructor` と `@extends` アノテーションは使用しない。

```javascript
/**
 * 素晴らしいことをするより高機能なイベントターゲット。
 * @implements {Iterable<string>}
 */
class MyFancyTarget extends EventTarget {
  /**
   * @param {string} arg1 これをより面白くする引数。
   * @param {!Array<number>} arg2 処理する数値のリスト。
   */
  constructor(arg1, arg2) {
    // ...
  }
};
```

### 7.7 列挙型とtypedefのコメント

すべての列挙型とtypedefは前の行に適切なJSDocタグ（`@typedef` または `@enum`）で文書化しなければならない。公開の列挙型とtypedefには説明も必要である。

```javascript
/**
 * 便利な型共用体。頻繁に再利用される。
 * @typedef {!FruitType|!FruitTypeEnum}
 */
let CoolUnionType;

/**
 * フルーツの種類。
 * @enum {string}
 */
const FruitTypeEnum = {
  /** この種類はとても酸っぱい。 */
  SOUR: 'sour',
  /** あまり酸っぱくない種類。 */
  SWEET: 'sweet',
};
```

typedefは短いレコード型の定義、共用体・複雑な関数・ジェネリック型のエイリアスに便利である。多くのフィールドを持つレコード型にはtypedefを避け、`@record` を優先すべきである。

### 7.8 メソッドと関数のコメント

メソッドと名前付き関数では、同じシグネチャの `@override` であっても、パラメータと返却値の型を文書化しなければならない。返却値の型は、関数に非空の `return` 文がない場合に省略してもよい。

メソッド、パラメータ、返却値の説明（型ではない）は、メソッドのJSDocやシグネチャから明らかな場合に省略してもよい。

メソッドの説明は、メソッドが何をするかを記述する動詞句で始める。三人称で記述し、「This method...」が先行するかのように書く。

スーパークラスのメソッドをオーバーライドする場合、`@override` アノテーションを含めなければならない。

```javascript
/** 何かを行うクラス。 */
class SomeClass extends SomeBaseClass {
  /**
   * MyClassのインスタンスを操作し、何かを返す。
   * @param {!MyClass} obj 複数行にわたる詳細な説明が
   *     何らかの理由で必要なオブジェクト。
   * @param {!OtherClass} obviousOtherClass
   * @return {boolean} 何かが発生したかどうか。
   */
  someMethod(obj, obviousOtherClass) { ... }

  /**
   * @param {string} param
   * @return {string}
   * @override
   */
  overriddenMethod(param) { ... }
}
```

### 7.9 プロパティコメント

プロパティの型を文書化しなければならない。プライベートプロパティの説明は、名前と型が十分な文書を提供する場合に省略してもよい。

```javascript
/** 自分のクラス。 */
class MyClass {
  /** @param {string=} someString */
  constructor(someString = 'default string') {
    /** @private @const {string} */
    this.someString_ = someString;

    /** @private @const {!OtherType} */
    this.someOtherThing_ = functionThatReturnsAThing();

    /**
     * ペインあたりの最大数。
     * @type {number}
     */
    this.someProperty = 4;
  }
}

/**
 * 諦める前の試行回数。
 * @const {number}
 */
MyClass.RETRY_COUNT = 33;
```

### 7.10 型アノテーション

型アノテーションは `@param`、`@return`、`@this`、`@type` タグに出現し、任意で `@const`、`@export`、可視性タグに出現する。JSDocタグに添付される型アノテーションは常に波括弧で囲まなければならない。

#### 7.10.1 Null許容性

型システムは修飾子 `!`（非null）と `?`（null許容）を定義する。これらの修飾子は型の前に置かなければならない。

1. **プリミティブとリテラル**（`string`、`number`、`boolean`、`symbol`、`undefined`、`null`、`{function(...): ...}`、`{{foo: string...}}`）：デフォルトで非null。null許容にするには `?` を使用し、冗長な `!` は省略する。

2. **参照型**（一般的に `UpperCamelCase`、`some.namespace.ReferenceType` を含む）：使用箇所での曖昧さを防ぐため、常に明示的に `?` と `!` 修飾子を使用する。

不適切：

```javascript
const /** MyObject */ myObject = null;
const /** !number */ someNum = 5;
const /** number? */ someNullableNum = null;
const /** !{foo: string, bar: number} */ record = ...;
const /** MyTypeDef */ def = ...;
```

適切：

```javascript
const /** ?MyObject */ myObject = null;
const /** number */ someNum = 5;
const /** ?number */ someNullableNum = null;
const /** {foo: string, bar: number} */ record = ...;
const /** !MyTypeDef */ def = ...;
```

#### 7.10.2 型キャスト

コンパイラが式の型を正確に推論できない場合、型アノテーションコメントを追加し、式を括弧で囲むことで型を絞り込む。括弧は必須である。

```javascript
/** @type {number} */ (x)
```

#### 7.10.3 テンプレートパラメータ型

常にテンプレートパラメータを指定すること。これによりコンパイラがより良い型チェックを行い、読者がコードの動作を理解しやすくなる。

不適切：

```javascript
const /** !Object */ users = {};
const /** !Array */ books = [];
const /** !Promise */ response = ...;
```

適切：

```javascript
const /** !Object<string, !User> */ users = {};
const /** !Array<string> */ books = [];
const /** !Promise<!Response> */ response = ...;
```

#### 7.10.4 関数型式

**用語の注記**：*関数型式*は、アノテーション内に `function` キーワードを含む関数型の型アノテーションを指す。

関数定義が与えられている場合、関数型式を使用しないこと。パラメータと返却値の型を `@param` と `@return`、またはインラインアノテーションで指定すること。

関数型式が必要な場合の例は、`@typedef`、`@param`、`@return` 内や、関数定義で即座に初期化されない関数型の変数やプロパティである。

```javascript
/** @private {function(string): string} */
this.idGenerator_ = googFunctions.identity;
```

関数型式を使用する場合、常に返却値の型を明示的に指定すること。そうしないと、デフォルトの返却値の型は不明（`?`）になり、奇妙で予期しない動作を引き起こす。

不適切 — 型エラーだが警告なし：

```javascript
/** @param {function()} generateNumber */
function foo(generateNumber) {
  const /** number */ x = generateNumber();  // コンパイル時の型エラーなし
}

foo(() => 'clearly not a number');
```

適切：

```javascript
/**
 * @param {function(): *} inputFunction1 任意の型を返せる。
 * @param {function(): undefined} inputFunction2 明らかに何も返さない。
 * 注：`foo` 自体の返却値の型は安全に {undefined} と推論される。
 */
function foo(inputFunction1, inputFunction2) {...}
```

#### 7.10.5 空白

型アノテーション内では、各カンマまたはコロンの後に単一のスペースまたは改行が必要である。可読性を向上させるため、またはカラム制限を超えないようにするために追加の改行を挿入してもよい。他の空白は型アノテーション内で許可されない。

適切：

```javascript
/** @type {function(string): number} */

/** @type {{foo: number, bar: number}} */

/** @type {number|string} */

/** @type {!Object} */
```

不適切：

```javascript
// コロンの後にのみスペースを入れる
/** @type {function(string) : number} */

// コロンとカンマの後にスペースを入れる
/** @type {{foo:number,bar:number}} */

// 共用体型にスペースなし
/** @type {number | string} */
```

### 7.11 可視性アノテーション

可視性アノテーション（`@private`、`@package`、`@protected`）は、`@fileoverview` ブロック内、またはエクスポートされたシンボルやプロパティに指定してもよい。関数内またはモジュールのトップレベルのローカル変数には可視性を指定しないこと。`@private` 名は任意でアンダースコアで終了してもよい。

## 8 ポリシー

### 8.1 SATELLITE Styleで規定されていない問題：一貫性を保つこと

この仕様で決定的に解決されていないスタイルの問題については、同じファイル内の他のコードが既に行っていることに従うことを推奨する。それで解決しない場合は、同じパッケージ内の他のファイルを模倣することを検討すること。

### 8.2 コンパイラの警告

#### 8.2.1 標準的な警告セットを使用する

可能な限り、プロジェクトは `--warning_level=VERBOSE` を使用すべきである。

#### 8.2.2 警告への対処方法

何かをする前に、警告が何を伝えているかを正確に理解すること。警告が出る理由が確信できない場合は、助けを求めること。

警告を理解したら、以下の解決策を順番に試みること：

1. **まず、修正するか回避する。** 実際に警告に対処するか、状況を完全に回避する別の方法を見つけるよう強く試みること。
2. **そうでなければ、誤報かどうかを判断する。** 警告が無効であり、コードが実際に安全で正しいことが確信できる場合、読者にその事実を納得させるコメントを追加し、`@suppress` アノテーションを適用すること。
3. **そうでなければ、TODOコメントを残す。** これは**最後の手段**である。この場合、**警告を抑制しないこと。** 警告は適切に対処されるまで可視のままにすべきである。

#### 8.2.3 最も狭い合理的なスコープで警告を抑制する

警告は最も狭い合理的なスコープで抑制する。通常は単一のローカル変数や非常に小さなメソッドのスコープである。

```javascript
/** @suppress {uselessCode} 認識されない 'use asm' 宣言 */
function fn() {
  'use asm';
  return 0;
}
```

### 8.3 非推奨

非推奨のメソッド、クラス、インターフェイスには `@deprecated` アノテーションを付けること。非推奨コメントには、呼び出し箇所を修正するための簡潔で明確な指示を含めなければならない。

### 8.4 SATELLITE Styleでないコード

コードベースにはSATELLITE Styleに準拠していないファイルが時折見つかる場合がある。

#### 8.4.1 既存コードのリフォーマット

既存コードのスタイルを更新する場合、以下のガイドラインに従うこと。

- すべての既存コードを現在のスタイルガイドラインに合わせて変更する必要はない。ただし、ファイルに大幅な変更が加えられる場合は、そのファイルがSATELLITE Styleに準拠していることが期待される。
- 日和見的なスタイル修正がCLの焦点をぼやけさせないように注意すること。

#### 8.4.2 新しく追加されるコード：SATELLITE Styleを使用する

完全に新しいファイルは、同じパッケージ内の他のファイルのスタイル選択に関わらず、SATELLITE Styleを使用する。

SATELLITE Styleでないファイルに新しいコードを追加する場合、まず既存コードをリフォーマットすることが推奨される。

### 8.5 ローカルスタイルルール

チームやプロジェクトはこのドキュメントを超える追加のスタイルルールを採用してもよいが、クリーンアップの変更がこれらの追加ルールに従わない場合があることを受け入れなければならず、追加ルールの違反を理由にそのようなクリーンアップの変更をブロックしてはならない。

### 8.6 生成コード：ほぼ免除

ビルドプロセスによって生成されたソースコードはSATELLITE Styleに準拠する必要はない。ただし、手書きのソースコードから参照される生成された識別子は命名要件に従わなければならない。

## 9 付録

### 9.1 JSDocタグリファレンス

JSDocはJavaScriptで複数の目的で使用される。ドキュメント生成に加えて、ツールの制御にも使用される。最もよく知られているのはClosure Compilerの型アノテーションである。

#### 9.1.1 型アノテーションとその他のClosure Compilerアノテーション

Closure Compilerが使用するJSDocのドキュメントは「Annotating JavaScript for the Closure Compiler」および「Types in the Closure Type System」に記載されている。

#### 9.1.2 ドキュメンテーションアノテーション

上記に加えて、以下のタグは一般的であり、純粋なドキュメンテーション目的でさまざまなドキュメント生成ツール（JsDossierなど）により十分にサポートされている。

##### 9.1.2.1 `@author` または `@owner` — 非推奨

**非推奨。**

構文：`@author username@google.com (First Last)`

ファイルの著者またはテストのオーナーを文書化する。通常は `@fileoverview` コメント内でのみ使用される。

##### 9.1.2.2 `@bug`

構文：`@bug bugnumber`

```javascript
/** @bug 1234567 */
function testSomething() {
  // …
}
```

指定されたテスト関数がリグレッションテストするバグを示す。複数のバグはそれぞれ独自の `@bug` 行に記載すべきである。

##### 9.1.2.3 `@code` — 非推奨。使用しないこと

**非推奨。使用しないこと。代わりにMarkdownのバッククォートを使用すること。**

##### 9.1.2.4 `@desc`

構文：`@desc メッセージの説明`

##### 9.1.2.5 `@link`

構文：`{@link ...}`

生成されたドキュメント内でクロスリファレンスリンクを生成するために使用される。外部リンクには常にMarkdownのリンク構文を使用すること。

##### 9.1.2.6 `@see`

構文：`@see リンク`

別のクラスの関数またはメソッドへの参照ルックアップ。

##### 9.1.2.7 `@supported`

構文：`@supported 説明`

```javascript
/**
 * @fileoverview イベントマネージャ
 * ブラウザのイベントシステムへの抽象化インターフェイスを提供する。
 * @supported IE10+, Chrome, Safari
 */
```

fileoverviewでファイルがサポートするブラウザを示すために使用される。

#### 9.1.3 フレームワーク固有のアノテーション

以下のアノテーションは特定のフレームワークに固有である。

##### 9.1.3.1 Angular 1の `@ngInject`

##### 9.1.3.2 Polymerの `@polymerBehavior`

#### 9.1.4 標準的なClosure Compilerアノテーションに関する注記

以下のタグは以前は標準であったが、現在は非推奨である。

##### 9.1.4.1 `@expose` — 非推奨。使用しないこと

**非推奨。使用しないこと。代わりに `@export` や `@nocollapse` を使用すること。**

##### 9.1.4.2 `@inheritDoc` — 非推奨。使用しないこと

**非推奨。使用しないこと。代わりに `@override` を使用すること。**

### 9.2 よく誤解されるスタイルルール

SATELLITE StyleのJavaScriptに関して、あまり知られていないか、よく誤解される事実の一覧を以下に示す（以下はすべて正しい記述であり、神話のリストではない）：

- ソースファイルには著作権表示も `@author` クレジットも必須ではない（いずれも明示的に推奨もされていない）
- クラスのメンバーの順序を規定するハードなルールはない
- 空ブロックは通常 `{}` として簡潔に表現できる
- 行の折り返しの主要な指針は、より高い構文レベルで折り返すことを優先すること
- 非ASCII文字は文字列リテラル、コメント、JSDocで許可されており、同等のUnicodeエスケープよりもコードが読みやすくなる場合に推奨される

### 9.3 スタイル関連ツール

#### 9.3.1 Closure Compiler

型チェックとその他のチェック、最適化、その他の変換（ECMAScript 5へのコード低下など）を実行するプログラム。

#### 9.3.2 clang-format

JavaScriptのソースコードをSATELLITE Styleにリフォーマットするプログラム。`clang-format` の出力はスタイルガイドに準拠している。

`clang-format` は必須ではない。著者はその出力を変更してもよく、レビュアーもそのような変更を要求してもよい。

#### 9.3.3 Closure compiler linter

さまざまなミスやアンチパターンをチェックするプログラム。

#### 9.3.4 Conformanceフレームワーク

JS Conformance FrameworkはClosure Compilerの一部であるツールで、開発者が標準チェックに加えてコードベースに対して実行する追加チェックのセットを簡単に指定する手段を提供する。

### 9.4 レガシープラットフォームの例外

#### 9.4.1 概要

このセクションでは、最新のECMAScript構文がコード著者に利用できない場合に従うべき例外と追加ルールを説明する。推奨スタイルの例外は、最新のECMAScript構文が使用できない場合に必要であり、ここで概説される：

- `var` 宣言の使用が許可される
- `arguments` の使用が許可される
- デフォルト値のない任意パラメータが許可される

#### 9.4.2 varの使用

##### 9.4.2.1 `var` 宣言はブロックスコープではない

`var` 宣言は最も近い囲み関数、スクリプト、またはモジュールの先頭にスコープされ、予期しない動作を引き起こす可能性がある。

##### 9.4.2.2 最初の使用にできるだけ近くで変数を宣言する

`var` 宣言は囲み関数の先頭にスコープされるが、可読性のために最初の使用にできるだけ近くに `var` 宣言を配置すべきである。ただし、ブロックの外で参照される変数の `var` 宣言をブロック内に置かないこと。

##### 9.4.2.3 定数変数には `@const` を使用する

`const` キーワードが利用可能であれば使用するグローバル宣言では、代わりに `var` 宣言に `@const` をアノテーションすること。

#### 9.4.3 ブロックスコープの関数宣言を使用しない

以下のようにしてはならない：

```javascript
if (x) {
  function foo() {}
}
```

一貫した動作を得るために、代わりに関数式で初期化された `var` を使用してブロック内で関数を定義すること：

```javascript
if (x) {
  var foo = function() {};
}
```

#### 9.4.4 `goog.provide`/`goog.require` による依存関係管理

##### 9.4.4.1 概要

**警告：`goog.provide` による依存関係管理は非推奨である。** すべての新しいファイルは、古いファイルに `goog.provide` を使用しているプロジェクトでも、`goog.module` を使用すべきである。

- すべての `goog.provide` を最初に、`goog.require` を次に配置する。空行でprovideとrequireを区切る
- エントリをアルファベット順にソートする（大文字を先に）
- `goog.provide` と `goog.require` 文を折り返さない。必要であれば80カラムを超えてもよい
- トップレベルシンボルのみをprovideする

##### 9.4.4.2 `goog.scope` によるエイリアス

**警告：`goog.scope` は非推奨である。** 既存の `goog.scope` 使用があるプロジェクトでも、新しいファイルは `goog.scope` を使用すべきでない。

##### 9.4.4.3 `goog.forwardDeclare`

同じライブラリ内のファイル間の循環依存を解消するには、`goog.forwardDeclare` よりも `goog.requireType` を優先すること。

##### 9.4.4.4 `goog.module.get(name)`

`goog.provide` ファイルが `goog.module` ファイルに依存する場合、`goog.provide` ファイルは通常グローバル名でモジュールのエクスポートを参照できない。代わりに、モジュールを `goog.require()` することに加えて、`goog.module.get('module.name')` を呼び出してモジュールのエクスポートオブジェクトを取得しなければならない。

##### 9.4.4.5 `goog.module.declareLegacyNamespace()`

**警告：`goog.module.declareLegacyNamespace` は移行用途のみである。**

`goog.module.declareLegacyNamespace` は、JavaScriptファイルとその利用者を `goog.provide` から `goog.module` に移行する間のみ使用する。
