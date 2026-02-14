# Markdown スタイルガイド

Markdown の魅力の多くは、プレーンテキストで書くだけで、美しくフォーマットされた出力が得られることにあります。次の著者のためにきれいな状態を保つために、あなたの Markdown は可能な限りシンプルで、コーパス全体と一貫性を持たせるべきです。

私たちは3つの目標のバランスを取ることを目指しています：

1. _ソーステキストが読みやすく、ポータブルであること。_
2. _Markdown コーパスが長期にわたり、チーム間で保守可能であること。_
3. _構文がシンプルで覚えやすいこと。_

## 最小限の実用的なドキュメント

少数の新鮮で正確なドキュメントのセットは、さまざまな状態で散在する「ドキュメント」の膨大で雑然とした集合よりも優れています。

**Markdown のやり方**は、エンジニアが自分のドキュメントのオーナーシップを持ち、テストを良好な状態に保つのと同じ情熱でドキュメントを最新に保つことを奨励します。これを目指してください。

*   本当に必要なものを特定する：リリースドキュメント、API ドキュメント、テストガイドライン。
*   古くなったものを頻繁に、少しずつ削除する。

## 「良い」は「最高」より良い

内部ドキュメントレビューの基準は、コードレビューの基準とは異なります。レビュアーは改善を求めるべきですが、一般的に、著者は常に「Better/Best ルール」を行使できるべきです。

素早いイテレーションはあなたの味方です。長期的な改善を得るには、短期的な改善を行う際に**著者が生産的であり続ける**必要があります。各 CL の基準を低く設定し、**より多くの CL** が実行されるようにしてください。

ドキュメント CL のレビュアーとして：

1.  合理的であれば、すぐに LGTM し、コメントが適切に修正されることを信頼する。
2.  曖昧なコメントを残すよりも、代替案を提案することを優先する。
3.  大幅な変更については、自分のフォローアップ CL を開始する。特に「あなたは_さらに_...すべきです」という形式のコメントは避けるようにする。
4.  まれに、CL が実際にドキュメントを悪化させる場合は、提出を保留する。著者にリバートを依頼しても構わない。

著者として：

1.  些細な議論にサイクルを無駄にしない。早めに譲歩して先に進む。
2.  必要に応じて Better/Best ルールを引用する。

## 大文字・小文字の使い分け

製品、ツール、バイナリの元の名前を、大文字・小文字を保持して使用してください。例：

```
# Markdown style guide

`Markdown` is a dead-simple platform for internal engineering documentation.
```

以下のようにはしないでください：

```
# markdown bad style guide example

`markdown` is a dead-simple platform for internal engineering documentation.
```

## ドキュメントのレイアウト

一般的に、ドキュメントは以下のレイアウトのバリエーションから恩恵を受けます：

```
# ドキュメントのタイトル

短い紹介文。

[TOC]

## トピック

コンテンツ。

## 関連項目

* https://link-to-more-info
```

1.  `# ドキュメントのタイトル`：最初の見出しはレベル1の見出しであるべきで、理想的にはファイル名と同じか、ほぼ同じであるべきです。最初のレベル1の見出しはページの `<title>` として使用されます。

2.  `author`：_オプション_。ドキュメントの所有権を主張したい場合や、非常に誇りに思っている場合は、タイトルの下に自分を追加してください。ただし、通常はリビジョン履歴で十分です。

3.  `短い紹介文。` トピックの概要を1〜3文で提供します。あなたの「Foo の拡張」ドキュメントに着地した、最も基本的な情報を当たり前と思っている完全な初心者を想像してください。「Foo とは何か？なぜそれを拡張するのか？」

4.  `[TOC]`：Gitiles のような目次をサポートするホスティングを使用している場合、短い紹介文の後に `[TOC]` を配置してください。[`[TOC]` ドキュメント](https://gerrit.googlesource.com/gitiles/+/HEAD/Documentation/markdown.md#Table-of-contents)を参照してください。

5.  `## トピック`：残りの見出しはレベル2から始めるべきです。

6.  `## 関連項目`：もっと知りたい、または必要な情報が見つからなかったユーザーのために、その他のリンクを末尾に配置してください。

## 目次

### `[TOC]` ディレクティブを使用する

すべてのコンテンツがラップトップの画面上部に収まる場合を除き、[`[TOC]` ディレクティブ](https://gerrit.googlesource.com/gitiles/+/HEAD/Documentation/markdown.md#Table-of-contents)を使用してください。

### `[TOC]` ディレクティブを紹介文の後に配置する

`[TOC]` ディレクティブはページの紹介文の後、最初の H2 見出しの前に配置してください。例えば：

```
# My Page

This is my introduction **before** the TOC.

[TOC]

## My first H2
```

```
# My Page

[TOC]

This is my introduction **after** the TOC where it should not be.

## My first H2
```

ドキュメントを視覚的に読むユーザーにとっては、`[TOC]` ディレクティブがどこに配置されていても問題ありません。Markdown は常にページの上部右側に目次を表示するからです。しかし、スクリーンリーダーやキーボード操作が関わる場合、`[TOC]` の配置は非常に重要です。

なぜなら、`[TOC]` は Markdown ファイル内でディレクティブを記述した場所に目次の HTML を DOM に挿入するからです。例えば、ファイルの一番下にディレクティブを配置すると、スクリーンリーダーはドキュメントの最後まで到達するまで目次を読み上げません。

## 文字行数制限

Markdown コンテンツは80文字の行数制限という慣習に従います。なぜか？それは私たちのほとんどがコードで行っていることだからです。

*   **ツール連携**：私たちのツールはすべてコードを中心に設計されているため、ドキュメントが類似のルールに従ってフォーマットされればされるほど、これらのツールはより良く機能します。例えば、Code Search はソフトラップしません。

*   **品質**：エンジニアが Markdown コンテンツの作成・編集時に使い慣れたコーディング習慣を多く使うほど、品質は向上します。Markdown は、私たちがすでに持っている優れたレビュー文化を活用します。

### 例外

80文字ルールの例外には以下が含まれます：

*   リンク
*   テーブル
*   見出し
*   コードブロック

これは、リンクを含む行は80列を超えることが許容されることを意味し、関連する句読点も同様です：

```
*   See the
    [foo docs](https://gerrit.googlesource.com/gitiles/+/HEAD/Documentation/markdown.md).
    and find the logfile.
```

ただし、リンクの前後のテキストは折り返されることに注意してください。

テーブルも長くなることがあります。ただし、[短く読みやすいテーブルを作成するためのベストプラクティス](#テーブル)があります。

```
Foo                                                                           | Bar | Baz
----------------------------------------------------------------------------- | --- | ---
Somehow-unavoidable-long-cell-filled-with-content-that-simply-refuses-to-wrap | Foo | Bar
```

## 末尾の空白

末尾の空白を使用しないでください。行を改行するには末尾のバックスラッシュを使用してください。

CommonMark の仕様では、行末の2つのスペースは `<br />` タグを生成するべきとされていますが、多くのディレクトリには末尾の空白をフラグするプレサブミットチェックがあり、IDE も自動的に末尾の空白を削除することが多いです。

末尾のスペースに頼る代わりに、バックスラッシュを控えめに使用してください：

```
For some reason I just really want a break here,\
though it's probably not necessary.
```

ベストプラクティスとして、`<br />` タグの使用を完全に避けてください。代わりに、ペアの改行に頼って段落タグを作成し、この標準的なアプローチに慣れてください。

## 見出し

### ATX スタイルの見出し

```
## Heading 2
```

以下のようにはしないでください：

```
Heading - do you remember what level? DO NOT DO THIS.
---------
```

`=` や `-` のアンダーラインを使った見出しは、維持するのが面倒で、残りの見出し構文と合いません。ATX スタイルの見出し（`#` を使う）を使用してください。

### 見出しにユニークで完全な名前を使用する

重複する見出しは避けてください。各見出しは一意で完全に説明的であるべきです。これにより、自動生成されるアンカーリンクが直感的になります。

以下のパターンは避けてください：

```
## Foo
### Summary
### Example
## Bar
### Summary
### Example
```

以下のパターンを推奨します：

```
## Foo
### Foo summary
### Foo example
## Bar
### Bar summary
### Bar example
```

### 見出しにスペースを追加する

`#` 記号の後にスペースを入れ、見出しの前後に空行を入れてください：

```
...text before.

## Heading 2

Text after...
```

以下のようにはしないでください：

```
...text before.

##Heading 2
Text after... DO NOT DO THIS.
```

### 単一の H1 見出しを使用する

ドキュメントのタイトルとして1つの H1 見出しを使用してください。それ以降の見出しは H2 以下にするべきです。

### タイトルと見出しの大文字・小文字の使い分け

タイトルと見出しの大文字・小文字については、[Google Developer Documentation Style Guide](https://developers.google.com/style/capitalization#capitalization-in-titles-and-headings) のガイダンスに従ってください。

## リスト

### 長いリストにはレイジーナンバリングを使用する

Markdown は十分にスマートで、結果の HTML で番号付きリストを正しくレンダリングします。変更される可能性のある長いリスト、特に長いネストされたリストには、「レイジー」ナンバリングを使用してください：

```
1.  Foo.
1.  Bar.
    1.  Foofoo.
    1.  Barbar.
1.  Baz.
```

ただし、変更が予想されない短い安定したリストには、完全に番号付けされたリストを使用してください。ソースで読みやすくなります：

```
1.  Foo.
2.  Bar.
3.  Baz.
```

### ネストされたリストのスペーシング

ネストされたリストでは、番号付きリストと箇条書きリストの両方で4スペースのインデントを使用してください：

番号付きリストの場合、アイテム番号の後に2つのスペースを使用し、テキスト自体が4スペースインデントされるようにします。折り返しテキストには4スペースのインデントを使用します：

```
1.  Use 2 spaces after the item number, so the text itself is indented 4 spaces.
    Use a 4-space indent for wrapped text.
2.  Use 2 spaces again for the next item.
```

箇条書きリストの場合、弾丸の後に3つのスペースを使用し、テキスト自体が4スペースインデントされるようにします：

```
*   Use 3 spaces after a bullet, so the text itself is indented 4 spaces.
    Use a 4-space indent for wrapped text.
    1.  Use 2 spaces with numbered lists, as before.
        Wrapped text in a nested list needs an 8-space indent.
    2.  Looks nice, doesn't it?
*   Back to the bulleted list, indented 3 spaces.
```

ネストがない場合でも、4スペースのインデントを使用することで、折り返しテキストのレイアウトが一貫します。

## コード

### インライン

`バッククォート`は`インラインコード`を指定し、そのままリテラルにレンダリングされます。短いコードの引用、フィールド名などに使用してください：

```
You'll want to run `really_cool_script.sh arg`.

Pay attention to the `foo_bar_whammy` field in that table.
```

特定のファイルではなく、一般的な意味でのファイルタイプにはインラインコードを使用してください：

```
Be sure to update your `README.md`!
```

### エスケープのためのコードスパンを使用する

通常の Markdown として処理されたくないテキスト（偽のパスや、不正な自動リンクにつながるサンプル URL など）にはバッククォートで囲んでください：

```
An example Markdown shortlink would be: `Markdown/foo/Markdown/bar.md`

An example query might be: `https://www.google.com/search?q=$TERM`
```

### コードブロック

1行以上のコード引用には、フェンスドコードブロックを使用してください。

#### 言語を宣言する

構文ハイライターも次の編集者も推測する必要がないように、言語を明示的に宣言するのがベストプラクティスです：

````
```python
def Foo(self, bar):
  self.bar = bar
```
````

#### インデントされたコードブロックの代わりにフェンスドコードブロックを使用する

4スペースのインデントもコードブロックとして解釈されます。しかし、すべてのコードブロックにフェンスドコードブロックを強く推奨します。インデントされたコードブロックには以下の欠点があります：

*   言語を指定できない。
*   ブロックの境界が曖昧。
*   Code Search での検索性が低い。

#### 改行をエスケープする

ほとんどのコマンドラインスニペットはターミナルに直接コピー＆ペーストされることを意図しているため、改行をエスケープするのがベストプラクティスです。行末に単一のバックスラッシュを使用してください：

```shell
$ bazel run :target -- --flag --foo=longlonglonglonglongvalue \
  --bar=anotherlonglonglonglonglonglonglonglonglonglongvalue
```

#### リスト内にコードブロックをネストする

リスト内にコードブロックをネストする場合は、リスト構造を維持するために適切にインデントしてください：

````
*   Bullet.

    ```c++
    int foo;
    ```

*   Next bullet.
````

または、リストのインデントから4スペース追加してインデントすることもできます。

## リンク

長いリンクはソースの Markdown を読みにくくし、80文字の折り返しを壊します。可能な限りリンクを短くしてください。

### Markdown 内のリンクには明示的なパスを使用する

Markdown ドキュメント内でリンクする場合は、完全な URL ではなく明示的なファイルパスを使用してください：

```
[...](/path/to/other/markdown/page.md)
```

以下のようにはしないでください：

```
[...](https://bad-full-url.example.com/path/to/other/markdown/page.md)
```

### 同じディレクトリ内でなければ相対パスを避ける

同じディレクトリ内であれば相対パスは安全に使用できます：

```
[...](other-page-in-same-dir.md)
[...](/path/to/another/dir/other-page.md)
```

ただし、ディレクトリをまたぐ `../` を使用した相対パスは避けてください。維持が難しく、ポータビリティを損ないます：

```
[...](../../bad/path/to/another/dir/other-page.md)
```

### 説明的な Markdown リンクタイトルを使用する

リンクテキストは説明的で意味のあるものにしてください。「here」や「link」のような一般的なフレーズではなく、文中で最も関連性の高いフレーズを自然にラップしてリンクを統合してください。

以下のようにはしないでください：

```
See the Markdown guide for more info: [link](markdown.md), or check out the
style guide [here](/styleguide/docguide/style.html).

Check out a typical test result:
[https://example.com/foo/bar](https://example.com/foo/bar).
```

以下のようにしてください：

```
See the [Markdown guide](markdown.md) for more info, or check out the
[style guide](/styleguide/docguide/style.html).

Check out a [typical test result](https://example.com/foo/bar).
```

### 参照リンク

#### 長いリンクには参照リンクを使用する

参照リンク構文は、リンクの使用と定義を分離するため、URL がテキストの流れを乱す場合に便利です：

```
See the [Markdown style guide][style], which has suggestions for making docs more
readable.

[style]: http://Markdown/corp/Markdown/docs/reference/style.md
```

URL の長さが可読性を損なう場合に参照リンクを使用してください。短いリンクにはインラインにしてください：

以下のようにはしないでください（短いリンクに参照リンクを使用する必要はありません）：

```
The [style guide][style_guide] says not to use reference links unless you have to.

[style_guide]: https://google.com/Markdown-style
```

以下のようにしてください（長いリンクに参照リンクを使用）：

```
The [style guide] says not to use reference links unless you have to.

[style guide]: https://docs.google.com/document/d/13HQBxfhCwx8lVRuN2Wf6poqvAfVeEXmFVcawP5I6B3c/edit
```

#### 重複を減らすために参照リンクを使用する

同じリンク先がドキュメント全体で複数回参照される場合、参照リンクを使用して繰り返しの URL 構文を排除してください。

#### 参照リンクは最初の使用箇所の後に定義する

参照リンクの定義は、最初に使用されたセクション内の次の見出しの直前に配置してください。これは、ドキュメントの末尾ではなく、セクションレベルで配置される脚注のようなアプローチです。

以下のようにはしないでください：

```
# Header FOR A BAD DOCUMENT

Some text with a [link][link_def].

Some more text with the same [link][link_def].

## Header 2

... lots of text ...

## Header 3

Some more text using a [different_link][different_link_def].

[link_def]: http://reallyreallyreallylonglink.com
[different_link_def]: http://differentreallyreallylonglink.com
```

以下のようにしてください：

```
# Header

Some text with a [link][link_def].

Some more text with the same [link][link_def].

[link_def]: http://reallyreallyreallylonglink.com

## Header 2

... lots of text ...

## Header 3

Some more text using a [different_link][different_link_def].

[different_link_def]: http://differentreallyreallylonglink.com
```

例外として、参照が複数のセクションにまたがって使用される場合は、コンテンツ更新時の孤立リンクを避けるため、ドキュメントの末尾に定義を配置してください。

## 画像

画像は控えめに使用し、シンプルなスクリーンショットを推奨します。何かを_説明する_よりも_見せる_方が簡単な場合に画像を使用してください（例えば、UI のナビゲーション方法を説明する場合）。

画像を説明する適切なテキストを必ず提供してください。視覚障害のある読者は画像を見ることができず、コンテンツを理解する必要があります。

## テーブル

テーブルは素早くスキャンする必要がある表形式のデータを提示するために使用してください。ただし、リストの方が適切な単純な情報にテーブルを誤用しないでください。

以下は問題のあるテーブルの例です：

```
Fruit  | Metrics      | Grows on | Acute curvature    | Attributes                                                                                                  | Notes
------ | ------------ | -------- | ------------------ | ----------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------
Apple  | Very popular | Trees    |                    | [Juicy](http://cs/SomeReallyReallyReallyReallyReallyReallyReallyReallyLongQuery), Firm, Sweet               | Apples keep doctors away.
Banana | Very popular | Trees    | 16 degrees average | [Convenient](http://cs/SomeDifferentReallyReallyReallyReallyReallyReallyReallyReallyLongQuery), Soft, Sweet | Contrary to popular belief, most apes prefer mangoes.
```

このテーブルの問題点は以下の通りです：

*   データ分布が不均一（多くのセルが空欄や共通値）。
*   次元が不均衡（属性列が非常に長い）。
*   テーブル形式に収まらない散文的なテキストが含まれている。

このような場合、リストの方がはるかに良い選択です：

```
## Fruits

Both types are highly popular, sweet, and grow on trees.

### Apple

*   [Juicy](http://SomeReallyReallyReallyReallyReallyReallyReallyReallyReallyReallyReallyReallyReallyReallyReallyReallyLongURL)
*   Firm

Apples keep doctors away.

### Banana

*   [Convenient](http://cs/SomeDifferentReallyReallyReallyReallyReallyReallyReallyReallyLongQuery)
*   Soft
*   16 degrees average acute curvature.

Contrary to popular belief, most apes prefer mangoes.
```

この方がより余裕があり、読者にとって必要な情報を見つけやすくなっています。

テーブルが効果的なのは、2つの次元にわたって比較的均一なデータ分布があり、異なる属性を持つ多数の並列アイテムがある場合です：

```
Transport        | Favored by     | Advantages
---------------- | -------------- | -----------------------------------------------
Swallow          | Coconuts       | [Fast when unladen][airspeed]
Bicycle          | Miss Gulch     | [Weatherproof][tornado_proofing]
X-34 landspeeder | Whiny farmboys | [Cheap][tosche_station] since the XP-38 came out

[airspeed]: http://google3/airspeed.h
[tornado_proofing]: http://google3/kansas/
[tosche_station]: http://google3/power_converter.h
```

テーブルセルを管理しやすくするために参照リンクが使用されていることに注目してください。

## Markdown を HTML より強く推奨する

可能な限り標準的な Markdown 構文を使用し、HTML のハックを避けてください。

HTML ハッキングのすべてが、Markdown コーパスの可読性とポータビリティを低下させます。これにより、他のツールとの統合の有用性が制限されます。

Gitiles は HTML をレンダリングしません。

Markdown はほぼすべてのニーズをすでに満たしています。
