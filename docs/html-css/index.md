# SATELLITE HTML/CSS スタイルガイド

## 背景

このドキュメントは、HTMLおよびCSSのフォーマットとスタイルに関するルールを定義するものである。コラボレーションの改善、コード品質の向上、およびサポートインフラの実現を目的としている。HTMLとCSS（SassファイルやGSSファイルを含む）を使用する、生の作業ファイルに適用される。ツールによる難読化、圧縮、コンパイルは、一般的なコード品質が維持される限り自由に行ってよい。

## 全般

### 全般的なスタイルルール

#### プロトコル

画像やその他のメディアファイル、スタイルシート、スクリプトには、それらのファイルがHTTPS経由で利用できない場合を除き、常にHTTPS（`https:`）を使用すること。

非推奨の例：

```html
<!-- プロトコルの省略 -->
<script src="//ajax.googleapis.com/ajax/libs/jquery/3.4.0/jquery.min.js"></script>

<!-- HTTPの使用 -->
<script src="http://ajax.googleapis.com/ajax/libs/jquery/3.4.0/jquery.min.js"></script>
```

```css
/* HTTPの使用 */
@import 'http://fonts.googleapis.com/css?family=Open+Sans';
```

推奨：

```html
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.0/jquery.min.js"></script>
```

```css
@import 'https://fonts.googleapis.com/css?family=Open+Sans';
```

#### インデント

一度に2スペースでインデントすること。タブを使用したり、タブとスペースを混在させたりしないこと。

```html
<ul>
  <li>Fantastic
  <li>Great
</ul>
```

```css
.example {
  color: blue;
}
```

#### 大文字・小文字

すべて小文字を使用すること。すべてのコードは小文字でなければならない。これはHTML要素名、属性、属性値（`text/CDATA`を除く）、CSSセレクタ、プロパティ、およびプロパティ値（文字列を除く）に適用される。

非推奨：

```html
<A HREF="/">Home</A>
```

```css
color: #E5E5E5;
```

推奨：

```html
<img src="google.png" alt="SATELLITE">
```

```css
color: #e5e5e5;
```

#### 末尾の空白

末尾の空白を削除すること。末尾の空白は不要であり、差分を複雑にする可能性がある。

非推奨：

```html
<p>What?_
```

推奨：

```html
<p>Yes please.
```

### 全般的なメタルール

#### エンコーディング

UTF-8（BOMなし）を使用すること。エディタがバイトオーダーマークなしのUTF-8を文字エンコーディングとして使用していることを確認すること。

HTMLテンプレートおよびドキュメントでは `<meta charset="utf-8">` を介してエンコーディングを指定すること。スタイルシートはUTF-8を前提としているため、エンコーディングを指定しないこと。

#### コメント

可能な限り、必要に応じてコードを説明すること。コメントを使用して、コードが何をカバーし、何の目的を果たし、なぜそのソリューションが使用または推奨されているのかを説明すること。

完全にドキュメント化されたコードを常に要求することは現実的な期待とはみなされないため、この項目は任意である。

#### アクション項目

TODOおよびアクション項目には `TODO` でマークすること。`@@` のような他の一般的なフォーマットではなく、`TODO` キーワードのみを使用してTODOを強調すること。`TODO: アクション項目` のように、コロンの後にアクション項目を追記すること。

```html
{# TODO: センタリングを再検討する。 #}
<center>Test</center>

<!-- TODO: オプショナルタグを削除する。 -->
<ul>
  <li>Apples</li>
  <li>Oranges</li>
</ul>
```

## HTML

### HTMLスタイルルール

#### ドキュメントタイプ

`<!doctype html>` を使用すること。ドキュメントの先頭に `<!doctype html>` を含めることで、常にHTMLを非互換モード（no-quirks mode）にすること。

doctypeのないドキュメントは「互換モード（quirks mode）」でレンダリングされ、異なるdoctypeを持つドキュメントは「限定互換モード（limited-quirks mode）」でレンダリングされる可能性がある。これらのモードは、さまざまなHTMLおよびCSSの基本的な構成要素について、広く理解され文書化されている動作に従わない。

#### HTMLの妥当性

可能な限り妥当なHTMLを使用すること。ファイルサイズに関するパフォーマンス目標のために他に達成不可能な場合を除き、妥当なHTMLコードを使用すること。

W3C HTMLバリデータなどのツールを使用してテストすること。妥当なHTMLの使用は、技術的な要件や制約を学ぶのに役立ち、適切なHTMLの使用を保証する、測定可能な基準品質属性である。

非推奨：

```html
<title>Test</title>
<article>This is only a test.
```

推奨：

```html
<!doctype html>
<meta charset="utf-8">
<title>Test</title>
<article>This is only a test.</article>
```

#### セマンティクス

HTMLをその目的に沿って使用すること。要素（誤って「タグ」と呼ばれることがある）は、それが作られた目的のために使用すること。例えば、見出しには見出し要素を、段落には `p` 要素を、アンカーには `a` 要素を使用する、など。

HTMLをその目的に沿って使用することは、アクセシビリティ、再利用性、およびコード効率の観点から重要である。

非推奨：

```html
<div onclick="goToRecommendations();">All recommendations</div>
```

推奨：

```html
<a href="recommendations/">All recommendations</a>
```

#### マルチメディアの代替コンテンツ

マルチメディアには代替コンテンツを提供すること。画像、動画、`canvas` によるアニメーションオブジェクトなどのマルチメディアについては、代替アクセス手段を提供すること。画像の場合は意味のある代替テキスト（`alt`）を使用し、動画および音声の場合はトランスクリプトとキャプション（利用可能な場合）を提供すること。

代替コンテンツを提供することはアクセシビリティの観点から重要である。視覚障害のあるユーザーは `@alt` がなければ画像が何についてのものかを判断する手がかりがほとんどなく、他のユーザーも動画や音声のコンテンツが何についてのものかを理解する手段がない場合がある。

`alt` 属性が冗長になる画像や、CSSですぐに対応できない純粋に装飾的な目的の画像については、`alt=""` のように代替テキストを使用しないこと。

非推奨：

```html
<img src="spreadsheet.png">
```

推奨：

```html
<img src="spreadsheet.png" alt="Spreadsheet screenshot.">
```

#### 関心の分離

構造とプレゼンテーションと振る舞いを分離すること。構造（マークアップ）、プレゼンテーション（スタイリング）、振る舞い（スクリプティング）を厳密に分離し、3つの間のインタラクションを最小限に抑えるよう努めること。

つまり、ドキュメントとテンプレートにはHTMLのみを含め、そのHTMLは構造的な目的のみに使用すること。プレゼンテーションに関するものはすべてスタイルシートに、振る舞いに関するものはすべてスクリプトに移動すること。

また、ドキュメントやテンプレートからリンクするスタイルシートとスクリプトをできるだけ少なくすることで、接触面を小さく保つこと。

構造とプレゼンテーションと振る舞いの分離は、メンテナンスの観点から重要である。HTMLドキュメントやテンプレートを変更するコストは、スタイルシートやスクリプトを更新するコストよりも常に高くつく。

非推奨：

```html
<!doctype html>
<title>HTML sucks</title>
<link rel="stylesheet" href="base.css" media="screen">
<link rel="stylesheet" href="grid.css" media="screen">
<link rel="stylesheet" href="print.css" media="print">
<h1 style="font-size: 1em;">HTML sucks</h1>
<p>I've read about this on a few sites but now I'm sure:
  <u>HTML is stupid!!1</u>
<center>I can't believe there's no way to control the styling of
  my website without doing everything all over again!</center>
```

推奨：

```html
<!doctype html>
<title>My first CSS-only redesign</title>
<link rel="stylesheet" href="default.css">
<h1>My first CSS-only redesign</h1>
<p>I've read about this on a few sites but today I'm actually
  doing it: separating concerns and avoiding anything in the HTML of
  my website that is presentational.
<p>It's awesome!
```

#### エンティティ参照

エンティティ参照を使用しないこと。ファイルやエディタ、チーム間で同じエンコーディング（UTF-8）が使用されていることを前提とすれば、`&mdash;`、`&rdquo;`、`&#x263a;` のようなエンティティ参照を使用する必要はない。

唯一の例外は、HTMLで特別な意味を持つ文字（`<` や `&` など）、および制御文字や「不可視」文字（ノーブレークスペースなど）に適用される。

非推奨：

```html
The currency symbol for the Euro is &ldquo;&eur;&rdquo;.
```

推奨：

```html
The currency symbol for the Euro is "€".
```

#### オプショナルタグ

オプショナルタグを省略すること（任意）。ファイルサイズの最適化と可読性の向上のために、オプショナルタグの省略を検討すること。HTML5仕様は、どのタグが省略可能かを定義している。

このアプローチは、Web開発者が通常教わる内容とは大きく異なるため、より広いガイドラインとして確立されるまでに猶予期間が必要になる場合がある。一貫性と簡潔性の理由から、一部だけではなく、すべてのオプショナルタグを省略することが最善である。

非推奨：

```html
<!doctype html>
<html>
  <head>
    <title>Spending money, spending bytes</title>
  </head>
  <body>
    <p>Sic.</p>
  </body>
</html>
```

推奨：

```html
<!doctype html>
<title>Saving money, saving bytes</title>
<p>Qed.
```

#### `type` 属性

スタイルシートとスクリプトの `type` 属性を省略すること。スタイルシート（CSSを使用しない場合を除く）およびスクリプト（JavaScriptを使用しない場合を除く）には `type` 属性を使用しないこと。

HTML5はデフォルトとして `text/css` および `text/javascript` を暗黙的に指定するため、これらのコンテキストで `type` 属性を指定する必要はない。これは古いブラウザでも安全に行うことができる。

非推奨：

```html
<link rel="stylesheet" href="https://www.google.com/css/maia.css"
    type="text/css">

<script src="https://www.google.com/js/gweb/analytics/autotrack.js"
    type="text/javascript"></script>
```

推奨：

```html
<link rel="stylesheet" href="https://www.google.com/css/maia.css">

<script src="https://www.google.com/js/gweb/analytics/autotrack.js"></script>
```

#### `id` 属性

不要な `id` 属性を避けること。スタイリングには `class` 属性を、スクリプティングには `data` 属性を優先すること。

`id` 属性が厳密に必要な場合は、JavaScriptの識別子構文と一致しないように、値にハイフンを含めること。例えば、`profile` や `userProfile` ではなく `user-profile` を使用すること。

要素に `id` 属性がある場合、ブラウザはそれをグローバル `window` プロトタイプの名前付きプロパティとして利用可能にするため、予期しない動作が発生する可能性がある。ハイフンを含む `id` 属性値はプロパティ名として利用可能だが、グローバルJavaScript変数として参照することはできない。

非推奨：

```html
<!-- `window.userProfile` が <div> ノードへの参照として解決される -->
<div id="userProfile"></div>
```

推奨：

```html
<!-- `id` 属性が必要であり、その値にはハイフンが含まれている -->
<div aria-describedby="user-profile">
  …
  <div id="user-profile"></div>
  …
</div>
```

### HTMLフォーマットルール

#### 一般的なフォーマット

すべてのブロック要素、リスト要素、テーブル要素には新しい行を使用し、それらの子要素はすべてインデントすること。

要素のスタイリングに関わらず（CSSは `display` プロパティにより要素に異なる役割を与えることができるため）、すべてのブロック要素、リスト要素、テーブル要素を新しい行に配置すること。

また、それらがブロック要素、リスト要素、テーブル要素の子要素である場合はインデントすること。

リスト項目間の空白に問題が発生する場合は、すべての `li` 要素を1行に配置することが許容される。

```html
<blockquote>
  <p><em>Space</em>, the final frontier.</p>
</blockquote>

<ul>
  <li>Moe
  <li>Larry
  <li>Curly
</ul>

<table>
  <thead>
    <tr>
      <th scope="col">Income
      <th scope="col">Taxes
  <tbody>
    <tr>
      <td>$ 5.00
      <td>$ 4.50
</table>
```

#### HTMLの行の折り返し

長い行を折り返すこと（任意）。HTMLにはカラム制限の推奨はないが、可読性が大幅に向上する場合は長い行の折り返しを検討すること。

行を折り返す場合、折り返された属性と子要素を区別するために、各継続行をインデントすること。プロジェクト内で一貫して行を折り返すこと。理想的には自動コードフォーマットツールによって強制されることが望ましい。

許容されるアプローチの例：

```html
<button
  mat-icon-button
  color="primary"
  class="menu-button"
  (click)="openMenu()"
>
  <mat-icon>menu</mat-icon>
</button>
```

```html
<button mat-icon-button color="primary" class="menu-button"
    (click)="openMenu()">
  <mat-icon>menu</mat-icon>
</button>
```

```html
<button
    mat-icon-button
    color="primary"
    class="menu-button"
    (click)="openMenu()">
  <mat-icon>menu</mat-icon>
</button>
```

```html
<button mat-icon-button
        color="primary"
        class="menu-button"
        (click)="openMenu()">
  <mat-icon>menu</mat-icon>
</button>
```

#### HTMLの引用符

属性値を引用するときはダブルクォーテーションを使用すること。属性値の周りにはシングルクォーテーション（`''`）ではなく、ダブルクォーテーション（`""`）を使用すること。

非推奨：

```html
<a class='maia-button maia-button-secondary'>Sign in</a>
```

推奨：

```html
<a class="maia-button maia-button-secondary">Sign in</a>
```

## CSS

### CSSスタイルルール

#### CSSの妥当性

可能な限り妥当なCSSを使用すること。CSSバリデータのバグへの対処やプロプライエタリな構文が必要な場合を除き、妥当なCSSコードを使用すること。

W3C CSSバリデータなどのツールを使用してテストすること。妥当なCSSの使用は、効果のないCSSコードを見つけて削除することを可能にし、適切なCSS使用を保証する、測定可能な基準品質属性である。

#### クラスの命名

意味のある名前または汎用的なクラス名を使用すること。プレゼンテーション的な名前や暗号的な名前ではなく、常に対象の要素の目的を反映するクラス名、またはそれ以外の汎用的なクラス名を使用すること。

具体的で要素の目的を反映する名前が最も理解しやすく、変更される可能性が最も低いため、優先されるべきである。

汎用的な名前は、兄弟要素と異なる特定の意味を持たない要素のための単なるフォールバックである。通常、「ヘルパー」として必要とされる。

機能的または汎用的な名前を使用することで、不要なドキュメントやテンプレートの変更の確率を減らすことができる。

非推奨：

```css
/* 意味のない名前 */
.yee-1901 {}

/* プレゼンテーション的な名前 */
.button-green {}
.clear {}
```

推奨：

```css
/* 具体的な名前 */
.gallery {}
.login {}
.video {}

/* 汎用的な名前 */
.aux {}
.alt {}
```

#### クラス名のスタイル

クラス名はできるだけ短く、しかし必要なだけの長さにすること。クラスが何についてのものかを伝えつつ、できるだけ簡潔にすること。

このようにクラス名を使用することで、許容可能なレベルの理解しやすさとコード効率に貢献する。

非推奨：

```css
.navigation {}
.atr {}
```

推奨：

```css
.nav {}
.author {}
```

#### クラス名の区切り文字

クラス名の単語はハイフンで区切ること。理解しやすさと可読性を向上させるために、セレクタ内の単語や略語をハイフン以外の文字（文字なしを含む）で結合しないこと。

非推奨：

```css
.demoimage {}

.error_status {}
```

推奨：

```css
.video-id {}
.ads-sample {}
```

#### プレフィックス

セレクタにアプリケーション固有のプレフィックスを付けること（任意）。大規模プロジェクト、および他のプロジェクトに埋め込まれるコードや外部サイトで使用されるコードでは、クラス名にプレフィックス（名前空間として）を使用すること。短く一意の識別子の後にダッシュを使用すること。

名前空間を使用することで、命名の衝突を防ぎ、検索や置換操作などでメンテナンスを容易にすることができる。

```css
.adw-help {} /* AdWords */
.maia-note {} /* Maia */
```

#### タイプセレクタ

クラス名をタイプセレクタで修飾することを避けること。必要な場合（例えばヘルパークラスの場合）を除き、クラスと組み合わせて要素名を使用しないこと。

不要な祖先セレクタを避けることは、パフォーマンスの観点から有用である。

非推奨：

```css
ul.example {}
div.error {}
```

推奨：

```css
.example {}
.error {}
```

#### IDセレクタ

IDセレクタを避けること。ID属性はページ全体で一意であることが期待されるが、多くの異なるエンジニアが作業する多数のコンポーネントを含むページでは、それを保証することが困難である。すべての状況でクラスセレクタを優先すべきである。

非推奨：

```css
#example {}
```

推奨：

```css
.example {}
```

#### ショートハンドプロパティ

可能な限りショートハンドプロパティを使用すること。CSSは `font` のようなさまざまなショートハンドプロパティを提供しており、1つの値のみが明示的に設定されている場合でも、可能な限り使用すべきである。

ショートハンドプロパティの使用は、コード効率と理解しやすさの観点から有用である。

非推奨：

```css
border-top-style: none;
font-family: palatino, georgia, serif;
font-size: 100%;
line-height: 1.6;
padding-bottom: 2em;
padding-left: 1em;
padding-right: 1em;
padding-top: 0;
```

推奨：

```css
border-top: 0;
font: 100%/1.6 palatino, georgia, serif;
padding: 0 1em 2em;
```

#### 0と単位

「0」値の後には単位の指定を省略すること（必須の場合を除く）。必須でない限り、`0` 値の後に単位を使用しないこと。

```css
flex: 0px; /* このflex-basisコンポーネントは単位を必要とする。 */
flex: 1 1 0px; /* 単位がなくても曖昧ではないが、IE11では必要。 */
margin: 0;
padding: 0;
```

#### 先頭の0

値には常に先頭の「0」を含めること。-1から1の間の値または長さの前に `0` を付けること。

```css
font-size: 0.8em;
```

#### 16進数表記

可能な場合は3文字の16進数表記を使用すること。許容されるカラー値については、3文字の16進数表記がより短く簡潔である。

非推奨：

```css
color: #eebbcc;
```

推奨：

```css
color: #ebc;
```

#### `!important` 宣言

`!important` 宣言の使用を避けること。これらの宣言はCSSの自然なカスケードを壊し、スタイルの推論や組み合わせを困難にする。代わりにセレクタの詳細度を使用してプロパティをオーバーライドすること。

非推奨：

```css
.example {
  font-weight: bold !important;
}
```

推奨：

```css
.example {
  font-weight: bold;
}
```

#### ハック

ユーザーエージェント検出やCSS「ハック」を避けること。まず別のアプローチを試すこと。

ユーザーエージェント検出や特殊なCSSフィルタ、回避策、ハックによってスタイリングの差異に対処したくなるものである。しかし、効率的で管理しやすいコードベースを実現し維持するためには、どちらのアプローチも最終手段とすべきである。別の言い方をすれば、検出やハックに自由なパスを与えることは、長期的にはプロジェクトに害を及ぼす。プロジェクトは最も抵抗の少ない道を選ぶ傾向があるためである。つまり、検出やハックの使用を許可し容易にすることは、検出やハックをより頻繁に使用することを意味し、より頻繁とは多すぎるということである。

### CSSフォーマットルール

#### 宣言の順序

宣言をアルファベット順に並べること（任意）。プロジェクト内で宣言を一貫して並べ替えること。一貫した並べ替え順序を自動化し強制するツールがない場合は、学習しやすく、覚えやすく、手動で維持しやすい一貫したコードを実現するために、宣言をアルファベット順に並べることを検討すること。

ベンダー固有のプレフィックスは並べ替えの目的では無視すること。ただし、特定のCSSプロパティに対する複数のベンダー固有のプレフィックスはソートされた状態を維持すること（例：-mozプレフィックスは-webkitの前に来る）。

```css
background: fuchsia;
border: 1px solid;
-moz-border-radius: 4px;
-webkit-border-radius: 4px;
border-radius: 4px;
color: black;
text-align: center;
text-indent: 2em;
```

#### ブロック内容のインデント

すべてのブロック内容をインデントすること。ルール内のルールおよび宣言を含むすべてのブロック内容をインデントし、階層を反映させ理解を向上させること。

```css
@media screen, projection {

  html {
    background: #fff;
    color: #444;
  }

}
```

#### 宣言の終端

すべての宣言の後にセミコロンを使用すること。一貫性と拡張性の理由から、すべての宣言をセミコロンで終了すること。

非推奨：

```css
.test {
  display: block;
  height: 100px
}
```

推奨：

```css
.test {
  display: block;
  height: 100px;
}
```

#### プロパティ名の終端

プロパティ名のコロンの後にスペースを使用すること。一貫性の理由から、プロパティと値の間には常に1つのスペースを使用すること（ただしプロパティとコロンの間にはスペースを入れないこと）。

非推奨：

```css
h3 {
  font-weight:bold;
}
```

推奨：

```css
h3 {
  font-weight: bold;
}
```

#### 宣言ブロックの区切り

最後のセレクタと宣言ブロックの間にスペースを使用すること。最後のセレクタと宣言ブロックを開始する開き波括弧の間には常に1つのスペースを使用すること。

開き波括弧は、指定されたルールの最後のセレクタと同じ行に配置すること。

非推奨：

```css
.video{
  margin-top: 1em;
}

.video
{
  margin-top: 1em;
}
```

推奨：

```css
.video {
  margin-top: 1em;
}
```

#### セレクタと宣言の区切り

セレクタと宣言は改行で区切ること。各セレクタと宣言は常に新しい行で開始すること。

非推奨：

```css
a:focus, a:active {
  position: relative; top: 1px;
}
```

推奨：

```css
h1,
h2,
h3 {
  font-weight: normal;
  line-height: 1.2;
}
```

#### ルールの区切り

ルールは改行で区切ること。ルール間には常に空行（2つの改行）を入れること。

```css
html {
  background: #fff;
}

body {
  margin: auto;
  width: 50%;
}
```

#### CSSの引用符

属性セレクタやプロパティ値には、ダブルクォーテーション（`""`）ではなくシングルクォーテーション（`''`）を使用すること。

URI値（`url()`）には引用符を使用しないこと。

例外：`@charset` ルールを使用する必要がある場合は、ダブルクォーテーションを使用すること。シングルクォーテーションは許可されていない。

非推奨：

```css
@import url("https://www.google.com/css/maia.css");

html {
  font-family: "open sans", arial, sans-serif;
}
```

推奨：

```css
@import url(https://www.google.com/css/maia.css);

html {
  font-family: 'open sans', arial, sans-serif;
}
```

### CSSメタルール

#### セクションコメント

セクションコメントでセクションをグループ化すること（任意）。可能であれば、コメントを使用してスタイルシートのセクションをグループ化すること。セクションは改行で区切ること。

```css
/* Header */

.adw-header {}

/* Footer */

.adw-footer {}

/* Gallery */

.adw-gallery {}
```

## 最後に

**一貫性を保つこと。**

コードを編集する場合は、数分かけて周囲のコードを見てそのスタイルを判断すること。もしすべての算術演算子の周りにスペースが使われていれば、あなたもそうすべきである。もしコメントの周りにハッシュマークの小さなボックスがあれば、あなたのコメントにもハッシュマークの小さなボックスを付けるべきである。

スタイルガイドラインを持つ意義は、コーディングの共通語彙を持つことで、人々が「何を言っているか」ではなく「どう言っているか」に集中できるようにすることである。ここではグローバルなスタイルルールを提示しているが、ローカルなスタイルも重要である。ファイルに追加するコードが既存のコードとあまりにもかけ離れて見える場合、読者がそのコードを読む際にリズムが崩れてしまう。これを避けること。
