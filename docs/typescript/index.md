# SATELLITE TypeScript スタイルガイド

## はじめに

### 用語に関する注意

本ガイドでは RFC 2119 の用語を使用する。*must*（しなければならない）、*must not*（してはならない）、*should*（すべきである）、*should not*（すべきでない）、*may*（してもよい）は、その規格に従って定義される。*prefer*（推奨する）と *avoid*（避ける）は、それぞれ *should* と *should not* に対応する。

### ガイドに関する注意

すべての例は**非規範的**であり、規範的な文言を説明するためのものである。例における任意のフォーマット選択をルールとして強制してはならない。

---

## ソースファイルの基本

### ファイルエンコーディング: UTF-8

ソースファイルは **UTF-8** でエンコードされる。

#### 空白文字

行終端子以外で、ソースファイル内の空白として現れるのは ASCII 水平スペース文字（0x20）のみである。文字列リテラル内のその他すべての空白文字はエスケープしなければならない。

#### 特殊エスケープシーケンス

数値エスケープではなく、特殊エスケープシーケンス（`\'`、`\"`、`\\`、`\b`、`\f`、`\n`、`\r`、`\t`、`\v`）を使用すること。レガシーな8進エスケープは決して使用しないこと。

#### 非ASCII文字

実際の Unicode 文字（例: `∞`）を使用すること。非印刷文字には、説明コメント付きの16進エスケープまたは Unicode エスケープを使用すること。

```typescript
// コメントなしでも明確
const units = 'μs';

// バイトオーダーマーク
const output = '\ufeff' + content;
```

---

## ソースファイルの構造

ファイルは以下の要素を**この順序で**構成する:

1. 著作権情報（存在する場合）
2. `@fileoverview` を含む JSDoc（存在する場合）
3. import 文（存在する場合）
4. ファイルの実装

存在する各セクション間は**正確に1つの空行**で区切る。

### 著作権情報

ファイルの先頭に、ライセンスまたは著作権情報を JSDoc で追加すること。

### `@fileoverview` JSDoc

ファイルには、説明、用途、または依存関係の情報を提供するトップレベルの `@fileoverview` JSDoc を記述してもよい。折り返された行はインデントしない。

```typescript
/**
 * @fileoverview Description of file. Lorem ipsum dolor sit amet, consectetur
 * adipiscing elit, sed do eiusmod tempor incididunt.
 */
```

### import

ES6/TypeScript は4種類の import バリアントを提供する:

| import の種類 | 例 | 用途 |
|---|---|---|
| module | `import * as foo from '...';` | TypeScript の import |
| named（名前付き） | `import {SomeThing} from '...';` | TypeScript の import |
| default | `import SomeThing from '...';` | それを必要とする外部コード |
| side-effect（副作用） | `import '...';` | 読み込み時の副作用のためのライブラリ |

```typescript
// Good
import * as ng from '@angular/core';
import {Foo} from './foo';

// 必要な場合のみ
import Button from 'Button';

// 副作用のため
import 'jasmine';
import '@polymer/paper-button';
```

#### import パス

TypeScript コードは他の TypeScript コードを import するためにパスを使用**しなければならない**。パスは相対パス（`.` または `..`）またはベースディレクトリからのルートパスであってよい。

同じ論理プロジェクト内では、絶対 import（`path/to/foo`）よりも相対 import（`./foo`）を使用**すべきである**。明確さを維持するために親ステップ（`../../../`）を制限すること。

```typescript
import {Symbol1} from 'path/from/root';
import {Symbol2} from '../parent/file';
import {Symbol3} from './sibling';
```

#### namespace import と名前付き import

namespace import と名前付き import の両方が許容される。

頻繁に使用されるシンボルや明確な名前を持つシンボル（例: Jasmine の `describe`、`it`）には**名前付き import を推奨する**。必要に応じてエイリアスに `as` を使用すること。

大規模な API から多くのシンボルを使用する場合は **namespace import を推奨する**。これは `Model` や `Controller` のような一般的な名前の可読性を助ける。

```typescript
// Bad: 不必要な名前空間付けで冗長
import {Item as TableviewItem, Header as TableviewHeader, Row as TableviewRow,
  Model as TableviewModel, Renderer as TableviewRenderer} from './tableview';

let item: TableviewItem|undefined;
```

```typescript
// Better: 名前空間付けにモジュールを使用
import * as tableview from './tableview';

let item: tableview.Item|undefined;
```

```typescript
// Bad: モジュール名が可読性を改善しない
import * as testing from './testing';

testing.describe('foo', () => {
  testing.it('bar', () => {
    testing.expect(null).toBeNull();
  });
});
```

```typescript
// Better: 一般的な関数にローカル名を付ける
import {describe, it, expect} from './testing';

describe('foo', () => {
  it('bar', () => {
    expect(null).toBeNull();
  });
});
```

##### 特殊なケース: Apps JSPB Proto

Apps JSPB proto は、import 行が長くなっても名前付き import を使用**しなければならない**。これはビルドパフォーマンスとデッドコード除去を助け、より細粒度の依存関係を作成する。

```typescript
// Good
import {Foo, Bar} from './foo.proto';

function copyFooBar(foo: Foo, bar: Bar) {...}
```

#### import のリネーム

名前の衝突は namespace import またはエクスポートのリネームで修正**すべきである**。必要に応じて import をリネームしても**よい**（`import {SomeThing as SomeOtherThing}`）。

リネームが有用な場合:

1. 他の import されたシンボルとの衝突を避ける場合
2. import されたシンボル名が生成されたものである場合
3. 明確さを改善するために不明瞭な名前の import シンボルをリネームする場合（例: RxJS の `from` を `observableFrom` としてリネーム）

### export

すべてのコードで**名前付き export を使用する**:

```typescript
export class Foo { ... }
```

**default export を使用しないこと**。これにより統一された import パターンが保証される。

```typescript
// これはしない
export default class Foo { ... }
```

**理由**: default export は正規名を提供せず、メンテナンスを複雑にする割にメリットが少ない:

```typescript
import Foo from './bar';  // 合法
import Bar from './bar';  // これも合法
```

名前付き export は存在しないアイテムの import 時にエラーとなる。`foo.ts` 内:

```typescript
const foo = 'blah';
export default foo;
```

`bar.ts` で `{fizz}` を import するとエラー: "Module has no exported member 'fizz'." しかし:

```typescript
import fizz from './foo';
```

予期しない `fizz === foo` となり、デバッグが困難になる。

さらに、default export は名前空間付けのためにすべてを1つのオブジェクトに詰め込むことを助長し、不必要なスコープの曖昧さを生じさせる。

代わりに、名前空間付けにはファイルスコープを使用し、名前付き export を推奨する:

```typescript
export const SOME_CONSTANT = ...
export function someHelpfulFunction() { ... }
export class Foo {
  // クラスの内容のみ
}
```

#### export の可視性

TypeScript は export されたシンボルの可視性を制限しない。モジュール外で使用されるシンボルのみを export すること。export される API サーフェスを最小化すること。

#### ミュータブルな export

ミュータブルな export を避けること。`export let` は禁止される:

```typescript
export let foo = 3;
// ES6: foo はミュータブルで、import 側は変更を観測する
// TS: foo が再エクスポートされた場合、import 側は変更を観測しない
window.setTimeout(() => {
  foo = 4;
}, 1000);
```

代わりに明示的な getter 関数を使用すること:

```typescript
let foo = 3;
window.setTimeout(() => {
  foo = 4;
}, 1000);

export function getFoo() { return foo; }
```

条件付き export の場合、先にチェックを行ってから export すること。モジュール本体の実行後、すべての export が最終的であることを保証すること:

```typescript
function pickApi() {
  if (useOtherApi()) return OtherApi;
  return RegularApi;
}
export const SomeApi = pickApi();
```

#### コンテナクラス

名前空間付けのために static メソッド/プロパティを持つコンテナクラスを作成**してはならない**:

```typescript
// これはしない
export class Container {
  static FOO = 1;
  static bar() { return 1; }
}
```

代わりに、個別の定数と関数を export すること:

```typescript
export const FOO = 1;
export function bar() { return 1; }
```

### import type と export type

#### import type

import されたシンボルが型としてのみ使用される場合は `import type {...}` を使用すること。値には通常の import を使用すること:

```typescript
import type {Foo} from './foo';
import {Bar} from './foo';

import {type Foo, Bar} from './foo';
```

**理由**: TypeScript コンパイラは型参照のためのランタイムロードを挿入せず、この区別を自動的に処理する。しかし:

- **開発モード**: 完全な型情報なしでのトランスパイルによる高速イテレーションでは、特定のケースで `import type` が必要になる。
- **本番モード**: 完全な型チェックにより `import type` が正しく使用されることが保証される。

副作用のためのランタイムロードを強制する場合は `import '...';` を使用すること。

#### export type

型を再エクスポートする場合は `export type` を使用すること:

```typescript
export type {AnInterface} from './foo';
```

**理由**: `export type` はファイル単位のトランスパイルでの型の再エクスポートを可能にする（`isolatedModules` のドキュメントを参照）。

`export type` は値シンボルの export を防ぐように見えるかもしれないが、保証はない。下流のコードが別のパスを通じて import する可能性がある。より良い方法: シンボルを分割する（例: `UserService` と `AjaxUserService`）ことで、型と値の使い分けを保証する。これはエラーが少なく、意図をより良く伝える。

#### namespace ではなくモジュールを使用する

TypeScript は namespace とモジュールをサポートするが、**namespace は禁止される**。コードは `import {foo} from 'bar';` の形式で import/export を使用**しなければならない**。

コードは `namespace Foo { ... }` を使用**してはならない**。namespace は外部のサードパーティコードとのインターフェースにのみ使用**してよい**。名前空間的にコードを整理するには別ファイルを使用すること。

コードは `require`（`import x = require('...');`）を使用**してはならない**。ES6 モジュール構文を使用すること。

```typescript
// これらのいずれも行わない
namespace Rocket {
  function launch() { ... }
}

/// <reference path="..."/>

import x = require('mydep');
```

> 注: TypeScript の `namespace` は以前「内部モジュール」であり、`module Foo { ... }` 構文を使用していた。それも使用しないこと。

---

## 言語機能

以下のセクションでは、どの機能を使用してよいか、およびその使用に関する制約を詳述する。ここで議論されていない機能は、特に推奨事項なしに使用してよい。

### ローカル変数宣言

#### const と let を使用する

**常に** `const` または `let` を使用すること。変数が後で再代入されない限り、デフォルトで `const` を使用すること。`var` は**決して**使用しないこと:

```typescript
const foo = otherValue;  // "foo" が変更されない場合
let bar = someValue;     // "bar" が再代入される場合
```

`const` と `let` は、ほとんどの言語と同様にブロックスコープである。JavaScript の `var` は関数スコープであり、扱いにくいバグの原因となる:

```typescript
var foo = someValue;  // 使用しない
```

変数は宣言前に使用**してはならない**。

#### 宣言ごとに1つの変数

各ローカル変数宣言は1つの変数のみを宣言する。`let a = 1, b = 2;` は使用しないこと:

```typescript
// Good
const a = 1;
const b = 2;
```

### 配列リテラル

#### `Array` コンストラクタを使用しない

`Array()` または `new Array()` を使用**してはならない**。紛らわしく矛盾した使い方がある:

```typescript
const a = new Array(2);     // [undefined, undefined]
const b = new Array(2, 3);  // [2, 3]
```

常にブラケット記法または `Array.from` を使用すること:

```typescript
const a = [2];
const b = [2, 3];

const c = [];
c.length = 2;

// [0, 0, 0, 0, 0]
Array.from<number>({length: 5}).fill(0);
```

#### 配列にプロパティを定義しない

配列に非数値プロパティを定義または使用しないこと（`length` を除く）。代わりに `Map` または `Object` を使用すること。

#### スプレッド構文の使用

スプレッド構文 `[...foo]` はイテラブルを便利に浅いコピーまたは連結する:

```typescript
const foo = [1];

const foo2 = [
  ...foo,
  6,
  7,
];

const foo3 = [
  5,
  ...foo,
];

foo2[1] === 6;  // true
foo3[1] === 1;  // true
```

スプレッド時、値は作成されるものと一致**しなければならない**。配列を作成する場合、イテラブルのみをスプレッドすること。プリミティブ（`null` や `undefined` を含む）をスプレッド**してはならない**:

```typescript
// Bad
const foo = [7];
const bar = [5, ...(shouldUseFoo && foo)];  // undefined になる可能性がある

const fooStrings = ['a', 'b', 'c'];
const ids = {...fooStrings};  // {0: 'a', 1: 'b', 2: 'c'} を作成、length なし
```

```typescript
// Good
const foo = shouldUseFoo ? [7] : [];
const bar = [5, ...foo];

const fooStrings = ['a', 'b', 'c'];
const ids = [...fooStrings, 'd', 'e'];
```

#### 配列の分割代入

配列リテラルは代入の左辺で分割代入してよい（単一の配列またはイテラブルから複数の値をアンパックする）。変数名の前にスペースなしで最後の rest 要素を含めること。未使用の要素は省略すること:

```typescript
const [a, b, c, ...rest] = generateResults();
let [, b,, d] = someArray;
```

分割代入は関数パラメータでも機能する。分割代入される配列パラメータがオプションの場合は常にデフォルトとして `[]` を指定し、左辺でデフォルト値を提供すること:

```typescript
function destructured([a = 4, b = 2] = []) { ... }
```

**禁止:**

```typescript
function badDestructuring([a, b] = [4, 2]) { ... }
```

**ヒント:** 関数パラメータや戻り値の複数のアンパックには、配列の分割代入よりもオブジェクトの分割代入を推奨する。要素に名前を付け、異なる型を指定できるためである。

### オブジェクトリテラル

#### `Object` コンストラクタを使用しない

`Object` コンストラクタは禁止される。代わりにオブジェクトリテラル（`{}` または `{a: 0, b: 1, c: 2}`）を使用すること。

#### オブジェクトのイテレーション

`for (... in ...)` でのオブジェクトのイテレーションはエラーが起きやすい。プロトタイプチェーンの列挙可能なプロパティを含むためである。

フィルタなしの `for (... in ...)` を使用**しないこと**:

```typescript
for (const x in someObj) {
  // x は親プロトタイプから来る可能性がある！
}
```

`if` で明示的にフィルタするか、`for (... of Object.keys(...))` を使用すること:

```typescript
for (const x in someObj) {
  if (!someObj.hasOwnProperty(x)) continue;
  // x は確実に someObj 上にある
}

for (const x of Object.keys(someObj)) {
  // x は確実に someObj 上にある
}

for (const [key, value] of Object.entries(someObj)) {
  // key は確実に someObj 上にある
}
```

#### スプレッド構文の使用

スプレッド構文 `{...bar}` はオブジェクトを浅いコピーする。後の値は同じキーの前の値を上書きする:

```typescript
const foo = {num: 1};

const foo2 = {
  ...foo,
  num: 5,
};

const foo3 = {
  num: 5,
  ...foo,
};

foo2.num === 5;  // true
foo3.num === 1;  // true
```

スプレッド時、値は作成されるものと一致**しなければならない**。オブジェクトを作成する場合、オブジェクトのみをスプレッドすること。配列やプリミティブ（`null` や `undefined` を含む）をスプレッド**してはならない**。Object 以外のプロトタイプを持つオブジェクト（例: クラス、インスタンス、関数）のスプレッドも避けること:

```typescript
// Bad
const foo = {num: 7};
const bar = {num: 5, ...(shouldUseFoo && foo)};  // undefined になる可能性がある

const fooStrings = ['a', 'b', 'c'];
const ids = {...fooStrings};  // {0: 'a', 1: 'b', 2: 'c'} を作成、length なし
```

```typescript
// Good
const foo = shouldUseFoo ? {num: 7} : {};
const bar = {num: 5, ...foo};
```

#### 計算プロパティ名

計算プロパティ（例: `{['key' + foo()]: 42}`）は許容され、dict スタイル（引用符付き）キーとみなされる。計算プロパティが symbol でない限り（例: `[Symbol.iterator]`）、引用符なしキーと混在させてはならない。

#### オブジェクトの分割代入

オブジェクトの分割代入パターンは、代入時に単一のオブジェクトから複数の値をアンパックするために使用してよい。

分割代入されたオブジェクトは関数パラメータとして機能するが、シンプルに保つべきである: 単一レベルの引用符なしショートハンドプロパティ。パラメータの分割代入では、より深いネストや計算プロパティは許可されない。左辺でデフォルト値を指定すること（`{str = 'some default'} = {}`、`{str} = {str: 'some default'}` ではなく）。分割代入されたオブジェクトがオプションの場合は `{}` をデフォルトにすること:

```typescript
interface Options {
  /** 何かを行う回数。 */
  num?: number;

  /** 何かに使う文字列。 */
  str?: string;
}

function destructured({num, str = 'default'}: Options = {}) {}
```

**禁止:**

```typescript
function nestedTooDeeply({x: {num, str}}: {x: Options}) {}
function nontrivialDefault({num, str}: Options = {num: 42, str: 'default'}) {}
```

### クラス

#### クラス宣言

クラス宣言はセミコロンで終了**してはならない**:

```typescript
class Foo {
}
```

```typescript
// これはしない
class Foo {
};
```

クラス式を含む文はセミコロンで終了**しなければならない**:

```typescript
export const Baz = class extends Bar {
  method(): number {
    return this.x;
  }
};
```

クラス宣言の中括弧とコンテンツの間の空行は、推奨も非推奨もされない:

```typescript
// OK: 中括弧の周りにスペースなし
class Baz {
  method(): number {
    return this.x;
  }
}

// これもOK: 両方の中括弧の周りに単一のスペース
class Foo {

  method(): number {
    return this.x;
  }

}
```

#### クラスメソッド宣言

クラスメソッド宣言では、メソッド間にセミコロンを使用**してはならない**:

```typescript
class Foo {
  doThing() {
    console.log("A");
  }
}
```

```typescript
// これはしない
class Foo {
  doThing() {
    console.log("A");
  };
}
```

メソッドは前後のコードと単一の空行で区切るべきである:

```typescript
class Foo {
  doThing() {
    console.log("A");
  }

  getOtherThing(): number {
    return 4;
  }
}
```

```typescript
// これはしない（空行がない）
class Foo {
  doThing() {
    console.log("A");
  }
  getOtherThing(): number {
    return 4;
  }
}
```

##### toString のオーバーライド

`toString` メソッドはオーバーライドしてよいが、常に成功し、目に見える副作用を持ってはならない。

**ヒント:** `toString` から他のメソッドを呼び出す際は注意すること。例外的な条件が無限ループを引き起こす可能性がある。

#### static メソッド

##### private static メソッドを避ける

可読性を損なわない場合、private static メソッドよりもモジュールローカル関数を推奨する。

##### 動的ディスパッチに依存しない

コードは static メソッドの動的ディスパッチに依存**すべきでない**。static メソッドはそれを定義する基底クラスでのみ呼び出す**べきである**。static メソッドは動的インスタンスを持つ変数で呼び出す**べきでなく**（そうする場合は `@nocollapse` をマークしなければならない）、それを定義していないサブクラスで直接呼び出す**ことは許されない**:

```typescript
// コンテキスト
class Base {
  /** @nocollapse */ static foo() {}
}
class Sub extends Base {}

// 非推奨: static メソッドを動的に呼び出さない
function callFoo(cls: typeof Base) {
  cls.foo();
}

// 禁止: 定義なしにサブクラスで呼び出さない
Sub.foo();

// 禁止: static メソッドで this にアクセスしない
class MyClass {
  static foo() {
    return this.staticField;
  }
}
MyClass.staticField = 1;
```

##### static コンテキストでの `this` 参照を避ける

コードは static コンテキストで `this` を使用**してはならない**。

JavaScript では `this` を通じて static フィールドにアクセスできる。他の言語とは異なり、static フィールドは継承される:

```typescript
class ShoeStore {
  static storage: Storage = ...;

  static isAvailable(s: Shoe) {
    // Bad: static メソッドで `this` を使用しない
    return this.storage.has(s.id);
  }
}

class EmptyShoeStore extends ShoeStore {
  static storage: Storage = EMPTY_STORE;  // ShoeStore の storage をオーバーライド
}
```

**理由**: このコードは読者を驚かせる。`this` を通じた static フィールドアクセスは予期されず、オーバーライド可能である。また、テスタビリティに問題のある大量の static state を助長する。

#### コンストラクタ

コンストラクタ呼び出しは、引数がない場合でも括弧を使用**しなければならない**:

```typescript
// Bad
const x = new Foo;
```

```typescript
// Good
const x = new Foo();
```

括弧の省略は微妙なミスにつながる。以下は異なる:

```typescript
new Foo().Bar();   // インスタンスの Bar を呼び出す
new Foo.Bar();     // static Bar を呼び出す
```

空のコンストラクタや単に親に委譲するだけのコンストラクタは不要である。ES2015 がデフォルトコンストラクタを提供する。ただし、パラメータプロパティ、可視性修飾子、またはパラメータデコレータを持つコンストラクタは、本体が空でも省略**すべきでない**:

```typescript
// 不要
class UnnecessaryConstructor {
  constructor() {}
}

// 不要なオーバーライド
class UnnecessaryConstructorOverride extends Base {
  constructor(value: number) {
    super(value);
  }
}
```

```typescript
// Good: デフォルトコンストラクタで問題ない
class DefaultConstructor {
}

// Good: パラメータプロパティを保持
class ParameterProperties {
  constructor(private myService) {}
}

// Good: デコレータを保持
class ParameterDecorators {
  constructor(@SideEffectDecorator myService) {}
}

// Good: private コンストラクタ
class NoInstantiation {
  private constructor() {}
}
```

コンストラクタは前後のコードと単一の空行で区切ること:

```typescript
class Foo {
  myField = 10;

  constructor(private readonly ctorParam) {}

  doThing() {
    console.log(ctorParam.getThing() + myField);
  }
}
```

```typescript
// これはしない（空行がない）
class Foo {
  myField = 10;
  constructor(private readonly ctorParam) {}
  doThing() {
    console.log(ctorParam.getThing() + myField);
  }
}
```

#### クラスメンバー

##### #private フィールドを使用しない

private フィールド（private 識別子）を使用**してはならない**:

```typescript
class Clazz {
  #ident = 1;
}
```

代わりに TypeScript の可視性アノテーションを使用すること:

```typescript
class Clazz {
  private ident = 1;
}
```

**理由**: private 識別子は TypeScript でダウンレベルされると、かなりの emit サイズとパフォーマンスの低下を引き起こし、ES2015 以前ではサポートされない。ES2015 までしかダウンレベルされず、それ以下にはならない。static 型チェックがある場合、実質的なメリットを提供しない。

##### readonly を使用する

コンストラクタ外で再代入されないプロパティには `readonly` をマークすること（深い不変性は不要）:

```typescript
class Foo {
  private readonly bar = 'bar';
}
```

##### パラメータプロパティ

自明な初期化子をクラスメンバーに伝搬する代わりに、TypeScript のパラメータプロパティを使用すること:

```typescript
// これはしない
class Foo {
  private readonly barService: BarService;

  constructor(barService: BarService) {
    this.barService = barService;
  }
}
```

```typescript
// Good
class Foo {
  constructor(private readonly barService: BarService) {}
}
```

パラメータプロパティのドキュメントが必要な場合は `@param` JSDoc タグを使用すること。

##### フィールド初期化子

クラスメンバーがパラメータでない場合、宣言時に初期化すること。これによりコンストラクタを省略できることもある:

```typescript
// これはしない
class Foo {
  private readonly userList: string[];

  constructor() {
    this.userList = [];
  }
}
```

```typescript
// Good
class Foo {
  private readonly userList: string[] = [];
}
```

**ヒント:** コンストラクタ完了後にプロパティを追加/削除すべきでない。クラスの形状に対する VM の最適化を妨げる。後で埋められるオプショナルフィールドは、形状変更を防ぐために明示的に `undefined` で初期化すべきである。

##### クラスのレキシカルスコープ外で使用されるプロパティ

Angular コンポーネントのテンプレートプロパティなど、含むクラスのレキシカルスコープ外で使用されるプロパティは `private` を使用**してはならない**。適切に `protected` または `public` を使用すること。Angular/AngularJS テンプレートでは `protected` を使用し、Polymer では `public` を使用する。

TypeScript コードはプロパティの可視性をバイパスするために `obj['foo']` を使用**してはならない**。

**理由**: `private` はプロパティアクセスがクラスメソッドにスコープされることをツールと人間に宣言する。未使用コードチェッカーなどのツールはこれに依存している。`obj['foo']` は可視性をバイパスし、最適化との互換性を壊す。

##### ゲッターとセッター

ゲッターとセッターは使用してよい。ゲッターは純粋関数（一貫した結果、副作用なし）で**なければならない**。ゲッターは観測可能な状態を変更**してはならない**。内部的または冗長な実装の詳細の可視性を制限する:

```typescript
class Foo {
  constructor(private readonly someService: SomeService) {}

  get someMember(): string {
    return this.someService.someVariable;
  }

  set someMember(newValue: string) {
    this.someService.someVariable = newValue;
  }
}
```

```typescript
// これはしない
class Foo {
  nextId = 0;
  get next() {
    return this.nextId++;  // Bad: ゲッターが状態を変更している
  }
}
```

アクセサがクラスプロパティを隠す場合、隠されたプロパティには `internal` や `wrapped` のような完全な単語をプレフィックス/サフィックスとして付けて**もよい**。可能な限りアクセサを通じて値にアクセスすること。少なくとも1つのアクセサは自明でない処理をする**必要がある**。単にプロパティを隠すためだけのパススルーアクセサを定義しないこと。代わりに、プロパティを public または `readonly` にすること:

```typescript
class Foo {
  private wrappedBar = '';

  get bar() {
    return this.wrappedBar || 'bar';
  }

  set bar(wrapped: string) {
    this.wrappedBar = wrapped.trim();
  }
}
```

```typescript
// これはしない（ロジックのないアクセサ）
class Bar {
  private barInternal = '';

  get bar() {
    return this.barInternal;
  }

  set bar(value: string) {
    this.barInternal = value;
  }
}
```

ゲッターとセッターは `Object.defineProperty` を使用**してはならない**。プロパティのリネームに干渉するためである。

##### 計算プロパティ

計算プロパティは、プロパティが symbol の場合にのみ使用してよい。dict スタイルプロパティ（引用符付きまたは計算された非 symbol キー）は許可されない。論理的にイテラブルなクラスに対して `[Symbol.iterator]` メソッドを定義すること。これ以外での `Symbol` の使用は控えめにすること。

**ヒント:** ビルトイン symbol（例: `Symbol.isConcatSpreadable`）には注意すること。ポリフィルされず、古いブラウザでは動作しない。

#### 可視性

プロパティ、メソッド、および型全体の可視性を制限することで、コードの結合度を下げる。

- シンボルの可視性を可能な限り制限する
- private メソッドは、クラス外の同じファイル内のエクスポートされない関数に変換する
- private プロパティは、別のエクスポートされないクラスに移動する
- TypeScript のシンボルはデフォルトで public である。readonly でない public パラメータプロパティ（コンストラクタ内）を宣言する場合を除き、`public` を決して使用しないこと

```typescript
class Foo {
  public bar = new Bar();  // BAD: public 修飾子は不要

  constructor(public readonly baz: Baz) {}  // BAD: readonly は public を暗黙に含む
}
```

```typescript
class Foo {
  bar = new Bar();  // GOOD: デフォルトで public

  constructor(public baz: Baz) {}  // public 修飾子は許可される
}
```

[export の可視性](#export-の可視性)も参照。

#### 禁止されるクラスパターン

##### `prototype` を直接操作しない

`class` キーワードは `prototype` プロパティを操作するよりもクリアなクラス定義を可能にする。通常のコードはこれらのオブジェクトを操作すべきでない。ミックスインやビルトインオブジェクトのプロトタイプの変更は明示的に禁止される。

**例外:** フレームワークコード（Polymer、Angular）は `prototype` が必要になる場合があり、より悪い回避策に頼るべきではない。

### 関数

#### 用語

異なる関数の種類には微妙な違いがある。本ガイドでは標準的な用語を使用する:

- **function declaration（関数宣言）**: `function` キーワードを使用した宣言（式ではない）
- **function expression（関数式）**: `function` を使用した式（通常は代入やパラメータ内）
- **arrow function（アロー関数）**: `=>` 構文を使用した式
- **block body（ブロック本体）**: 中括弧を持つアロー関数の右辺
- **concise body（簡潔な本体）**: 中括弧のないアロー関数の右辺

メソッドおよびクラス/コンストラクタはこのセクションの対象外である。

#### 名前付き関数には関数宣言を推奨する

名前付き関数にはアロー関数や関数式よりも関数宣言を推奨する:

```typescript
function foo() {
  return 42;
}
```

```typescript
// これはしない
const foo = () => 42;
```

アロー関数は、例えば明示的な型アノテーションが必要な場合に使用して**よい**:

```typescript
interface SearchFunction {
  (source: string, subString: string): boolean;
}

const fooSearch: SearchFunction = (source, subString) => { ... };
```

#### ネストされた関数

他のメソッドや関数内にネストされた関数は、関数宣言またはアロー関数を使用して**よい**。メソッド本体ではアロー関数が推奨される。外側の `this` にアクセスできるためである。

#### 関数式を使用しない

関数式を使用**してはならない**。代わりにアロー関数を使用すること:

```typescript
bar(() => { this.doSomething(); })
```

```typescript
// これはしない
bar(function() { ... })
```

**例外:** 関数式は、コードが動的に `this` をリバインドする必要がある場合（非推奨）、またはジェネレータ関数の場合（アロー構文がないため）に_のみ_使用してよい。

#### アロー関数の本体

アロー関数には、必要に応じて簡潔な本体（式）またはブロック本体を使用すること:

```typescript
// トップレベル関数は宣言を使用
function someFunction() {
  // ブロック本体は問題ない
  const receipts = books.map((b: Book) => {
    const receipt = payMoney(b.price);
    recordTransaction(receipt);
    return receipt;
  });

  // 戻り値が使用される場合、簡潔な本体は問題ない
  const longThings = myValues.filter(v => v.length > 1000).map(v => String(v));

  function payMoney(amount: number) {
    // 宣言は問題ないが、`this` にアクセスしてはならない
  }

  // ネストされたアロー関数は const に代入してよい
  const computeTax = (amount: number) => amount * 0.12;
}
```

戻り値が実際に使用される場合のみ簡潔な本体を使用すること。ブロック本体は戻り値の型を `void` にし、副作用の漏れを防ぐ:

```typescript
// BAD: 戻り値が使用されない場合はブロック本体を使用する
myPromise.then(v => console.log(v));

// BAD: 型チェックは通るが戻り値が漏れる
let f: () => void;
f = () => 1;
```

```typescript
// GOOD: 戻り値が未使用、ブロック本体を使用
myPromise.then(v => {
  console.log(v);
});

// GOOD: 可読性のためにブロックを使用
const transformed = [1, 2, 3].map(v => {
  const intermediate = someComplicatedExpr(v);
  const more = acrossManyLines(intermediate);
  return worthWrapping(more);
});

// GOOD: 明示的な void で戻り値の漏れを防ぐ
myPromise.then(v => void console.log(v));
```

**ヒント:** `void` 演算子は、結果が未使用のとき、式本体のアロー関数が `undefined` を返すことを保証する。

#### `this` のリバインド

関数式と関数宣言は、特にリバインドする場合を除いて `this` を使用**してはならない**。リバインドは通常、アロー関数または明示的なパラメータで回避できる:

```typescript
// Bad: ここでの `this` は何？
function clickHandler() {
  this.textContent = 'Hello';
}

// Bad: `this` が暗黙的に document.body に設定される
document.body.onclick = clickHandler;
```

```typescript
// Good: アロー関数からオブジェクトを明示的に参照
document.body.onclick = () => { document.body.textContent = 'hello'; };

// 代替: 明示的なパラメータ
const setTextFn = (e: HTMLElement) => { e.textContent = 'hello'; };
document.body.onclick = setTextFn.bind(null, document.body);
```

`f.bind(this)`、`goog.bind(f, this)`、`const self = this` よりもアロー関数を推奨する。

#### コールバックとしてアロー関数を渡すことを推奨する

予期しない引数で呼び出されるコールバックは、型チェックを通過しても論理エラーを引き起こす可能性がある。

両方の関数の安定性が確実でない限り、名前付きコールバックを高階関数に渡すことを避けること。オプショナルパラメータに注意すること:

```typescript
// BAD: オプショナルな `radix` 引数に配列のインデックス 0, 1, 2 が渡される
const numbers = ['11', '5', '10'].map(parseInt);
// > [11, NaN, 2]
```

代わりに、パラメータを明示的に転送するアロー関数を渡すこと:

```typescript
// GOOD: 引数が明示的に渡される
const numbers = ['11', '5', '3'].map((n) => parseInt(n));
// > [11, 5, 3]

// GOOD: ローカルに定義され、コールバックとして設計されている
function dayFilter(element: string|null|undefined) {
  return element != null && element.endsWith('day');
}

const days = ['tuesday', undefined, 'juice', 'wednesday'].filter(dayFilter);
```

#### プロパティとしてのアロー関数

クラスは通常、アロー関数で初期化されたプロパティを含む**べきでない**。アロー関数プロパティは、呼び出し側に `this` がプリバインドされていることの理解を要求し、混乱を増大させる。そのようなハンドラを使用する呼び出しサイトと参照は壊れているように見える。

コードはインスタンスメソッドを呼び出すアロー関数を使用**すべきであり**（`const handler = (x) => { this.listener(x); };`）、インスタンスメソッドへの参照を取得/渡す**べきでない**（`const handler = this.listener; handler(x);`）。

> **注:** 特定の状況（例: テンプレート内での関数バインディング）では、プロパティとしてのアロー関数は有用で、より読みやすいコードを作成する。ここでは判断を使用すること。[`イベントハンドラ`](#イベントハンドラ)も参照。

```typescript
class DelayHandler {
  constructor() {
    // 問題: `this` が保持されない。コールバック内の `this` は DelayHandler ではない
    setTimeout(this.patienceTracker, 5000);
  }
  private patienceTracker() {
    this.waitedPatiently = true;
  }
}
```

```typescript
// Bad: アロー関数はプロパティにすべきでない
class DelayHandler {
  constructor() {
    // Bad: `this` バインディングを忘れたように見える
    setTimeout(this.patienceTracker, 5000);
  }
  private patienceTracker = () => {
    this.waitedPatiently = true;
  }
}
```

```typescript
// Better: 呼び出し時に `this` を明示的に管理する
class DelayHandler {
  constructor() {
    // 可能な場合は無名関数を使用
    setTimeout(() => {
      this.patienceTracker();
    }, 5000);
  }
  private patienceTracker() {
    this.waitedPatiently = true;
  }
}
```

#### イベントハンドラ

イベントハンドラは、ハンドラのアンインストールが不要な場合（クラス自体が発行するイベント）にアロー関数を使用して**よい**。ハンドラのアンインストールが必要な場合は、プロパティとしてのアロー関数が適切である。`this` を自動的にキャプチャし、アンインストールのための安定した参照を提供する:

```typescript
// イベントハンドラは無名またはアロー関数プロパティでよい
class Component {
  onAttached() {
    // このクラスが発行するイベント、アンインストール不要
    this.addEventListener('click', () => {
      this.listener();
    });

    // this.listener は安定しており、後でアンインストール可能
    window.addEventListener('onbeforeunload', this.listener);
  }

  onDetached() {
    // window が発行するイベント。アンインストールしないと、this.listener が
    // `this` への参照を保持し、メモリリークを引き起こす
    window.removeEventListener('onbeforeunload', this.listener);
  }

  // `this` に自動バインドされたアロー関数プロパティ
  private listener = () => {
    confirm('Do you want to exit the page?');
  }
}
```

イベントハンドラのインストール時に `bind` を使用**してはならない**。アンインストールできない一時的な参照を作成するためである:

```typescript
// Bad: バインディングが一時的な参照を作成し、アンインストールを妨げる
class Component {
  onAttached() {
    // アンインストールできない一時的な参照を作成
    window.addEventListener('onbeforeunload', this.listener.bind(this));
  }

  onDetached() {
    // この bind は別の参照を作成するので、何も行わない
    window.removeEventListener('onbeforeunload', this.listener.bind(this));
  }

  private listener() {
    confirm('Do you want to exit the page?');
  }
}
```

#### パラメータ初期化子

オプショナルな関数パラメータにはデフォルト初期化子を指定して**よい**。初期化子は観測可能な副作用を持っ**てはならない**。初期化子はシンプルに保つこと:

```typescript
function process(name: string, extraContext: string[] = []) {}
function activate(index = 0) {}
```

```typescript
// BAD: カウンターをインクリメントする副作用
let globalCounter = 0;
function newId(index = globalCounter++) {}

// BAD: 共有ミュータブル状態を公開し、結合を導入
class Foo {
  private readonly defaultPaths: string[];
  frobnicate(paths = defaultPaths) {}
}
```

デフォルトパラメータは控えめに使用すること。自然な順序のない多くのオプショナルパラメータを持つ読みやすい API には分割代入を推奨する:

```typescript
function complexFunction({
  param1 = 'default1',
  param2 = 'default2',
  param3 = 'default3',
} = {}) { ... }
```

#### rest とスプレッドを適切に使用する

`arguments` にアクセスする代わりに rest パラメータを使用すること。ローカル変数/パラメータに `arguments` と名付けないこと。ビルトインを紛らわしく隠蔽するためである:

```typescript
function variadic(array: string[], ...numbers: number[]) {}
```

`Function.prototype.apply` の代わりに関数のスプレッド構文を使用すること:

```typescript
// Good
myFunction(...array, ...iterable, ...generator());
```

#### 関数のフォーマット

関数本体の開始または終了に空行は許可されない。

関数本体内では、文の_論理的なグループ_を作成するために単一の空行を使用して**よい**。

ジェネレータでは `*` を `function` と `yield` に付けること。`function* foo()` および `yield* iter` のように。`function *foo()` や `yield *iter` ではない。

単一引数のアロー関数の括弧は推奨されるが必須ではない。

rest やスプレッド構文の `...` の後にスペースを入れないこと:

```typescript
function myFunction(...elements: number[]) {}
myFunction(...array, ...iterable, ...generator());
```

### this

`this` は以下の場合にのみ使用すること:

- クラスのコンストラクタとメソッド
- 明示的な `this` 型を持つ関数（`function func(this: ThisType, ...)`）
- `this` を使用してよいスコープ内のアロー関数

`this` を以下の参照に使用しないこと:

- グローバルオブジェクト
- `eval` コンテキスト
- イベントターゲット
- 不必要に `call()` や `apply()` された関数

```typescript
// これはしない
this.alert('Hello');
```

### interface

TypeScript の interface は構造型を定義するために使用される。詳細なガイダンスについては[型システム](#型システム)セクションを参照。

### プリミティブリテラル

#### 文字列リテラル

##### シングルクォートを使用する

通常の文字列リテラルにはダブルクォート（`"`）ではなくシングルクォート（`'`）を使用すること:

```typescript
const message = 'Hello, world!';
```

**ヒント:** 文字列にシングルクォートが含まれる場合、エスケープを避けるためにテンプレート文字列を検討すること:

```typescript
const message = `It's a beautiful day`;
```

##### 行の継続を使用しない

文字列リテラル内で行の継続（行末のバックスラッシュ）を使用**してはならない**。通常の文字列とテンプレート文字列の両方に適用される。スラッシュの後に末尾の空白が続くとトリッキーなエラーを引き起こし、読者にも分かりにくい:

```typescript
// Bad: 行の継続
const LONG_STRING = 'This is very long. \
    It contains spaces from indentation.';
```

代わりに文字列連結を使用すること:

```typescript
const LONG_STRING = 'This is very long. ' +
    'It does not contain indentation spaces.';

const SINGLE_STRING =
    'http://example.com/very/long/url';
```

##### テンプレートリテラル

複雑な文字列連結よりもテンプレートリテラル（`` ` ``）を使用すること。特に複数の文字列を扱う場合。テンプレートリテラルは複数行にまたがってよい。

複数行のテンプレートリテラルは囲むブロックのインデントに従う必要はないが、空白が問題にならない場合は従ってもよい:

```typescript
function arithmetic(a: number, b: number) {
  return `Here is a table of arithmetic operations:
${a} + ${b} = ${a + b}
${a} - ${b} = ${a - b}
${a} * ${b} = ${a * b}
${a} / ${b} = ${a / b}`;
}
```

#### 数値リテラル

数値は16進、8進、2進に `0x`、`0o`、`0b` プレフィックスを使用し、小文字を使用する。`x`、`o`、`b` が直後に続く場合を除き、先頭のゼロを含めないこと:

```typescript
const hex = 0xdeadbeef;
const octal = 0o755;
const binary = 0b1010;
```

#### 型変換

TypeScript コードは型の変換に `String()` および `Boolean()`（`new` なし）、文字列テンプレートリテラル、または `!!` を使用して**よい**:

```typescript
const bool = Boolean(false);
const str = String(aNumber);
const bool2 = !!str;
const str2 = `result: ${bool2}`;
```

enum 型の値は `Boolean()` や `!!` を使用してブール値に変換**してはならない**。演算子で明示的に比較すること:

```typescript
enum SupportLevel {
  NONE,
  BASIC,
  ADVANCED,
}

const level: SupportLevel = ...;
let enabled = Boolean(level);  // Bad

const maybeLevel: SupportLevel|undefined = ...;
enabled = !!maybeLevel;  // Bad
```

```typescript
enum SupportLevel {
  NONE,
  BASIC,
  ADVANCED,
}

const level: SupportLevel = ...;
let enabled = level !== SupportLevel.NONE;  // Good

const maybeLevel: SupportLevel|undefined = ...;
enabled = level !== undefined && level !== SupportLevel.NONE;  // Good
```

**理由**: ほとんどの目的で、enum のマッピングされた値はソースコード内では重要でない。名前で参照される。驚くべき動作（最初の値が falsy になる）は、ブール値への変換を望ましくなくする。読者は enum 値が最初に宣言されたものかどうか知らない可能性がある。

コードは数値の解析に `Number()` を使用し、解析が不可能でない限り `NaN` を明示的にチェック**しなければならない**:

```typescript
const aNumber = Number('123');
if (!isFinite(aNumber)) throw new Error(...);
```

**注:** `Number('')`、`Number(' ')`、`Number('\t')` は `NaN` ではなく `0` を返す。`Number('Infinity')` と `Number('-Infinity')` はそれぞれの値を返す。指数表記は `Infinity` にオーバーフローする可能性がある。特別な処理が必要な場合がある。

コードは変換に単項プラス（`+`）を使用**してはならない**。解析が失敗する可能性があり、驚くべきコーナーケースがあり、コードの匂いである。単項プラスはレビューで見逃しやすい。

```typescript
// Bad
const x = +y;
```

コードは10進以外の文字列を除いて `parseInt` や `parseFloat` を使用**してはならない**。両方とも末尾の文字を無視し、エラー条件を隠蔽する:

```typescript
// 基数に関係なくエラーが起きやすい
const n = parseInt(someString, 10);
const f = parseFloat(someString);
```

基数解析が必要なコードは、`parseInt` を呼び出す前に入力が適切な桁のみを含むことを確認**しなければならない**:

```typescript
if (!/^[a-fA-F0-9]+$/.test(someString)) throw new Error(...);
// 16進数に必要
// tslint:disable-next-line:ban
const n = parseInt(someString, 16);  // 基数が10以外の場合のみ許可
```

整数の解析には `Number()` の後に `Math.floor` または `Math.trunc` を使用すること:

```typescript
let f = Number(someString);
if (isNaN(f)) handleError();
f = Math.floor(f);
```

##### 暗黙の型変換

暗黙のブール型変換を持つ条件節で明示的なブール変換を使用**してはならない**。対象は `if`、`for`、`while` 文の条件である:

```typescript
const foo: MyInterface|null = ...;
if (!!foo) {...}   // Bad
while (!!foo) {...}  // Bad
```

```typescript
const foo: MyInterface|null = ...;
if (foo) {...}      // Good
while (foo) {...}   // Good
```

明示的な変換と同様に、enum 型の値は暗黙的にブール値に変換**してはならない**。明示的に比較すること:

```typescript
enum SupportLevel {
  NONE,
  BASIC,
  ADVANCED,
}

const level: SupportLevel = ...;
if (level) {...}  // Bad

const maybeLevel: SupportLevel|undefined = ...;
if (level) {...}  // Bad
```

```typescript
enum SupportLevel {
  NONE,
  BASIC,
  ADVANCED,
}

const level: SupportLevel = ...;
if (level !== SupportLevel.NONE) {...}  // Good

const maybeLevel: SupportLevel|undefined = ...;
if (level !== undefined && level !== SupportLevel.NONE) {...}  // Good
```

その他の値型は暗黙的に変換してよいし、明示的に比較してもよい:

```typescript
// 明示的な比較はOK
if (arr.length > 0) {...}

// ブール型変換もOK
if (arr.length) {...}
```

### 制御構造

#### 制御フロー文とブロック

制御フロー文（`if`、`else`、`for`、`do`、`while`）は、単一の文であっても**常に**中括弧付きブロックを使用すること。空でないブロックの最初の文は独自の行で始まること:

```typescript
for (let i = 0; i < x; i++) {
  doSomethingWith(i);
}

if (x) {
  doSomethingWithALongMethodNameThatForcesANewLine(x);
}
```

```typescript
// これはしない
if (x)
  doSomethingWithALongMethodNameThatForcesANewLine(x);

for (let i = 0; i < x; i++) doSomethingWith(i);
```

**例外:** 1行に収まる `if` 文はブロックを省略して**よい**:

```typescript
if (x) x.doFoo();
```

##### 制御文内での代入

制御文内での変数への代入を避けること。代入は等値チェックと間違えやすい:

```typescript
// Bad: 代入が等値チェックと間違えられる
if (x = someFunction()) {
  // ...
}
```

```typescript
// Good
x = someFunction();
if (x) {
  // ...
}
```

制御文内での代入が好ましい場合、意図を示すために追加の括弧で囲むこと:

```typescript
while ((x = someFunction())) {
  // 二重括弧は代入が意図的であることを示す
  // ...
}
```

##### コンテナのイテレーション

配列のイテレーションには `for (... of someArr)` を推奨する。`Array.prototype.forEach` や通常の `for` ループも許容される:

```typescript
for (const x of someArr) {
  // x は someArr の値
}

for (let i = 0; i < someArr.length; i++) {
  // インデックスが必要な場合は明示的にカウント
  const x = someArr[i];
  // ...
}

for (const [i, x] of someArr.entries()) {
  // 上記の代替
}
```

`for`-`in` ループは dict スタイルオブジェクトにのみ使用してよい。配列に `for (... in ...)` を使用**してはならない**。値ではなく配列のインデックス（文字列として！）を返すためである:

```typescript
// Bad: x はインデックス！
for (const x in someArray) {
  // ...
}
```

`Object.prototype.hasOwnProperty` で `for`-`in` ループをフィルタすべきである。`Object.keys`、`Object.values`、`Object.entries` を使用した `for`-`of` を推奨する:

```typescript
for (const key in obj) {
  if (!obj.hasOwnProperty(key)) continue;
  doWork(key, obj[key]);
}

for (const key of Object.keys(obj)) {
  doWork(key, obj[key]);
}

for (const value of Object.values(obj)) {
  doWorkValOnly(value);
}

for (const [key, value] of Object.entries(obj)) {
  doWork(key, value);
}
```

#### グルーピング括弧

オプショナルなグルーピング括弧は、作者とレビュアーの両方が誤解の合理的な可能性がないか、括弧が可読性を改善しないと合意した場合にのみ省略する。

`delete`、`typeof`、`void`、`return`、`throw`、`case`、`in`、`of`、`yield` に続く式の周りに不要な括弧を使用**しないこと**。

#### 例外処理

例外は重要であり、例外的なケースに使用すべきである。

カスタム例外は追加のエラー情報を伝え、ネイティブの `Error` が不十分な場合に定義/使用すべきである。

アドホックなエラー処理（エラーコンテナ参照、エラープロパティ）よりも例外のスローを推奨する。

##### `new` を使用して Error をインスタンス化する

`Error()` ではなく**常に** `new Error()` を使用すること。両方とも新しい `Error` インスタンスを作成するが、`new` は他のオブジェクトのインスタンス化方法と一貫している:

```typescript
throw new Error('Foo is not a valid bar.');
```

```typescript
// これはしない
throw Error('Foo is not a valid bar.');
```

##### Error のみをスローする

JavaScript は任意の値のスローを許可する。スローされた値が `Error` でない場合、スタックトレースが生成されず、デバッグが困難になる。これは `Promise` のリジェクションにも適用される。async 関数では `Promise.reject(obj)` は `throw obj;` と等しい。

```typescript
// Bad: スタックトレースなし
throw 'oh noes!';

// Promise の場合
new Promise((resolve, reject) => void reject('oh noes!'));
Promise.reject();
Promise.reject('oh noes!');
```

代わりに `Error` サブクラスのみをスローすること:

```typescript
// Error のみをスロー
throw new Error('oh noes!');

// またはサブクラス
class MyError extends Error {}
throw new MyError('my oh noes!');

// Promise の場合
new Promise((resolve) => resolve());  // reject なしはOK
new Promise((resolve, reject) => void reject(new Error('oh noes!')));
Promise.reject(new Error('oh noes!'));
```

##### キャッチと再スロー

エラーをキャッチする際、すべてのスローされた値が `Error` インスタンスであると仮定すること:

```typescript
function assertIsError(e: unknown): asserts e is Error {
  if (!(e instanceof Error)) throw new Error("e is not an Error");
}

try {
  doSomething();
} catch (e: unknown) {
  // すべてのエラーは Error サブクラスでなければならない
  assertIsError(e);
  displayError(e.message);
  // または再スロー
  throw e;
}
```

例外ハンドラは、呼び出された API が証明可能に非 `Error` をスローしない限り、非 `Error` 型を防御的に処理**してはならない**。非 `Error` の出所を特定するコメントを含めること:

```typescript
try {
  badApiThrowingStrings();
} catch (e: unknown) {
  // 注: 不適切な API がエラーの代わりに文字列をスローする
  if (typeof e === 'string') { ... }
}
```

**理由**: 過度に防御的なプログラミングを避けること。存在しない問題に対する防御の繰り返しは、実用性のないボイラープレートを生む。

##### 空の catch ブロック

catch ブロックで何もしないことが正しいケースはまれである。適切な場合はコメントで説明すること:

```typescript
try {
  return handleNumericResponse(response);
} catch (e: unknown) {
  // レスポンスが数値でない。テキストとしての処理を続行する。
}
return handleTextResponse(response);
```

**禁止:**

```typescript
try {
  shouldFail();
  fail('expected an error');
} catch (expected: unknown) {
}
```

**ヒント:** このパターンは機能しない。`fail` がスローするエラーをキャッチしてしまう。代わりに `assertThrows()` を使用すること。

#### switch 文

すべての `switch` 文は、空であっても `default` ステートメントグループを含ま**なければならない**。`default` は最後でなければならない:

```typescript
switch (x) {
  case Y:
    doSomethingElse();
    break;
  default:
    // 何もしない。
}
```

switch ブロック内の各ステートメントグループは、`break`、`return`、または例外で突然終了する。空でない case グループはフォールスルー**してはならない**（コンパイラにより強制される）:

```typescript
// Bad: フォールスルーは許可されない
switch (x) {
  case X:
    doSomething();
    // フォールスルー - 許可されない！
  case Y:
    // ...
}
```

空のステートメントグループはフォールスルーしてよい:

```typescript
switch (x) {
  case X:
  case Y:
    doSomething();
    break;
  default:
    // 何もしない。
}
```

#### 等値チェック

**常に**三重等号（`===`）と不等号（`!==`）を使用すること。二重等号演算子は理解しにくくエラーが起きやすい型変換を引き起こし、VM にとっても遅い。[JavaScript 等値テーブル](https://dorey.github.io/JavaScript-Equality-Table/)を参照。

```typescript
// Bad: 型変換の動作は理解しにくい
if (foo == 'bar' || baz != bam) {
  // ...
}
```

```typescript
// Good
if (foo === 'bar' || baz !== bam) {
  // ...
}
```

**例外:** リテラル `null` との比較には `==` および `!=` を使用して**よい**。`null` と `undefined` の両方をカバーするためである:

```typescript
if (foo == null) {
  // null または undefined の場合にトリガーされる
}
```

#### 型アサーションと非 null アサーション

型アサーション（`x as SomeType`）と非 null アサーション（`y!`）は安全でない。両方とも TypeScript を静かにさせるだけで、ランタイムチェックを挿入しないため、プログラムがクラッシュする可能性がある。

明確な理由なしにこれらを使用**すべきでない**。

以下の代わりに:

```typescript
(x as Foo).foo();

y!.bar();
```

ランタイムチェックを明示的に書くこと:

```typescript
// Foo がクラスであると仮定
if (x instanceof Foo) {
  x.foo();
}

if (y) {
  y.bar();
}
```

ローカルコードのプロパティがアサーションの安全性を保証する場合、説明を追加すること:

```typescript
// x は Foo である。なぜなら...
(x as Foo).foo();

// y は null になりえない。なぜなら...
y!.bar();
```

理由が明白な場合、コメントは不要かもしれない。判断を使用すること。

##### 型アサーション構文

型アサーションは `as` 構文を使用**しなければならない**（山括弧ではなく）。これによりメンバーへのアクセス時に括弧が強制される:

```typescript
// Bad
const x = (<Foo>z).length;
const y = <Foo>z.length;
```

```typescript
// Good: z は Foo でなければならない。なぜなら...
const x = (z as Foo).length;
```

##### 二重アサーション

TypeScript はより_具体的_またはより_汎用的_な型バージョンへの変換のアサーションのみを許可する。この基準を満たさないアサーションはエラーとなる: "Conversion...may be a mistake because neither type sufficiently overlaps with the other."

アサーションが安全であることが確実な場合、`unknown`（すべての型より汎用的）を通じた_二重アサーション_を行うこと:

```typescript
// x はここでは Foo である。なぜなら...
(x as unknown as Foo).fooMethod();
```

中間型として `unknown` を使用すること（`any` や `{}` ではなく）。

##### 型アサーションとオブジェクトリテラル

オブジェクトリテラルにはアサーション（`as Foo`）ではなく型アノテーション（`: Foo`）を使用すること。これにより interface フィールドが変更された場合のリファクタリングバグを検出できる:

```typescript
interface Foo {
  bar: number;
  baz?: string;  // かつて "bam" だったが、後に "baz" にリネーム
}

// Bad: エラーなし！
const foo = {
  bar: 123,
  bam: 'abc',
} as Foo;

function func() {
  return {
    bar: 123,
    bam: 'abc',  // エラーなし！
  } as Foo;
}
```

```typescript
interface Foo {
  bar: number;
  baz?: string;
}

// Good: "bam" についてエラーを出す
const foo: Foo = {
  bar: 123,
  bam: 'abc',  // エラー: bam は Foo にない
};

function func(): Foo {
  return {
    bar: 123,
    bam: 'abc',   // エラー: bam は Foo にない
  };
}
```

#### try ブロックを焦点を絞る

可読性を損なわない範囲で try ブロックのコードを制限すること:

```typescript
try {
  const result = methodThatMayThrow();
  use(result);
} catch (error: unknown) {
  // ...
}
```

```typescript
// 代替
let result;
try {
  result = methodThatMayThrow();
} catch (error: unknown) {
  // ...
}
use(result);
```

スローしない行を外に移動することで、どのメソッドがスローするかを読者が特定しやすくなる。一時変数を複雑にしない一部のインラインの非スロー呼び出しは内部に残してよい。

**例外:** ループ内でのパフォーマンス問題が try ブロックをループ全体に広げることを正当化する場合がある。

### デコレータ

デコレータは `@` 構文で、`@MyDecorator` のようなものである。

新しいデコレータを定義**してはならない**。フレームワークが定義するもののみを使用すること:

- Angular（`@Component`、`@NgModule` など）
- Polymer（`@property`）

**理由**: デコレータは TC39 提案から分岐した実験的機能であり、既知の修正不能なバグがある。

デコレータを使用する場合、デコレートされるシンボルの直前に空行なしで配置**しなければならない**:

```typescript
/** JSDoc コメントはデコレータの前に置く */
@Component({...})  // デコレータの後に空行なし
class MyComp {
  @Input() myField: string;  // 同じ行または折り返し

  @Input()
  myOtherField: string;  // 折り返しはOK
}
```

### 禁止される機能

#### プリミティブ型のラッパーオブジェクト

TypeScript コードはプリミティブ `String`、`Boolean`、`Number` のラッパークラスをインスタンス化**してはならない**。予期しない動作がある:

```typescript
const s = new String('hello');
const b = new Boolean(false);  // true と評価される！
const n = new Number(5);
```

ラッパーは変換のための関数として呼び出して**よい**（`+` や空文字列連結よりも推奨）、またはシンボルの作成のために使用してよい。[型変換](#型変換)を参照。

#### 自動セミコロン挿入

自動セミコロン挿入（ASI）に依存**してはならない**。すべての文をセミコロンで明示的に終了すること。これによりバグを防ぎ、ASI サポートが限定的なツールとの互換性を確保する:

```typescript
function foo() {
  return
    {bar: 1};  // 問題: セミコロンが挿入される
}
```

#### const enum

コードは `const enum` を使用**してはならない**。通常の `enum` を使用すること:

```typescript
// これはしない
const enum Color {RED, GREEN, BLUE}
```

```typescript
// これを行う
enum Color {RED, GREEN, BLUE}
```

**理由**: TypeScript の enum はすでにミューテートできない。`const enum` は enum を JavaScript モジュールのユーザーから見えなくする別の最適化機能である。

#### debugger 文

本番コードに `debugger` 文を含め**てはならない**:

```typescript
// これはしない
function debugMe() {
  debugger;
}
```

#### `with`

`with` を使用**してはならない**。コードを理解しにくくし、[ES5 以降 strict モードで禁止](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/with)されている。

#### 動的コード評価

`eval` や `Function(...string)` コンストラクタを使用**してはならない**（コードローダーを除く）。これらは潜在的に危険であり、厳格な [Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) と互換性がない。

#### 非標準機能

非標準の ECMAScript または Web Platform 機能を使用**してはならない**。

これには以下が含まれる:

- 廃止/削除された古い ECMAScript/Web Platform 機能（[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Deprecated_and_obsolete_features) を参照）
- まだ標準化されていない新しい ECMAScript 機能
  - TC39 の作業ドラフト機能や提案段階の機能を避ける
  - 現在の ECMA-262 仕様の機能のみを使用する
- 提案されたが未完成の Web 標準
  - 提案プロセスを完了していない WHATWG 提案
- 非標準の言語「拡張」（トランスパイラ提供のもの）

特定のランタイムをターゲットとするプロジェクト（最新 Chrome のみ、拡張機能、Node、Electron）はそれらの API を使用してよい。プロプライエタリ/単一ブラウザの API には注意すること。それらを抽象化するライブラリの検討を推奨する。

#### ビルトインオブジェクトの変更

コンストラクタやプロトタイプにメソッドを追加してビルトイン型を変更**しないこと**。これを行うライブラリに依存することも避けること。

絶対に必要な場合（サードパーティ API の要件）を除き、グローバルオブジェクトにシンボルを追加しないこと。

---

## 命名

### 識別子

識別子は ASCII 文字、数字、アンダースコア（定数およびテストメソッド名用）、および（まれに）`$` のみを使用する。

#### 命名スタイル

TypeScript は型で情報を表現するため、名前に型情報を装飾すべきでない。命名のガイダンスについては [Testing Blog](https://testing.googleblog.com/2017/10/code-health-identifiernamingpostforworl.html) も参照。

具体例:

- private プロパティ/メソッドに末尾/先頭のアンダースコアを付けない
- オプショナルパラメータに `opt_` プレフィックスを付けない
- 慣用的でない限り、interface を特別にマークしない（`IMyInterface` や `MyFooInterface`）。interface 名はその存在理由を説明するものにする（例: `class TodoItem` とシリアル化のための `interface TodoItemStorage`）
- `Observable` には外部規約として `$` サフィックスを付け、Observable と具体的な値の混同を解消する。プロジェクト内で一貫して使用すること

#### 説明的な名前

名前は説明的で、新しい読者にとって明確でなければならない。曖昧または馴染みのない略語を使用しないこと。内部の文字を削除して略語にしないこと。

**例外:** 10行以内のスコープの変数（エクスポートされない API の引数を含む）は短い（単一文字の）名前を使用して**よい**。

```typescript
// Good: 適切な識別子
errorCount          // 略語なし
dnsConnectionIndex  // ほとんどの人が "DNS" を知っている
referrerUrl         // "URL" は明確
customerId          // "Id" はどこでも使われる
```

```typescript
// 禁止: 不適切な識別子
n                   // 意味がない
nErr                // 曖昧
nCompConns          // 曖昧
wgcConnections      // グループ内の人しか知らない
pcReader            // "pc" には多くの意味がある
cstmrId             // 内部の文字を削除
kSecondsPerDay      // ハンガリアン記法は使わない
customerID          // "ID" のキャメルケースが間違い
```

#### キャメルケース

名前中の頭字語は完全な単語として扱うこと: `loadHttpUrl` であって `loadHTTPURL` ではない。プラットフォーム名が要求する場合を除く（例: `XMLHttpRequest`）:

```typescript
// Good
function loadHttpUrl() {...}

// Bad
function loadHTTPURL() {...}

// 例外: プラットフォーム名
const xhr = new XMLHttpRequest();
```

#### ドル記号

識別子は一般的に `$` を使用**すべきでない**。サードパーティフレームワークの命名規約を除く。`Observable` の `$` 使用については上記を参照。

### 識別子の種類別ルール

ほとんどの識別子は種類に基づくケーシングに従う:

| スタイル | カテゴリ |
|---------|---------|
| `UpperCamelCase` | class / interface / type / enum / decorator / 型パラメータ / TSX コンポーネント関数 / JSXElement 型パラメータ |
| `lowerCamelCase` | variable / parameter / function / method / property / module alias |
| `CONSTANT_CASE` | グローバル定数値、enum 値 |
| `#ident` | private 識別子（使用しない） |

#### 型パラメータ

型パラメータ（`Array<T>` の `T` のような）は単一の大文字（`T`）または `UpperCamelCase` を使用して**よい**。

#### テスト名

xUnit スタイルのフレームワークにおけるテストメソッド名は `_` セパレータを使用して**よい**: `testX_whenY_doesZ()`。

#### `_` プレフィックス/サフィックス

識別子はプレフィックスまたはサフィックスとして `_` を使用**してはならない**。スタンドアロンの `_`（未使用パラメータ）も含む。

> **ヒント:** 配列/タプルの一部の要素のみが必要な場合、分割代入で追加のカンマを挿入すること:
>
> ```typescript
> const [a, , b] = [1, 5, 10];  // a <- 1, b <- 10
> ```

#### import

モジュール namespace import は `lowerCamelCase` であり、ファイルは `snake_case` である。正しい import はケーシングスタイルに一致しない:

```typescript
import * as fooBar from './foo_bar';
```

一部のライブラリはこのスキームに違反する namespace プレフィックスを使用するが、一般的なオープンソースでの使用により、違反の方がより読みやすくなる。現在の例外:

- [jquery](https://jquery.com/): `$` プレフィックス
- [threejs](https://threejs.org/): `THREE` プレフィックス

#### 定数

**イミュータブル**: `CONSTANT_CASE` は変更されないことを意図した値を示し、技術的にミュータブルな値（深くフリーズされていない）にも使用して**よい**。ユーザーに変更しないよう知らせるためである:

```typescript
const UNIT_SUFFIXES = {
  'milliseconds': 'ms',
  'seconds': 's',
};
// UNIT_SUFFIXES は JavaScript のルール上ミュータブルだが、
// 大文字はユーザーに変更しないことを知らせる
```

定数はクラスの `static readonly` プロパティでもよい:

```typescript
class Foo {
  private static readonly MY_SPECIAL_NUMBER = 5;

  bar() {
    return 2 * Foo.MY_SPECIAL_NUMBER;
  }
}
```

**グローバル**: モジュールレベルの宣言、モジュールレベルクラスの static フィールド、およびモジュールレベルの enum 値のみが `CONST_CASE` を使用して**よい**。値がプログラムのライフタイムで複数回インスタンス化される場合（ローカル関数変数、ネストされた関数スコープの static フィールド）、`lowerCamelCase` を使用すること:

```typescript
const MY_GLOBAL = 5;  // モジュールレベルの定数

function myFunction() {
  const myLocal = 10;  // ローカル定数（小文字）
}
```

値が interface を実装するアロー関数の場合、`lowerCamelCase` を使用して**よい**:

```typescript
const myHandler: EventHandler = (event) => {
  // ...
};
```

#### エイリアス

ローカルスコープのエイリアスの作成は、既存の識別子フォーマットを使用する。ローカルエイリアスはソースの命名/フォーマットに一致**しなければならない**。変数には `const` を使用し、クラスフィールドには `readonly` を使用すること:

> **注:** テンプレートに公開するエイリアスを作成する場合、適切なアクセス修飾子を適用すること。

```typescript
const {BrewStateEnum} = SomeType;
const CAPACITY = 5;

class Teapot {
  readonly BrewStateEnum = BrewStateEnum;
  readonly CAPACITY = CAPACITY;
}
```

---

## 型システム

### 型推論

コードはすべての型式（変数、フィールド、戻り値の型）について TypeScript コンパイラの型推論に依存して**よい**。

```typescript
const x = 15;  // 型が推論される
```

自明に推論される型の型アノテーションは省略すること: `string`、`number`、`boolean`、`RegExp` リテラル、または `new` 式で初期化される変数/パラメータ:

```typescript
// Bad: 'boolean' は可読性を助けない
const x: boolean = true;

// Bad: 'Set' は自明に推論される
const x: Set<string> = new Set();
```

ジェネリックパラメータが `unknown` として推論されることを防ぐ場合は、型を明示的に指定すること。例えば、空のコレクション:

```typescript
const x = new Set<string>();
```

複雑な式の場合、型アノテーションは可読性を助ける:

```typescript
// アノテーションなしでは 'value' の型を推論しにくい
const value = await rpc.getSomeValue().transform();

// 一目で 'value' の型がわかる
const value: string[] = await rpc.getSomeValue().transform();
```

アノテーションが必要かどうかはコードレビュアーが判断する。

#### 戻り値の型

関数/メソッドの戻り値の型アノテーションを含めるかどうかは著者に任される。レビュアーは複雑な戻り値の型を明確にするアノテーションを求めて**よい**。プロジェクトはローカルに戻り値の型を要求して**よい**が、これは一般的な TypeScript の要件ではない。

明示的な戻り値の型のメリット:

- 読者にとってより正確なドキュメント
- コード変更が戻り値の型に影響する場合、潜在的な型エラーをより早く発見できる

### undefined と null

TypeScript は `undefined` と `null` の型をサポートする。Nullable 型は union 型として構築する（`string|null`）。`undefined` も同様。特別な union 構文は存在しない。

TypeScript コードは不在値に `undefined` または `null` のいずれかを使用してよい。一般的な優先順位はない。多くの JavaScript API は `undefined` を使用する（例: `Map.get`）。多くの DOM/Google API は `null` を使用する（例: `Element.getAttribute`）。コンテキストが適切な不在値を決定する。

#### Nullable/undefined 型エイリアス

型エイリアスは union に `|null` や `|undefined` を含め**てはならない**。Nullable なエイリアスは null 値がアプリケーションの多くのレイヤーを通過することを示唆し、元の問題を曖昧にし、特定のクラス/interface の値がいつ不在になりうるかを不明確にする。

代わりに、コードはエイリアスが使用される場合にのみ `|null` や `|undefined` を追加**しなければならない**。コードは null 値が発生する場所の近くで処理**すべきである**:

```typescript
// Bad
type CoffeeResponse = Latte|Americano|undefined;

class CoffeeService {
  getLatte(): CoffeeResponse { ... }
}
```

```typescript
// Better
type CoffeeResponse = Latte|Americano;

class CoffeeService {
  getLatte(): CoffeeResponse|undefined { ... }
}
```

#### `|undefined` よりもオプショナルを推奨する

TypeScript にはオプショナルパラメータ/フィールドのための特別なオプショナル構文（`?`）がある:

```typescript
interface CoffeeOrder {
  sugarCubes: number;
  milk?: Whole|LowFat|HalfHalf;
}

function pourCoffee(volume?: Milliliter) { ... }
```

オプショナルパラメータは暗黙的に `|undefined` を含む。これらは異なる。値の構築やメソッドの呼び出し時に省略できる。例えば、`milk` がオプショナルであるため `{sugarCubes: 1}` は有効な `CoffeeOrder` である。

`|undefined` ではなく、オプショナルフィールド（interface/class）とパラメータを使用すること:

```typescript
// Good
interface CoffeeOrder {
  sugarCubes: number;
  milk?: Whole|LowFat|HalfHalf;
}

function pourCoffee(volume?: Milliliter) { ... }
```

クラスの場合、このパターンを避け、多くのフィールドを初期化することが望ましい:

```typescript
class MyClass {
  field = '';
}
```

### 構造型を使用する

TypeScript の型システムは名前的ではなく構造的である。値が必要なすべてのプロパティを一致する型で持っていれば、再帰的にその型に一致する。

構造ベースの実装を提供する場合、宣言時に型を明示的に含めるべきである（正確な型チェック/エラー報告を有効にする）:

```typescript
const foo: Foo = {
  a: 123,
  b: 'abc',
}
```

```typescript
// Bad: 型推論に依存
const badFoo = {
  a: 123,
  b: 'abc',
}
```

構造型には interface を使用し、クラスではない:

```typescript
interface Foo {
  a: number;
  b: string;
}

const foo: Foo = {
  a: 123,
  b: 'abc',
}
```

```typescript
// これはしない
class Foo {
  readonly a: number;
  readonly b: number;
}

const foo: Foo = {
  a: 123,
  b: 'abc',
}
```

**理由**: badFoo オブジェクトは推論に依存している。追加のフィールドが追加される可能性があり、型推論はオブジェクト自体を反映する。

badFoo を `Foo` 関数に渡すと宣言時ではなく呼び出しサイトでエラーになる。これはコードベース全体で interface サーフェスを変更する場合に有用である:

```typescript
interface Animal {
  sound: string;
  name: string;
}

function makeSound(animal: Animal) {}

// 'cat' は推論された型 '{sound: string}' を持つ
const cat = {
  sound: 'meow',
};

// 'cat' は型契約を満たさない、ここでエラー（宣言から離れている）
makeSound(cat);

// 'horse' は構造型を持ち、宣言時にエラーを表示
const horse: Animal = {
  sound: 'niegh',
};

// 'dog' は契約を満たす
const dog: Animal = {
  sound: 'bark',
  name: 'MrPickles',
};

makeSound(dog);
makeSound(horse);
```

### 型リテラルエイリアスよりも interface を推奨する

TypeScript は型式（プリミティブ、union、タプル、任意の型）に名前を付けるための[型エイリアス](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#type-aliases)をサポートする。

オブジェクト型を宣言する場合、型エイリアスではなく **interface** を使用すること:

```typescript
interface User {
  firstName: string;
  lastName: string;
}
```

```typescript
// これはしない
type User = {
  firstName: string,
  lastName: string,
}
```

**理由**: これらの形式はほぼ同等である。1つを選択することでバリエーションを防ぐ。さらに、[interface を推奨する興味深い技術的理由](https://ncjamieson.com/prefer-interfaces/)がある。TypeScript チームリーダーは次のように述べている: 「私の見解では、interface がモデル化できるものすべてに interface を使用すべきである。表示/パフォーマンスに関する多くの問題がある型エイリアスにはメリットがない。」

### `Array<T>` 型

単純な型（英数字 + ドット）の場合、`Array<T>` や `ReadonlyArray<T>` ではなく配列構文糖 `T[]` または `readonly T[]` を使用すること:

```typescript
let a: string[];
let b: readonly string[];
let c: ns.MyObj[];
```

単純な型の多次元非 `readonly` 配列の場合、構文糖を使用すること:

```typescript
let d: string[][];
let e: string[][][];
```

複雑な型の場合、長い形式を使用すること:

```typescript
let f: Array<{n: number, s: string}>;
let g: Array<string|number>;
let h: ReadonlyArray<string|number>;
```

これらのルールはネストの各レベルで適用される。複雑な型にネストされた単純な `T[]` は構文糖を使用する:

```typescript
let i: InjectionToken<string[]>;  // ネストされた構文糖
let j: ReadonlyArray<string[]>;
let k: Array<readonly string[]>;
```

```typescript
// これらはしない
let a: Array<string>;           // 構文糖の方が短い
let b: ReadonlyArray<string>;
let c: Array<ns.MyObj>;
let d: Array<string[]>;
let e: {n: number, s: string}[]; // 中括弧が読みにくい
let f: (string|number)[];         // 括弧も同様
let g: readonly (string | number)[];
let h: InjectionToken<Array<string>>;
let i: readonly string[][];
let j: (readonly string[])[];
```

### インデックス可能型 / インデックスシグネチャ

JavaScript では、オブジェクトが連想配列として一般的に使われる。そのようなオブジェクトは[インデックスシグネチャ](https://www.typescriptlang.org/docs/handbook/2/objects.html#index-signatures)で型付けする:

```typescript
const fileSizes: {[fileName: string]: number} = {};
fileSizes['readme.txt'] = 541;
```

キーには意味のあるラベルを提供すること（ラベルはドキュメンテーションのみ）:

```typescript
// これはしない
const users: {[key: string]: number} = ...;

// Better
const users: {[userName: string]: number} = ...;
```

> ES6 の `Map` と `Set` の使用を検討すること。JavaScript のオブジェクトには[驚くべき望ましくない動作](http://2ality.com/2012/01/objects-as-maps.html)があり、ES6 の型はより明示的に意図を伝える。`Map` は `string` 以外の型をキーにでき、`Set` は非 `string` 型を含む。

TypeScript の `Record<Keys, ValueType>` 型は、定義されたキーセットを持つ型を構築する。これはキーが静的に既知の連想配列とは異なる。以下の[マップ型/条件型](#マップ型と条件型)を参照。

### マップ型と条件型

TypeScript の[マップ型](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html)と[条件型](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html)は他の型に基づいて型を指定する。TypeScript の標準ライブラリには型演算子（`Record`、`Partial`、`Readonly` など）が含まれる。

これらの機能は簡潔な型と強力な型安全の抽象化を可能にする。しかしデメリットもある:

- 明示的なプロパティ/関係（interface、拡張）と比較して、型演算子は読者が式をメンタルに評価する必要がある。これにより、特に型推論やファイル間の式と組み合わせると、プログラムの理解が著しく困難になる。
- マップ型と条件型の評価モデル、特に推論を伴うものは、仕様が不十分で、常に理解されているわけでなく、TypeScript のバージョン間で頻繁に変更される。コードが正しいように見えて誤ってコンパイルされ、将来のサポートコストが増加する可能性がある。
- マップ型と条件型は、複雑/推論された型から型を導出する際に最も強力である。これは同時に、理解しにくいプログラムを作成する最も起こりやすい状況でもある。
- 一部のツールはこれらの機能で動作しない。IDE の "find references" は `Pick<T, Keys>` 内のプロパティを見つけない。Code Search はそれらをハイパーリンクしない。

**スタイルの推奨事項:**

- コードを表現する最も単純な型構築を常に使用すること
- 繰り返しや冗長性は、複雑な型式の長期的なコストよりもはるかに安価であることが多い
- マップ型と条件型は、上記を考慮した上で使用して**よい**

例えば、TypeScript の `Pick<T, Keys>` 型は `T` のサブセットの新しい型を作成するが、単純な interface 拡張の方が明確なことが多い:

```typescript
interface User {
  shoeSize: number;
  favoriteIcecream: string;
  favoriteChocolate: string;
}

// FoodPreferences は favoriteIcecream、favoriteChocolate を持ち、shoeSize を持たない
type FoodPreferences = Pick<User, 'favoriteIcecream'|'favoriteChocolate'>;
```

プロパティを書き出すのと同等:

```typescript
interface FoodPreferences {
  favoriteIcecream: string;
  favoriteChocolate: string;
}
```

重複を減らすために、`User` が `FoodPreferences` を拡張するか、食べ物の好みフィールドをネストすることができる:

```typescript
interface FoodPreferences { /* 上記と同じ */ }

interface User extends FoodPreferences {
  shoeSize: number;
  // 食べ物の好みを含む
}
```

ここでの interface はプロパティのグループ化を明示的にし、IDE サポートを改善し、より良い最適化を可能にし、間違いなくコードの理解を容易にする。

### `any` 型

TypeScript の `any` 型はすべての型のスーパー型かつサブ型であり、すべてのプロパティの参照解除を許可する。`any` は危険であり、深刻なプログラミングエラーを隠蔽し、静的型付けの価値を損なう。

**`any` を使用しないことを検討すること。** `any` を使いたい状況では、以下を検討する:

- [より具体的な型を提供する](#より具体的な型を提供する)
- [`unknown` を使用する](#any-の代わりに-unknown-を使用する)
- [lint 警告を抑制し、理由をドキュメントする](#any-の-lint-警告を抑制する)

#### より具体的な型を提供する

interface、インラインオブジェクト型、または型エイリアスを使用すること:

```typescript
// サーバーサイド JSON のために宣言された interface
declare interface MyUserJson {
  name: string;
  email: string;
}

// 反復的な型の型エイリアス
type MyType = number|string;

// 複雑な戻り値のインライン型
function getTwoThings(): {something: number, other: string} {
  // ...
  return {something, other};
}

// ライブラリが `any` と言うところでのジェネリック型
function nicestElement<T>(items: T[]): T {
  // items 内の最も素敵な要素を見つける
  // コードは T を制約できる。例: <T extends HTMLElement>
}
```

#### any の代わりに `unknown` を使用する

`any` は任意の型への代入と任意のプロパティの参照解除を許可する。多くの場合これは必要ない。ビルトインの `unknown` 型は未知の型をはるかに安全に表現する。任意のプロパティの参照解除を許可しない:

```typescript
// 任意の値（null/undefined を含む）を代入できるが、
// ナローイングまたはキャストなしでは使用できない
const val: unknown = value;
```

```typescript
// これはしない
const danger: any = value;
danger.whoops();  // 完全にチェックされないアクセス！
```

`unknown` 値を安全に使用するには、[型ガード](https://www.typescriptlang.org/docs/handbook/advanced-types.html#type-guards-and-differentiating-types)で型をナローイングすること:

```typescript
if (typeof val === 'string') {
  console.log(val.length);
}

if (val instanceof MyClass) {
  val.myMethod();
}
```

#### `any` の lint 警告を抑制する

`any` が正当な場合もある。例えば、テストでモックオブジェクトを構築する場合。lint 警告を抑制するコメントを追加し、理由をドキュメントすること:

```typescript
// このテストは BookService の部分的な実装のみ必要。見落とした場合、
// テストは明白に失敗する。これは意図的に安全でない部分的なモック。
// tslint:disable-next-line:no-any
const mockBookService = ({get() { return mockBook; }} as any) as BookService;

// ショッピングカートはこのテストでは使用されない
// tslint:disable-next-line:no-any
const component = new MyComponent(mockBookService, /* unused ShoppingCart */ null as any);
```

### `{}` 型

`{}` 型は_空の interface_ 型であり、プロパティを持たない interface を表す。空の interface は、指定されたプロパティがないため、任意の非 nullish 値を受け入れる:

```typescript
let player: {};

player = {
  health: 50,
};  // 許可される

console.log(player.health) // プロパティ 'health' は型 '{}' に存在しない
```

```typescript
function takeAnything(obj:{}) {
}

takeAnything({});
takeAnything({ a: 1, b: 2 });
```

Google3 コードはほとんどのユースケースで `{}` を使用**すべきでない**。`{}` は任意の非 nullish のプリミティブまたはオブジェクトを表し、めったに適切でない。以下を推奨する:

- `unknown`: `null`/`undefined` を含む任意の値を保持する。不透明な値には一般的により適切
- `Record<string, T>`: 辞書的なオブジェクトに適している。含まれる値の型 `T` を明示することで型安全を提供する（`T` は `unknown` でもよい）
- `object`: プリミティブを除外し、非 nullish の関数/オブジェクトのみを残す。プロパティの仮定はない

### タプル型

Pair 型を作りたくなった場合、代わりにタプル型を使用すること:

```typescript
// これはしない
interface Pair {
  first: string;
  second: string;
}

function splitInHalf(input: string): Pair {
  ...
  return {first: x, second: y};
}
```

```typescript
// Good
function splitInHalf(input: string): [string, string] {
  ...
  return [x, y];
}

// 以下のように使用
const [leftHalf, rightHalf] = splitInHalf('my string');
```

多くの場合、意味のあるプロパティ名の方が明確である。

interface が大げさすぎる場合、インラインオブジェクトリテラル型を使用すること:

```typescript
function splitHostPort(address: string): {host: string, port: number} {
  ...
}

// 以下のように使用
const address = splitHostPort(userAddress);
use(address.port);

// またはタプル的な動作のために分割代入
const {host, port} = splitHostPort(userAddress);
```

### ラッパー型

JavaScript プリミティブに関連するいくつかの型は使用すべきでない:

- `String`、`Boolean`、`Number` は小文字のプリミティブ `string`、`boolean`、`number` とは異なる。常に小文字を使用すること。
- `Object` は `{}` と `object` の両方に似ているが、わずかに緩い。`null`/`undefined` を除くすべてを含む型には `{}` を使用し、さらにプリミティブ（上記の3つに加えて `symbol`、`bigint`）を除外するには小文字の `object` を使用すること

ラッパー型をコンストラクタとして呼び出す（`new`）ことは決してしないこと。

### 戻り値の型のみのジェネリクス

戻り値の型のみのジェネリクスを持つ API の作成を避けること。戻り値の型のみのジェネリクスを持つ既存の API では、常にジェネリクスを明示的に指定すること。

---

## ツールチェーン要件

Google スタイルでは特定のツールを特定の方法で要求する。

### TypeScript コンパイラ

すべての TypeScript ファイルは標準ツールチェーンを使用して型チェックを通過しなければならない。

#### @ts-ignore

`@ts-ignore`、`@ts-expect-error`、`@ts-nocheck` を使用**してはならない**。

**理由**: 表面的には簡単に見えるが、通常はより直接的に修正可能なより大きな問題を示している。

`@ts-ignore` を使用してコンパイラエラーを抑制する場合、周囲のコードがどの型を見ているかを予測するのは困難である。多くの型エラーについて、[`any` の最適な使用方法](#any-型)のアドバイスが有用である。

ユニットテストで `@ts-expect-error` を使用して**よい**が、一般的には使用すべきでない。`@ts-expect-error` はすべてのエラーを抑制するため、過剰にマッチし、深刻なエラーを抑制しやすい。以下を検討すること:

- チェックされないランタイム値を処理する API のテスト時、説明コメント付きの期待される型または `any` へのキャストを追加すること。これにより抑制が単一の式に限定される。
- [`any` の lint 警告の抑制](#any-の-lint-警告を抑制する)と同様に lint 警告を抑制し、理由をドキュメントすること。

### 適合性

Google TypeScript には_適合性フレームワーク_ [tsetse](https://tsetse.info) と [tsec](https://github.com/google/tsec) が含まれる。

これらのルールは重要な制限（コードベースを壊すグローバルの定義）とセキュリティパターン（`eval` の使用、`innerHTML` への代入）を強制するか、コード品質を緩やかに改善する。

Google スタイルの TypeScript は、適用可能なグローバルまたはフレームワークローカルの適合性ルールに従わなければならない。

---

## コメントとドキュメンテーション

### JSDoc とコメント

2種類のコメントが存在する: JSDoc（`/** ... */`）と非 JSDoc（`// ...` または `/* ... */`）。

- ドキュメンテーション、つまりユーザーが読むべきコメントには `/** JSDoc */` コメントを使用すること
- 実装コメント、つまりコード実装のみに関するコメントには `// line comments` を使用すること

JSDoc はツール（エディタ、ドキュメント生成器）に理解される。通常のコメントは人間のためだけのものである。

### 複数行コメント

複数行コメントは囲むコードレベルでインデントする。複数の単一行コメント（`//` スタイル）を使用**しなければならず**、ブロックスタイル（`/* */`）ではない:

```typescript
// これは
// 問題ない
```

```typescript
// これらはしない
/*
 * これは複数の
 * 単一行コメントを
 * 使用すべき
 */

/* これは // を使用すべき */
```

コメントはアスタリスクや文字のボックスで囲まないこと。

### JSDoc の一般的な形式

基本的な JSDoc のフォーマット:

```typescript
/**
 * 通常に折り返される複数行の JSDoc テキスト。
 * @param arg 何かを行うための数値。
 */
function doSomething(arg: number) { ... }
```

または単一行:

```typescript
/** この短い jsdoc は関数を説明する。 */
function doSomething(arg: number) { ... }
```

単一行が複数行にオーバーフローする場合、`/**` と `*/` を独自の行に持つ複数行スタイルを使用**しなければならない**。

多くのツールはバリデーション/最適化のために JSDoc からメタデータを抽出するため、適切に形成されたコメントが必要である。

### Markdown

JSDoc は Markdown で書かれるが、必要に応じて HTML を使用して**よい**。

JSDoc を解析するツールはプレーンテキストのフォーマットを無視する。以下は:

```typescript
/**
 * 3つの要因に基づいて重みを計算する:
 *   送信アイテム
 *   受信アイテム
 *   最終タイムスタンプ
 */
```

次のようにレンダリングされる: "3つの要因に基づいて重みを計算する: 送信アイテム 受信アイテム 最終タイムスタンプ"

代わりに Markdown リストを書くこと:

```typescript
/**
 * 3つの要因に基づいて重みを計算する:
 *
 * - 送信アイテム
 * - 受信アイテム
 * - 最終タイムスタンプ
 */
```

### JSDoc タグ

Google スタイルは JSDoc タグのサブセットを許可する。ほとんどのタグは独自の行を占め、行の先頭で始まる。

```typescript
/**
 * "param" タグは独自の行を占め、結合してはならない。
 * @param left 左パラメータの説明。
 * @param right 右パラメータの説明。
 */
function add(left: number, right: number) { ... }
```

```typescript
/**
 * "param" タグは独自の行を占め、結合してはならない。
 * @param left @param right
 */
function add(left: number, right: number) { ... }
```

#### 許可されるタグ

以下のタグが許可される:

- `@abstract`（同義語: `@virtual`）
- `@access`（同義語: `@package`、`@private`、`@protected`、`@public`）
- `@deprecated`
- `@enum`
- `@example`
- `@experimental`
- `@fileoverview`
- `@final`
- `@param`（同義語: `@arg`、`@argument`）
- `@private`
- `@protected`
- `@public`
- `@returns`（同義語: `@return`）
- `@see`
- `@static`
- `@throws`（同義語: `@except`、`@exception`）
- `@type`
- `@typedef`

#### 禁止されるタグ

以下のタグは許可されない:

- `@augments` / `@extends`
- `@author`
- `@borrows`
- `@callback`
- `@classdesc`
- `@class` / `@constructor`
- `@constant` / `@const`
- `@copyright`
- `@default` / `@defaultvalue`
- `@desc`
- `@event`
- `@exports`
- `@external` / `@host`
- `@fires` / `@emits`
- `@function` / `@func` / `@method`
- `@global`
- `@hideconstructor`
- `@ignore`
- `@implements`
- `@inheritdoc`
- `@inner`
- `@interface`
- `@lends`
- `@license`
- `@link`
- `@member` / `@var`
- `@memberof` / `@memberof!`
- `@mixes`
- `@mixin`
- `@module`
- `@name`
- `@namespace`
- `@override`
- `@param` の型宣言
- `@property` / `@prop`
- `@readonly`
- `@requires`
- `@since`
- `@summary`
- `@this`
- `@todo`
- `@tutorial`
- `@unmodifiable`
- `@version`
- `@variation`
- `@yields` / `@yield`

### 行の折り返し

JSDoc の行の折り返しは、メインの行折り返しセクションで指定されたルールに従う。コメントは80文字で折り返す。

### すべてのトップレベル export をドキュメントする

すべてのトップレベル export は、エクスポートされるシンボルが何を表すかを説明する JSDoc コメントでドキュメント化**しなければならない**。実装の詳細を明確にするために必要に応じて他のコメントを含めるべきである。

```typescript
/** プロバイダーを実装するための抽象遅延。 */
export abstract class Provider {
  /**
   * このプロバイダーが変更通知を発行したときに呼び出される
   * 新しいリスナーコールバックを登録する。
   */
  abstract onChanged(callback: () => void): void;
}
```

### TypeScript と冗長なコメントを省略する

TypeScript がすでに宣言している情報を JSDoc に含めないこと。例えば、関数シグネチャで既に型が付けられているパラメータや戻り値の型をドキュメントしないこと:

```typescript
/**
 * 2つの数値の合計を計算する。
 */
function add(left: number, right: number): number {
  return left + right;
}
```

禁止:

```typescript
/**
 * 2つの数値の合計を計算する。
 * @param left 足す数値。
 * @param right 足す数値。
 * @returns 2つの数値の合計。
 */
function add(left: number, right: number): number {
  return left + right;
}
```

ただし、型がすでに説明するものを超えてパラメータの意味に関する追加の説明を提供するために `@param` 行を使用して**よい**。

```typescript
/**
 * 2つの数値の合計を計算する。
 * @param left 足す最初の数値。
 * @param right 足す2番目の数値。
 */
function add(left: number, right: number): number {
  return left + right;
}
```

### @override を使用しない

`@override` アノテーションを使用しないこと。TypeScript の `override` キーワードで十分であり、こちらが推奨される:

```typescript
class Parent {
  someMethod() { }
}

class Child extends Parent {
  override someMethod() { }
}
```

### 実際に情報を追加するコメントを書く

周囲のコードから読者がすぐに推論できる情報を超える情報を伝える JSDoc コメントを書くこと。例えば、_何を_ではなく_なぜ_を説明するコメント:

```typescript
/**
 * 返す前にクライアントの準備完了を待つ。これが必要なのは、
 * 基盤の接続が初期化中に失敗する可能性があるため、
 * クライアントの状態が有効であることを確認する必要があるからである。
 */
function waitForClientReady(): Promise<void> {
  // ...
}
```

禁止:

```typescript
/** Promise を返す。 */
function waitForClientReady(): Promise<void> {
  // ...
}
```

### パラメータプロパティのコメント

クラスがフィールドの宣言と初期化にパラメータプロパティを使用する場合、JSDoc は `@param` タグを使用すべきである。追加の説明テキストは標準の `@param` ドキュメンテーションを使用すること:

```typescript
/**
 * @param regularParameter 通常のパラメータ。
 * @param constructorParam これはコンストラクタパラメータかつフィールドである。
 */
constructor(regularParameter: string, private constructorParam: string) {}
```

### 関数呼び出し時のコメント

関数名とパラメータがすでに伝えていることを単に繰り返すコメントを書かないこと。ただし、驚くべきまたは非自明な意味合いを明確にするためにコメントが有用な場合がある:

```typescript
// Good: 非自明な意味合いを明確にする
element.appendChild(document.createTextNode('foo'));  // 引数はテキストとして扱われる
element.innerHTML = 'bar';  // 引数はマークアップとして解析される
```

```typescript
// Bad: 自明なことを述べるコメント
const value = getValue();  // 値を取得する
```
