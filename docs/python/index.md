<!--
AUTHORS:
外部テキストでは GitHub-flavored Markdown のみを使用してください。
詳細は README.md を参照してください。
-->

# SATELLITE Python スタイルガイド

<!-- markdown="1" は GitHub Pages が目次を適切にレンダリングするために必要です。 -->

<details markdown="1">
  <summary>目次</summary>

-   [1 背景](#s1-background)
-   [2 Python 言語のルール](#s2-python-language-rules)
    *   [2.1 Lint](#s2.1-lint)
    *   [2.2 インポート](#s2.2-imports)
    *   [2.3 パッケージ](#s2.3-packages)
    *   [2.4 例外](#s2.4-exceptions)
    *   [2.5 ミュータブルなグローバル状態](#s2.5-global-variables)
    *   [2.6 ネストされた/ローカル/内部クラスと関数](#s2.6-nested)
    *   [2.7 内包表記とジェネレータ式](#s2.7-comprehensions)
    *   [2.8 デフォルトイテレータと演算子](#s2.8-default-iterators-and-operators)
    *   [2.9 ジェネレータ](#s2.9-generators)
    *   [2.10 Lambda 関数](#s2.10-lambda-functions)
    *   [2.11 条件式](#s2.11-conditional-expressions)
    *   [2.12 デフォルト引数値](#s2.12-default-argument-values)
    *   [2.13 プロパティ](#s2.13-properties)
    *   [2.14 True/False の評価](#s2.14-truefalse-evaluations)
    *   [2.16 レキシカルスコーピング](#s2.16-lexical-scoping)
    *   [2.17 関数とメソッドのデコレータ](#s2.17-function-and-method-decorators)
    *   [2.18 スレッド処理](#s2.18-threading)
    *   [2.19 パワー機能](#s2.19-power-features)
    *   [2.20 モダン Python: from \_\_future\_\_ imports](#s2.20-modern-python)
    *   [2.21 型アノテーション付きコード](#s2.21-type-annotated-code)
-   [3 Python スタイルのルール](#s3-python-style-rules)
    *   [3.1 セミコロン](#s3.1-semicolons)
    *   [3.2 行の長さ](#s3.2-line-length)
    *   [3.3 括弧](#s3.3-parentheses)
    *   [3.4 インデント](#s3.4-indentation)
        +   [3.4.1 アイテムシーケンスの末尾カンマ](#s3.4.1-trailing-commas)
    *   [3.5 空行](#s3.5-blank-lines)
    *   [3.6 空白](#s3.6-whitespace)
    *   [3.7 シバン行](#s3.7-shebang-line)
    *   [3.8 コメントとドキュメント文字列](#s3.8-comments-and-docstrings)
        +   [3.8.1 ドキュメント文字列](#s3.8.1-comments-in-doc-strings)
        +   [3.8.2 モジュール](#s3.8.2-comments-in-modules)
        +   [3.8.2.1 テストモジュール](#s3.8.2.1-test-modules)
        +   [3.8.3 関数とメソッド](#s3.8.3-functions-and-methods)
        +   [3.8.3.1 オーバーライドされたメソッド](#s3.8.3.1-overridden-methods)
        +   [3.8.4 クラス](#s3.8.4-comments-in-classes)
        +   [3.8.5 ブロックコメントとインラインコメント](#s3.8.5-block-and-inline-comments)
        +   [3.8.6 句読点、スペル、文法](#s3.8.6-punctuation-spelling-and-grammar)
    *   [3.10 文字列](#s3.10-strings)
        +   [3.10.1 ロギング](#s3.10.1-logging)
        +   [3.10.2 エラーメッセージ](#s3.10.2-error-messages)
    *   [3.11 ファイル、ソケット、および類似のステートフルリソース](#s3.11-files-sockets-closeables)
    *   [3.12 TODO コメント](#s3.12-todo-comments)
    *   [3.13 インポートのフォーマット](#s3.13-imports-formatting)
    *   [3.14 文](#s3.14-statements)
    *   [3.15 アクセサ](#s3.15-accessors)
    *   [3.16 命名規則](#s3.16-naming)
        +   [3.16.1 避けるべき名前](#s3.16.1-names-to-avoid)
        +   [3.16.2 命名規約](#s3.16.2-naming-conventions)
        +   [3.16.3 ファイルの命名](#s3.16.3-file-naming)
        +   [3.16.4 Guido の推奨から導かれたガイドライン](#s3.16.4-guidelines-derived-from-guidos-recommendations)
    *   [3.17 Main](#s3.17-main)
    *   [3.18 関数の長さ](#s3.18-function-length)
    *   [3.19 型アノテーション](#s3.19-type-annotations)
        +   [3.19.1 一般的なルール](#s3.19.1-general-rules)
        +   [3.19.2 行の折り返し](#s3.19.2-line-breaking)
        +   [3.19.3 前方宣言](#s3.19.3-forward-declarations)
        +   [3.19.4 デフォルト値](#s3.19.4-default-values)
        +   [3.19.5 NoneType](#s3.19.5-nonetype)
        +   [3.19.6 型エイリアス](#s3.19.6-type-aliases)
        +   [3.19.7 型の無視](#s3.19.7-ignoring-types)
        +   [3.19.8 型付け変数](#s3.19.8-typing-variables)
        +   [3.19.9 タプル vs リスト](#s3.19.9-tuples-vs-lists)
        +   [3.19.10 型変数](#s3.19.10-typevars)
        +   [3.19.11 文字列型](#s3.19.11-string-types)
        +   [3.19.12 型付けのためのインポート](#s3.19.12-imports-for-typing)
        +   [3.19.13 条件付きインポート](#s3.19.13-conditional-imports)
        +   [3.19.14 循環依存](#s3.19.14-circular-dependencies)
        +   [3.19.15 ジェネリクス](#s3.19.15-generics)
        +   [3.19.16 ビルド依存関係](#s3.19.16-build-dependencies)
-   [4 最後に](#4-parting-words)

</details>

<a id="s1-background"></a>
<a id="1-background"></a>

<a id="background"></a>
## 1 背景

Python は SATELLITE で使用される主要な動的言語です。このスタイルガイドは、Python プログラムにおける*やるべきこと・やってはいけないこと*のリストです。

コードを正しくフォーマットするために、[Vim 用の設定ファイル](google_python_style.vim)を作成しました。Emacs については、デフォルト設定で問題ありません。

多くのチームが、フォーマットに関する議論を避けるために [Black](https://github.com/psf/black) や [Pyink](https://github.com/google/pyink) 自動フォーマッタを使用しています。


<a id="s2-python-language-rules"></a>
<a id="2-python-language-rules"></a>

<a id="python-language-rules"></a>
## 2 Python 言語のルール

<a id="s2.1-lint"></a>
<a id="21-lint"></a>

<a id="lint"></a>
### 2.1 Lint

この [pylintrc](https://google.github.io/styleguide/pylintrc) を使用して、コードに対して `pylint` を実行してください。

<a id="s2.1.1-definition"></a>
<a id="211-definition"></a>

<a id="lint-definition"></a>
#### 2.1.1 定義

`pylint` は Python ソースコード内のバグとスタイルの問題を検出するツールです。C や C++ のような動的でない言語では通常コンパイラが検出するような問題を見つけます。Python の動的な性質のため、一部の警告は不正確な場合がありますが、誤った警告はかなりまれなはずです。

<a id="s2.1.2-pros"></a>
<a id="212-pros"></a>

<a id="lint-pros"></a>
#### 2.1.2 長所

タイプミス、代入前の変数使用など、見落としやすいエラーを検出します。

<a id="s2.1.3-cons"></a>
<a id="213-cons"></a>

<a id="lint-cons"></a>
#### 2.1.3 短所

`pylint` は完璧ではありません。利点を活かすために、時には回避策を書いたり、警告を抑制したり、修正したりする必要があります。

<a id="s2.1.4-decision"></a>
<a id="214-decision"></a>

<a id="lint-decision"></a>
#### 2.1.4 結論

コードに対して `pylint` を実行してください。

不適切な警告は抑制し、他の問題が隠れないようにしてください。警告を抑制するには、行レベルのコメントを設定できます:

```python
def do_PUT(self):  # WSGI name, so pylint: disable=invalid-name
  ...
```

`pylint` の警告はそれぞれシンボリック名（`empty-docstring`）で識別されます。SATELLITE 固有の警告は `g-` で始まります。

抑制の理由がシンボリック名からは明らかでない場合は、説明を追加してください。

この方法で抑制することの利点は、抑制を簡単に検索して再検討できることです。

`pylint` の警告の一覧は以下のコマンドで取得できます:

```shell
pylint --list-msgs
```

特定のメッセージの詳細情報を取得するには:

```shell
pylint --help-msg=invalid-name
```

非推奨の古い形式 `pylint: disable-msg` よりも `pylint: disable` を優先してください。

未使用の引数の警告は、関数の先頭で変数を削除することで抑制できます。削除する理由を説明するコメントを必ず含めてください。「Unused.」で十分です。例:

```python
def viking_cafe_order(spam: str, beans: str, eggs: str | None = None) -> str:
    del beans, eggs  # Unused by vikings.
    return spam + spam + spam
```

この警告を抑制する他の一般的な方法には、未使用の引数の識別子として '`_`' を使用する、引数名の前に '`unused_`' を付ける、または '`_`' に代入する方法があります。これらの形式は許可されていますが、もはや推奨されていません。これらは名前で引数を渡す呼び出し元を壊し、引数が実際に未使用であることを強制しません。

<a id="s2.2-imports"></a>
<a id="22-imports"></a>

<a id="imports"></a>
### 2.2 インポート

`import` 文はパッケージとモジュールにのみ使用し、個々の型、クラス、関数には使用しないでください。

<a id="s2.2.1-definition"></a>
<a id="221-definition"></a>

<a id="imports-definition"></a>
#### 2.2.1 定義

あるモジュールから別のモジュールへコードを共有するための再利用メカニズムです。

<a id="s2.2.2-pros"></a>
<a id="222-pros"></a>

<a id="imports-pros"></a>
#### 2.2.2 長所

名前空間管理の規約はシンプルです。各識別子のソースが一貫した方法で示されます。`x.Obj` は、オブジェクト `Obj` がモジュール `x` で定義されていることを示します。

<a id="s2.2.3-cons"></a>
<a id="223-cons"></a>

<a id="imports-cons"></a>
#### 2.2.3 短所

モジュール名が衝突する可能性があります。一部のモジュール名は不便なほど長いです。

<a id="s2.2.4-decision"></a>
<a id="224-decision"></a>

<a id="imports-decision"></a>
#### 2.2.4 結論

*   パッケージとモジュールのインポートには `import x` を使用してください。
*   `from x import y` を使用してください。ここで `x` はパッケージのプレフィックスで、`y` はプレフィックスなしのモジュール名です。
*   以下のいずれかの状況では `from x import y as z` を使用してください:
    -   `y` という名前のモジュールが2つインポートされる場合。
    -   `y` が現在のモジュールで定義されたトップレベルの名前と衝突する場合。
    -   `y` がパブリック API の一部である一般的なパラメータ名と衝突する場合（例: `features`）。
    -   `y` が不便なほど長い名前の場合。
    -   `y` がコードのコンテキストで汎用的すぎる場合（例: `from storage.file_system import options as fs_options`）。
*   `import y as z` は `z` が標準的な略語の場合にのみ使用してください（例: `import numpy as np`）。

例えば、モジュール `sound.effects.echo` は以下のようにインポートできます:

```python
from sound.effects import echo
...
echo.EchoFilter(input, output, delay=0.7, atten=4)
```

インポートで相対名を使用しないでください。モジュールが同じパッケージ内にある場合でも、完全なパッケージ名を使用してください。これにより、パッケージを意図せず2回インポートすることを防ぐことができます。

<a id="imports-exemptions"></a>
##### 2.2.4.1 例外

このルールの例外:

*   以下のモジュールからのシンボルは、静的解析と型チェックをサポートするために使用されます:
    *   [`typing` モジュール](#typing-imports)
    *   [`collections.abc` モジュール](#typing-imports)
    *   [`typing_extensions` モジュール](https://github.com/python/typing_extensions/blob/main/README.md)
*   [six.moves モジュール](https://six.readthedocs.io/#module-six.moves)からのリダイレクト。

<a id="s2.3-packages"></a>
<a id="23-packages"></a>

<a id="packages"></a>
### 2.3 パッケージ

各モジュールは、モジュールの完全なパス名を使用してインポートしてください。

<a id="s2.3.1-pros"></a>
<a id="231-pros"></a>

<a id="packages-pros"></a>
#### 2.3.1 長所

モジュール名の衝突や、モジュール検索パスが作者の期待通りでないことによる不正なインポートを回避できます。モジュールの検索が容易になります。

<a id="s2.3.2-cons"></a>
<a id="232-cons"></a>

<a id="packages-cons"></a>
#### 2.3.2 短所

パッケージ階層を複製する必要があるため、コードのデプロイが難しくなります。ただし、最新のデプロイメカニズムでは実際には問題になりません。

<a id="s2.3.3-decision"></a>
<a id="233-decision"></a>

<a id="packages-decision"></a>
#### 2.3.3 結論

すべての新しいコードは、各モジュールを完全なパッケージ名でインポートすべきです。

インポートは以下のようにすべきです:

```python
Yes:
  # コード内で完全な名前で absl.flags を参照する（冗長）。
  import absl.flags
  from doctor.who import jodie

  _FOO = absl.flags.DEFINE_string(...)
```

```python
Yes:
  # コード内でモジュール名のみで flags を参照する（一般的）。
  from absl import flags
  from doctor.who import jodie

  _FOO = flags.DEFINE_string(...)
```

*（このファイルが `doctor/who/` にあり、`jodie.py` も同じ場所にあると仮定）*

```python
No:
  # 作者がどのモジュールを意図し、何がインポートされるか不明確。実際の
  # インポート動作は sys.path を制御する外部要因に依存する。
  # 作者はどの jodie モジュールをインポートするつもりだったのか？
  import jodie
```

メインバイナリが配置されているディレクトリが `sys.path` にあると仮定すべきではありません（一部の環境ではそうなりますが）。この場合、コードは `import jodie` がローカルの `jodie.py` ではなく、サードパーティまたはトップレベルの `jodie` という名前のパッケージを指すと仮定すべきです。


<a id="s2.4-exceptions"></a>
<a id="24-exceptions"></a>

<a id="exceptions"></a>
### 2.4 例外

例外は許可されていますが、注意して使用する必要があります。

<a id="s2.4.1-definition"></a>
<a id="241-definition"></a>

<a id="exceptions-definition"></a>
#### 2.4.1 定義

例外は、エラーやその他の例外的な条件を処理するために、通常の制御フローから抜け出す手段です。

<a id="s2.4.2-pros"></a>
<a id="242-pros"></a>

<a id="exceptions-pros"></a>
#### 2.4.2 長所

通常の操作コードの制御フローがエラー処理コードで散らかることがありません。また、特定の条件が発生したときに制御フローが複数のフレームをスキップできます。例えば、エラーコードを伝播させる代わりに、N 個のネストされた関数から一度に戻ることができます。

<a id="s2.4.3-cons"></a>
<a id="243-cons"></a>

<a id="exceptions-cons"></a>
#### 2.4.3 短所

制御フローが混乱する原因になる可能性があります。ライブラリ呼び出しを行う際にエラーケースを見落としやすくなります。

<a id="s2.4.4-decision"></a>
<a id="244-decision"></a>

<a id="exceptions-decision"></a>
#### 2.4.4 結論

例外は特定の条件に従う必要があります:

-   合理的な場合は組み込みの例外クラスを利用してください。例えば、関数の引数を検証する際に発生する可能性があるような、違反された前提条件のようなプログラミングミスを示すために `ValueError` を発生させてください。

-   条件分岐や前提条件の検証の代わりに `assert` 文を使用しないでください。アプリケーションロジックにとって重要であってはなりません。判断基準は、コードを壊さずに `assert` を削除できるかどうかです。`assert` の条件は[評価が保証されていません](https://docs.python.org/3/reference/simple_stmts.html#the-assert-statement)。[pytest](https://pytest.org) ベースのテストでは、期待値を検証するために `assert` は問題なく、期待されています。例えば:

    ```python
    Yes:
      def connect_to_next_port(self, minimum: int) -> int:
        """次に利用可能なポートに接続します。

        Args:
          minimum: 1024 以上のポート値。

        Returns:
          新しい最小ポート。

        Raises:
          ConnectionError: 利用可能なポートが見つからない場合。
        """
        if minimum < 1024:
          # この ValueError の発生は、API の誤用に対するこの特定の
          # 動作反応を保証することが適切でないため、ドキュメント文字列の
          # "Raises:" セクションには記載されていないことに注意してください。
          raise ValueError(f'Min. port must be at least 1024, not {minimum}.')
        port = self._find_next_open_port(minimum)
        if port is None:
          raise ConnectionError(
              f'Could not connect to service on port {minimum} or higher.')
        # コードはこの assert の結果に依存しません。
        assert port >= minimum, (
            f'Unexpected port {port} when minimum was {minimum}.')
        return port
    ```

    ```python
    No:
      def connect_to_next_port(self, minimum: int) -> int:
        """次に利用可能なポートに接続します。

        Args:
          minimum: 1024 以上のポート値。

        Returns:
          新しい最小ポート。
        """
        assert minimum >= 1024, 'Minimum port must be at least 1024.'
        # 以下のコードは前の assert に依存しています。
        port = self._find_next_open_port(minimum)
        assert port is not None
        # return 文の型チェックは assert に依存しています。
        return port
    ```


-   ライブラリやパッケージは独自の例外を定義できます。その場合、既存の例外クラスから継承する必要があります。例外名は `Error` で終わるべきで、繰り返しを導入してはなりません（`foo.FooError`）。

-   すべてをキャッチする `except:` 文を使用したり、`Exception` や `StandardError` をキャッチしたりしないでください。ただし、以下の場合を除きます:

    -   例外を再送出する場合、または
    -   プログラム内で例外が伝播されずに記録されて抑制される分離ポイントを作成する場合（スレッドの最外部のブロックを保護してクラッシュを防ぐなど）。

    Python はこの点で非常に寛容であり、`except:` はスペルミスの名前、sys.exit() 呼び出し、Ctrl+C 割り込み、unittest の失敗、およびあなたが単にキャッチしたくないあらゆる種類の例外を本当にすべてキャッチします。

-   `try`/`except` ブロック内のコード量を最小限にしてください。`try` の本体が大きいほど、例外を発生させることを予期していなかった行のコードによって例外が発生する可能性が高くなります。そのような場合、`try`/`except` ブロックが本当のエラーを隠してしまいます。

-   `try` ブロック内で例外が発生するかどうかに関係なくコードを実行するには、`finally` 句を使用してください。これはクリーンアップ（例: ファイルのクローズ）によく便利です。

<a id="s2.5-global-variables"></a>
<a id="25-global-variables"></a>
<a id="s2.5-global-state"></a>
<a id="25-global-state"></a>

<a id="global-variables"></a>
### 2.5 ミュータブルなグローバル状態

ミュータブルなグローバル状態を避けてください。

<a id="s2.5.1-definition"></a>
<a id="251-definition"></a>

<a id="global-variables-definition"></a>
#### 2.5.1 定義

プログラム実行中に変更される可能性のあるモジュールレベルの値やクラス属性です。

<a id="s2.5.2-pros"></a>
<a id="252-pros"></a>

<a id="global-variables-pros"></a>
#### 2.5.2 長所

時々便利です。

<a id="s2.5.3-cons"></a>
<a id="253-cons"></a>

<a id="global-variables-cons"></a>
#### 2.5.3 短所

*   カプセル化を破壊します: そのような設計は、正当な目的を達成することを困難にする可能性があります。例えば、グローバル状態がデータベース接続の管理に使用されている場合、（マイグレーション中の差分計算などのために）同時に2つの異なるデータベースに接続することが困難になります。同様の問題はグローバルレジストリでも容易に発生します。

*   グローバル変数への代入はモジュールが最初にインポートされたときに行われるため、インポート中にモジュールの動作を変更する可能性があります。

<a id="s2.5.4-decision"></a>
<a id="254-decision"></a>

<a id="global-variables-decision"></a>
#### 2.5.4 結論

ミュータブルなグローバル状態を避けてください。

グローバル状態の使用が正当化されるまれなケースでは、ミュータブルなグローバルエンティティはモジュールレベルまたはクラス属性として宣言し、名前の前に `_` を付けて内部にすべきです。必要に応じて、ミュータブルなグローバル状態への外部アクセスは、パブリック関数またはクラスメソッドを通じて行う必要があります。[命名規則](#s3.16-naming)を参照してください。ミュータブルなグローバル状態が使用されている設計上の理由をコメントまたはコメントからリンクされたドキュメントで説明してください。

モジュールレベルの定数は許可され、推奨されます。例えば: 内部使用の定数の場合は `_MAX_HOLY_HANDGRENADE_COUNT = 3`、パブリック API の定数の場合は `SIR_LANCELOTS_FAVORITE_COLOR = "blue"` です。定数はアンダースコア付きのすべて大文字で命名する必要があります。[命名規則](#s3.16-naming)を参照してください。

<a id="s2.6-nested"></a>
<a id="26-nested"></a>

<a id="nested-classes-functions"></a>
### 2.6 ネストされた/ローカル/内部クラスと関数

ローカル変数をクローズオーバーするために使用する場合、ネストされたローカル関数やクラスは問題ありません。内部クラスは問題ありません。

<a id="s2.6.1-definition"></a>
<a id="261-definition"></a>

<a id="nested-classes-functions-definition"></a>
#### 2.6.1 定義

クラスはメソッド、関数、またはクラスの内部で定義できます。関数はメソッドまたは関数の内部で定義できます。ネストされた関数は、外側のスコープで定義された変数に対して読み取り専用のアクセスを持ちます。

<a id="s2.6.2-pros"></a>
<a id="262-pros"></a>

<a id="nested-classes-functions-pros"></a>
#### 2.6.2 長所

非常に限定されたスコープ内でのみ使用されるユーティリティクラスや関数の定義を可能にします。非常に [ADT](https://en.wikipedia.org/wiki/Abstract_data_type) 的です。デコレータの実装によく使用されます。

<a id="s2.6.3-cons"></a>
<a id="263-cons"></a>

<a id="nested-classes-functions-cons"></a>
#### 2.6.3 短所

ネストされた関数やクラスは直接テストできません。ネストにより、外側の関数が長くなり、読みにくくなる可能性があります。

<a id="s2.6.4-decision"></a>
<a id="264-decision"></a>

<a id="nested-classes-functions-decision"></a>
#### 2.6.4 結論

いくつかの注意点はありますが、問題ありません。`self` や `cls` 以外のローカル値をクローズオーバーする場合を除き、ネストされた関数やクラスを避けてください。モジュールのユーザーから隠すためだけに関数をネストしないでください。代わりに、モジュールレベルで名前の前に \_ を付けて、テストからアクセスできるようにしてください。

<a id="s2.7-comprehensions"></a>
<a id="s2.7-list_comprehensions"></a>
<a id="27-list_comprehensions"></a>
<a id="list_comprehensions"></a>
<a id="list-comprehensions"></a>

<a id="comprehensions"></a>
### 2.7 内包表記とジェネレータ式

シンプルなケースでは使用して構いません。

<a id="s2.7.1-definition"></a>
<a id="271-definition"></a>

<a id="comprehensions-definition"></a>
#### 2.7.1 定義

リスト、辞書、セットの内包表記およびジェネレータ式は、従来のループ、`map()`、`filter()`、または `lambda` に頼ることなく、コンテナ型やイテレータを作成するための簡潔で効率的な方法を提供します。

<a id="s2.7.2-pros"></a>
<a id="272-pros"></a>

<a id="comprehensions-pros"></a>
#### 2.7.2 長所

シンプルな内包表記は、他の辞書、リスト、セットの作成テクニックよりも明快でシンプルになり得ます。ジェネレータ式はリストの作成を完全に回避するため、非常に効率的です。

<a id="s2.7.3-cons"></a>
<a id="273-cons"></a>

<a id="comprehensions-cons"></a>
#### 2.7.3 短所

複雑な内包表記やジェネレータ式は読みにくい場合があります。

<a id="s2.7.4-decision"></a>
<a id="274-decision"></a>

<a id="comprehensions-decision"></a>
#### 2.7.4 結論

内包表記は許可されていますが、複数の `for` 句やフィルタ式は許可されていません。簡潔さではなく、可読性を最適化してください。

```python
Yes:
  result = [mapping_expr for value in iterable if filter_expr]

  result = [
      is_valid(metric={'key': value})
      for value in interesting_iterable
      if a_longer_filter_expression(value)
  ]

  descriptive_name = [
      transform({'key': key, 'value': value}, color='black')
      for key, value in generate_iterable(some_input)
      if complicated_condition_is_met(key, value)
  ]

  result = []
  for x in range(10):
    for y in range(5):
      if x * y > 10:
        result.append((x, y))

  return {
      x: complicated_transform(x)
      for x in long_generator_function(parameter)
      if x is not None
  }

  return (x**2 for x in range(10))

  unique_names = {user.name for user in users if user is not None}
```

```python
No:
  result = [(x, y) for x in range(10) for y in range(5) if x * y > 10]

  return (
      (x, y, z)
      for x in range(5)
      for y in range(5)
      if x != y
      for z in range(5)
      if y != z
  )
```
<a id="s2.8-default-iterators-and-operators"></a>

<a id="default-iterators-operators"></a>
### 2.8 デフォルトイテレータと演算子

リスト、辞書、ファイルなど、デフォルトイテレータと演算子をサポートする型に対しては、それらを使用してください。

<a id="s2.8.1-definition"></a>
<a id="281-definition"></a>

<a id="default-iterators-operators-definition"></a>
#### 2.8.1 定義

辞書やリストなどのコンテナ型は、デフォルトイテレータとメンバーシップテスト演算子（"in" と "not in"）を定義しています。

<a id="s2.8.2-pros"></a>
<a id="282-pros"></a>

<a id="default-iterators-operators-pros"></a>
#### 2.8.2 長所

デフォルトイテレータと演算子はシンプルで効率的です。余分なメソッド呼び出しなしに、操作を直接表現します。デフォルト演算子を使用する関数は汎用的です。その操作をサポートする任意の型で使用できます。

<a id="s2.8.3-cons"></a>
<a id="283-cons"></a>

<a id="default-iterators-operators-cons"></a>
#### 2.8.3 短所

メソッド名を読むだけではオブジェクトの型を判別できません（変数に型アノテーションがある場合を除く）。これは利点でもあります。

<a id="s2.8.4-decision"></a>
<a id="284-decision"></a>

<a id="default-iterators-operators-decision"></a>
#### 2.8.4 結論

リスト、辞書、ファイルなど、デフォルトイテレータと演算子をサポートする型に対しては、それらを使用してください。組み込み型はイテレータメソッドも定義しています。リストを返すメソッドよりもこれらのメソッドを優先してください。ただし、コンテナのイテレーション中にコンテナを変更してはいけません。

```python
Yes:  for key in adict: ...
      if obj in alist: ...
      for line in afile: ...
      for k, v in adict.items(): ...
```

```python
No:   for key in adict.keys(): ...
      for line in afile.readlines(): ...
```

<a id="s2.9-generators"></a>
<a id="29-generators"></a>

<a id="generators"></a>
### 2.9 ジェネレータ

必要に応じてジェネレータを使用してください。

<a id="s2.9.1-definition"></a>
<a id="291-definition"></a>

<a id="generators-definition"></a>
#### 2.9.1 定義

ジェネレータ関数は、yield 文を実行するたびに値を生成するイテレータを返します。値を yield した後、次の値が必要になるまでジェネレータ関数のランタイム状態は一時停止されます。

<a id="s2.9.2-pros"></a>
<a id="292-pros"></a>

<a id="generators-pros"></a>
#### 2.9.2 長所

ローカル変数の状態と制御フローが各呼び出しで保持されるため、コードがよりシンプルになります。ジェネレータは、一度に値のリスト全体を作成する関数よりもメモリ使用量が少なくなります。

<a id="s2.9.3-cons"></a>
<a id="293-cons"></a>

<a id="generators-cons"></a>
#### 2.9.3 短所

ジェネレータ内のローカル変数は、ジェネレータが最後まで消費されるか、ジェネレータ自体がガベージコレクションされるまで、ガベージコレクションされません。

<a id="s2.9.4-decision"></a>
<a id="294-decision"></a>

<a id="generators-decision"></a>
#### 2.9.4 結論

問題ありません。ジェネレータ関数のドキュメント文字列では "Returns:" ではなく "Yields:" を使用してください。

ジェネレータが高コストなリソースを管理する場合は、必ずクリーンアップを強制するようにしてください。

クリーンアップを行う良い方法は、ジェネレータをコンテキストマネージャでラップすることです [PEP-0533](https://peps.python.org/pep-0533/)。

<a id="s2.10-lambda-functions"></a>
<a id="210-lambda-functions"></a>

<a id="lambdas"></a>
### 2.10 Lambda 関数

一行で書ける場合は使用して構いません。`lambda` を使った `map()` や `filter()` よりもジェネレータ式を優先してください。

<a id="s2.10.1-definition"></a>
<a id="2101-definition"></a>

<a id="lambdas-definition"></a>
#### 2.10.1 定義

Lambda は、文ではなく式の中で無名関数を定義します。

<a id="s2.10.2-pros"></a>
<a id="2102-pros"></a>

<a id="lambdas-pros"></a>
#### 2.10.2 長所

便利です。

<a id="s2.10.3-cons"></a>
<a id="2103-cons"></a>

<a id="lambdas-cons"></a>
#### 2.10.3 短所

ローカル関数よりも読みにくく、デバッグしにくいです。名前がないため、スタックトレースの理解がより困難になります。関数が式しか含められないため、表現力が制限されます。

<a id="s2.10.4-decision"></a>
<a id="2104-decision"></a>

<a id="lambdas-decision"></a>
#### 2.10.4 結論

Lambda の使用は許可されています。lambda 関数内のコードが複数行にまたがるか、60〜80文字を超える場合は、通常の[ネストされた関数](#lexical-scoping)として定義する方がよいかもしれません。

乗算などの一般的な操作には、lambda 関数の代わりに `operator` モジュールの関数を使用してください。例えば、`lambda x, y: x * y` よりも `operator.mul` を優先してください。

<a id="s2.11-conditional-expressions"></a>
<a id="211-conditional-expressions"></a>

<a id="conditional-expressions"></a>
### 2.11 条件式

シンプルな場合は使用して構いません。

<a id="s2.11.1-definition"></a>
<a id="2111-definition"></a>

<a id="conditional-expressions-definition"></a>
#### 2.11.1 定義

条件式（「三項演算子」と呼ばれることもあります）は、if 文のより短い構文を提供する仕組みです。例: `x = 1 if cond else 2`。

<a id="s2.11.2-pros"></a>
<a id="2112-pros"></a>

<a id="conditional-expressions-pros"></a>
#### 2.11.2 長所

if 文よりも短く、便利です。

<a id="s2.11.3-cons"></a>
<a id="2113-cons"></a>

<a id="conditional-expressions-cons"></a>
#### 2.11.3 短所

if 文よりも読みにくい場合があります。式が長い場合、条件部分を見つけにくくなることがあります。

<a id="s2.11.4-decision"></a>
<a id="2114-decision"></a>

<a id="conditional-expressions-decision"></a>
#### 2.11.4 結論

シンプルな場合は使用して構いません。各部分は1行に収まる必要があります: true 式、if 式、else 式。より複雑になる場合は、完全な if 文を使用してください。

```python
Yes:
    one_line = 'yes' if predicate(value) else 'no'
    slightly_split = ('yes' if predicate(value)
                      else 'no, nein, nyet')
    the_longest_ternary_style_that_can_be_done = (
        'yes, true, affirmative, confirmed, correct'
        if predicate(value)
        else 'no, false, negative, nay')
```

```python
No:
    bad_line_breaking = ('yes' if predicate(value) else
                         'no')
    portion_too_long = ('yes'
                        if some_long_module.some_long_predicate_function(
                            really_long_variable_name)
                        else 'no, false, negative, nay')
```

<a id="s2.12-default-argument-values"></a>
<a id="212-default-argument-values"></a>

<a id="default-arguments"></a>
### 2.12 デフォルト引数値

ほとんどの場合、使用して構いません。

<a id="s2.12.1-definition"></a>
<a id="2121-definition"></a>

<a id="default-arguments-definition"></a>
#### 2.12.1 定義

関数のパラメータリストの末尾で変数に値を指定できます。例: `def foo(a, b=0):`。`foo` が1つの引数のみで呼び出された場合、`b` は 0 に設定されます。2つの引数で呼び出された場合、`b` は2番目の引数の値になります。

<a id="s2.12.2-pros"></a>
<a id="2122-pros"></a>

<a id="default-arguments-pros"></a>
#### 2.12.2 長所

多くのデフォルト値を使用する関数がある場合が多いですが、まれにデフォルトをオーバーライドしたいことがあります。デフォルト引数値は、まれな例外のために多くの関数を定義する必要なく、これを行う簡単な方法を提供します。Python はオーバーロードされたメソッド/関数をサポートしていないため、デフォルト引数はオーバーロード動作を「模倣」する簡単な方法です。

<a id="s2.12.3-cons"></a>
<a id="2123-cons"></a>

<a id="default-arguments-cons"></a>
#### 2.12.3 短所

デフォルト引数はモジュールのロード時に一度だけ評価されます。引数がリストや辞書などのミュータブルオブジェクトである場合、問題が発生する可能性があります。関数がオブジェクトを変更した場合（例: リストにアイテムを追加）、デフォルト値が変更されてしまいます。

<a id="s2.12.4-decision"></a>
<a id="2124-decision"></a>

<a id="default-arguments-decision"></a>
#### 2.12.4 結論

以下の注意点を守れば使用して構いません:

関数やメソッドの定義でミュータブルオブジェクトをデフォルト値として使用しないでください。

```python
Yes: def foo(a, b=None):
         if b is None:
             b = []
Yes: def foo(a, b: Sequence | None = None):
         if b is None:
             b = []
Yes: def foo(a, b: Sequence = ()):  # Empty tuple OK since tuples are immutable.
         ...
```

```python
from absl import flags
_FOO = flags.DEFINE_string(...)

No:  def foo(a, b=[]):
         ...
No:  def foo(a, b=time.time()):  # Is `b` supposed to represent when this module was loaded?
         ...
No:  def foo(a, b=_FOO.value):  # sys.argv has not yet been parsed...
         ...
No:  def foo(a, b: Mapping = {}):  # Could still get passed to unchecked code.
         ...
```

<a id="s2.13-properties"></a>
<a id="213-properties"></a>

<a id="properties"></a>
### 2.13 プロパティ

プロパティは、些細な計算やロジックを必要とする属性の取得や設定を制御するために使用できます。プロパティの実装は、通常の属性アクセスに対する一般的な期待に沿うものでなければなりません: すなわち、低コストで、わかりやすく、驚きがないことです。

<a id="s2.13.1-definition"></a>
<a id="2131-definition"></a>

<a id="properties-definition"></a>
#### 2.13.1 定義

属性の取得と設定のためのメソッド呼び出しを、標準的な属性アクセスとしてラップする方法です。

<a id="s2.13.2-pros"></a>
<a id="2132-pros"></a>

<a id="properties-pros"></a>
#### 2.13.2 長所

*   [getter と setter](#getters-and-setters) のメソッド呼び出しの代わりに、属性アクセスと代入の API を提供できます。
*   属性を読み取り専用にするために使用できます。
*   計算を遅延させることができます。
*   クラスの内部がクラスのユーザーとは独立して進化する場合に、クラスのパブリックインターフェースを維持する方法を提供します。

<a id="s2.13.3-cons"></a>
<a id="2133-cons"></a>

<a id="properties-cons"></a>
#### 2.13.3 短所

*   演算子のオーバーロードと同様に、副作用を隠す可能性があります。
*   サブクラスにとって混乱を招く可能性があります。

<a id="s2.13.4-decision"></a>
<a id="2134-decision"></a>

<a id="properties-decision"></a>
#### 2.13.4 結論

プロパティの使用は許可されていますが、演算子のオーバーロードと同様に、必要な場合にのみ使用し、典型的な属性アクセスの期待に沿うようにしてください。それ以外の場合は、[getter と setter](#getters-and-setters) のルールに従ってください。

例えば、単に内部属性を取得および設定するためだけにプロパティを使用することは許可されていません: 計算が行われていないため、プロパティは不要です（[代わりに属性を public にしてください](#getters-and-setters)）。一方、属性アクセスを制御したり、*些細な*派生値を計算するためにプロパティを使用することは許可されています: ロジックがシンプルで驚きがないためです。

プロパティは `@property` [デコレータ](#s2.17-function-and-method-decorators)を使用して作成してください。プロパティディスクリプタを手動で実装することは、[パワー機能](#power-features)とみなされます。

プロパティによる継承は自明ではない場合があります。サブクラスがオーバーライドして拡張したいと考えうる計算を実装するためにプロパティを使用しないでください。

<a id="s2.14-truefalse-evaluations"></a>
<a id="214-truefalse-evaluations"></a>

<a id="truefalse-evaluations"></a>
### 2.14 True/False の評価

可能な限り「暗黙の」false を使用してください（いくつかの注意点があります）。

<a id="s2.14.1-definition"></a>
<a id="2141-definition"></a>

<a id="truefalse-evaluations-definition"></a>
#### 2.14.1 定義

Python はブールコンテキストにおいて、特定の値を `False` として評価します。簡単な「経験則」として、すべての「空の」値は false とみなされます。つまり、`0, None, [], {}, ''` はすべてブールコンテキストで false と評価されます。

<a id="s2.14.2-pros"></a>
<a id="2142-pros"></a>

<a id="truefalse-evaluations-pros"></a>
#### 2.14.2 長所

Python のブール値を使用した条件は、読みやすく、エラーが起きにくいです。ほとんどの場合、より高速でもあります。

<a id="s2.14.3-cons"></a>
<a id="2143-cons"></a>

<a id="truefalse-evaluations-cons"></a>
#### 2.14.3 短所

C/C++ 開発者にとっては奇妙に見えるかもしれません。

<a id="s2.14.4-decision"></a>
<a id="2144-decision"></a>

<a id="truefalse-evaluations-decision"></a>
#### 2.14.4 結論

可能であれば「暗黙の」false を使用してください。例えば、`if foo != []:` ではなく `if foo:` とします。ただし、いくつかの注意点を念頭に置いてください:

-   `None` 値のチェックには常に `if foo is None:` （または `is not None`）を使用してください。例えば、デフォルト値が `None` の変数や引数に他の値が設定されたかどうかをテストする場合です。その他の値がブールコンテキストで false となる値かもしれません！

-   ブール変数を `==` で `False` と比較しないでください。代わりに `if not x:` を使用してください。`False` と `None` を区別する必要がある場合は、`if not x and x is not None:` のように式を連鎖させてください。

-   シーケンス（文字列、リスト、タプル）については、空のシーケンスが false であるという事実を利用してください。したがって、`if len(seq):` や `if not len(seq):` よりも、それぞれ `if seq:` や `if not seq:` が望ましいです。

-   整数を扱う場合、暗黙の false はメリットよりもリスクが大きい場合があります（すなわち、`None` を誤って 0 として扱ってしまう場合）。整数であることがわかっている値（かつ `len()` の結果ではない値）は、整数 0 と比較しても構いません。

    ```python
    Yes: if not users:
             print('no users')

         if i % 10 == 0:
             self.handle_multiple_of_ten()

         def f(x=None):
             if x is None:
                 x = []
    ```

    ```python
    No:  if len(users) == 0:
             print('no users')

         if not i % 10:
             self.handle_multiple_of_ten()

         def f(x=None):
             x = x or []
    ```

-   `'0'`（すなわち、文字列としての `0`）は true と評価されることに注意してください。

-   Numpy 配列は暗黙のブールコンテキストで例外を発生させる場合があることに注意してください。`np.array` の空判定をテストする場合は、`.size` 属性を使用することを推奨します（例: `if not users.size`）。
<a id="s2.16-lexical-scoping"></a>
<a id="216-lexical-scoping"></a>

<a id="lexical-scoping"></a>
### 2.16 レキシカルスコーピング

使用して構いません。

<a id="s2.16.1-definition"></a>
<a id="2161-definition"></a>

<a id="lexical-scoping-definition"></a>
#### 2.16.1 定義

ネストされた Python 関数は、外側の関数で定義された変数を参照できますが、代入はできません。変数の束縛はレキシカルスコーピング、つまり静的なプログラムテキストに基づいて解決されます。ブロック内での名前への代入は、使用が代入よりも前であっても、Python がその名前へのすべての参照をローカル変数として扱う原因になります。グローバル宣言がある場合、その名前はグローバル変数として扱われます。

この機能の使用例は以下の通りです:

```python
def get_adder(summand1: float) -> Callable[[float], float]:
    """指定された数値に数を加える関数を返します。"""
    def adder(summand2: float) -> float:
        return summand1 + summand2

    return adder
```

<a id="s2.16.2-pros"></a>
<a id="2162-pros"></a>

<a id="lexical-scoping-pros"></a>
#### 2.16.2 長所

より明快でエレガントなコードになることが多いです。特に、経験豊富な Lisp や Scheme（および Haskell や ML など…）プログラマーにとっては心地よいです。

<a id="s2.16.3-cons"></a>
<a id="2163-cons"></a>

<a id="lexical-scoping-cons"></a>
#### 2.16.3 短所

[PEP-0227](https://peps.python.org/pep-0227/) に基づくこの例のような、紛らわしいバグを引き起こす可能性があります:

```python
i = 4
def foo(x: Iterable[int]):
    def bar():
        print(i, end='')
    # ...
    # ここに大量のコード
    # ...
    for i in x:  # ああ、i は foo のローカルなので、bar はこれを見る
        print(i, end='')
    bar()
```

したがって `foo([1, 2, 3])` は `1 2 3 4` ではなく `1 2 3 3` を出力します。

<a id="s2.16.4-decision"></a>
<a id="2164-decision"></a>

<a id="lexical-scoping-decision"></a>
#### 2.16.4 結論

使用して構いません。

<a id="s2.17-function-and-method-decorators"></a>
<a id="217-function-and-method-decorators"></a>
<a id="function-and-method-decorators"></a>

<a id="decorators"></a>
### 2.17 関数とメソッドのデコレータ

明確な利点がある場合にデコレータを慎重に使用してください。`staticmethod` を避け、`classmethod` の使用を制限してください。

<a id="s2.17.1-definition"></a>
<a id="2171-definition"></a>

<a id="decorators-definition"></a>
#### 2.17.1 定義

[関数とメソッドのデコレータ](https://docs.python.org/3/glossary.html#term-decorator)（別名「`@` 記法」）。よく使われるデコレータの一つは `@property` で、通常のメソッドを動的に計算される属性に変換するために使用されます。ただし、デコレータ構文はユーザー定義のデコレータも許可します。具体的には、ある関数 `my_decorator` に対して、これは:

```python
class C:
    @my_decorator
    def method(self):
        # メソッド本体 ...
```

以下と等価です:

```python
class C:
    def method(self):
        # メソッド本体 ...
    method = my_decorator(method)
```

<a id="s2.17.2-pros"></a>
<a id="2172-pros"></a>

<a id="decorators-pros"></a>
#### 2.17.2 長所

メソッドに対する変換をエレガントに指定します。変換によって繰り返しコードを排除したり、不変条件を強制したりできます。

<a id="s2.17.3-cons"></a>
<a id="2173-cons"></a>

<a id="decorators-cons"></a>
#### 2.17.3 短所

デコレータは関数の引数や戻り値に対して任意の操作を行うことができ、驚くべき暗黙の動作を引き起こす可能性があります。さらに、デコレータはオブジェクト定義時に実行されます。モジュールレベルのオブジェクト（クラス、モジュール関数など）の場合、これはインポート時に発生します。デコレータコードの失敗からの回復はほぼ不可能です。

<a id="s2.17.4-decision"></a>
<a id="2174-decision"></a>

<a id="decorators-decision"></a>
#### 2.17.4 結論

明確な利点がある場合にデコレータを慎重に使用してください。デコレータは関数と同じインポートおよび命名のガイドラインに従うべきです。デコレータのドキュメント文字列は、その関数がデコレータであることを明確に述べるべきです。デコレータのユニットテストを書いてください。

デコレータ自体での外部依存関係を避けてください（例: ファイル、ソケット、データベース接続などに依存しないでください）。これらは、デコレータが（インポート時、おそらく `pydoc` や他のツールから）実行されるときに利用できない可能性があるためです。有効なパラメータで呼び出されたデコレータは、（可能な限り）すべてのケースで成功することが保証されるべきです。

デコレータは「トップレベルコード」の特殊なケースです。詳細は [main](#s3.17-main) を参照してください。

`staticmethod` は既存のライブラリで定義された API と統合するために強制される場合を除き、決して使用しないでください。代わりにモジュールレベルの関数を書いてください。

`classmethod` は名前付きコンストラクタを書く場合、またはプロセス全体のキャッシュなど必要なグローバル状態を変更するクラス固有のルーチンの場合にのみ使用してください。

<a id="s2.18-threading"></a>
<a id="218-threading"></a>

<a id="threading"></a>
### 2.18 スレッド処理

組み込み型のアトミック性に頼らないでください。

Python の辞書などの組み込みデータ型はアトミック操作を持つように見えますが、アトミックではないコーナーケースがあります（例: `__hash__` や `__eq__` が Python メソッドとして実装されている場合）。そのアトミック性に頼るべきではありません。アトミックな変数代入にも頼るべきではありません（これもまた辞書に依存するため）。

スレッド間のデータ通信の望ましい方法として、`queue` モジュールの `Queue` データ型を使用してください。それ以外の場合は、`threading` モジュールとそのロックプリミティブを使用してください。低レベルのロックよりも条件変数と `threading.Condition` を優先してください。

<a id="s2.19-power-features"></a>
<a id="219-power-features"></a>

<a id="power-features"></a>
### 2.19 パワー機能

これらの機能を避けてください。

<a id="s2.19.1-definition"></a>
<a id="2191-definition"></a>

<a id="power-features-definition"></a>
#### 2.19.1 定義

Python は非常に柔軟な言語で、カスタムメタクラス、バイトコードへのアクセス、オンザフライコンパイル、動的継承、オブジェクトの親変更、インポートハック、リフレクション（例: `getattr()` の一部の使用）、システム内部の変更、カスタマイズされたクリーンアップを実装する `__del__` メソッドなど、多くの高度な機能を提供します。

<a id="s2.19.2-pros"></a>
<a id="2192-pros"></a>

<a id="power-features-pros"></a>
#### 2.19.2 長所

これらは強力な言語機能です。コードをよりコンパクトにすることができます。

<a id="s2.19.3-cons"></a>
<a id="2193-cons"></a>

<a id="power-features-cons"></a>
#### 2.19.3 短所

これらの「クール」な機能は、絶対に必要でないときに使用したくなる誘惑があります。内部で通常とは異なる機能を使用しているコードは、読みにくく、理解しにくく、デバッグしにくいです。最初は（元の作者にとっては）そう見えませんが、コードを再訪すると、より長いが素直なコードよりも難しい傾向があります。

<a id="s2.19.4-decision"></a>
<a id="2194-decision"></a>

<a id="power-features-decision"></a>
#### 2.19.4 結論

コード内でこれらの機能を避けてください。

内部的にこれらの機能を使用する標準ライブラリのモジュールやクラスは使用して構いません（例えば、`abc.ABCMeta`、`dataclasses`、`enum`）。

<a id="s2.20-modern-python"></a>
<a id="220-modern-python"></a>

<a id="modern-python"></a>
### 2.20 モダン Python: from \_\_future\_\_ imports

新しい言語バージョンのセマンティック変更は、以前のランタイムでファイルごとに有効にするための特別な future インポートの背後にゲートされる場合があります。

<a id="s2.20.1-definition"></a>
<a id="2201-definition"></a>

<a id="modern-python-definition"></a>
#### 2.20.1 定義

`from __future__ import` 文を通じてよりモダンな機能の一部を有効にできることで、将来の Python バージョンから期待される機能を早期に使用できます。

<a id="s2.20.2-pros"></a>
<a id="2202-pros"></a>

<a id="modern-python-pros"></a>
#### 2.20.2 長所

これにより、ファイルごとに互換性を宣言し、それらのファイル内でのリグレッションを防ぎながら変更を加えることができるため、ランタイムバージョンのアップグレードがスムーズになることが証明されています。モダンなコードは、将来のランタイムアップグレード中に問題となる技術的負債が蓄積される可能性が低いため、よりメンテナンスしやすくなります。

<a id="s2.20.3-cons"></a>
<a id="2203-cons"></a>

<a id="modern-python-cons"></a>
#### 2.20.3 短所

そのようなコードは、必要な future 文の導入以前の非常に古いインタプリタバージョンでは動作しない場合があります。この必要性は、非常に幅広い多様な環境をサポートするプロジェクトでより一般的です。

<a id="s2.20.4-decision"></a>
<a id="2204-decision"></a>

<a id="modern-python-decision"></a>
#### 2.20.4 結論

##### from \_\_future\_\_ imports

`from __future__ import` 文の使用は推奨されます。これにより、特定のソースファイルで今日からよりモダンな Python 構文機能の使用を開始できます。`__future__` インポートの背後に隠された機能のバージョンで実行する必要がなくなったら、それらの行を自由に削除してください。

3.7 以降ではなく 3.5 という古いバージョンでも実行される可能性のあるコードでは、以下をインポートしてください:

```python
from __future__ import generator_stop
```

詳細は [Python future 文の定義](https://docs.python.org/3/library/__future__.html) ドキュメントを参照してください。

コードが十分にモダンな環境でのみ使用されることが確信できるまで、これらのインポートを削除しないでください。今日のコードで特定の future インポートが有効にする機能を現在使用していなくても、ファイル内に保持しておくことで、後のコード変更が不注意に古い動作に依存することを防ぐことができます。

他の `from __future__` インポート文は適切と思われる場合に使用してください。

<a id="s2.21-type-annotated-code"></a>
<a id="s2.21-typed-code"></a>
<a id="221-type-annotated-code"></a>
<a id="typed-code"></a>

<a id="typed-code"></a>
### 2.21 型アノテーション付きコード

Python コードには[型ヒント](https://docs.python.org/3/library/typing.html)でアノテーションを付けることができます。[pytype](https://github.com/google/pytype) のような型チェックツールを使用して、ビルド時にコードを型チェックしてください。ほとんどの場合、可能であれば型アノテーションはソースファイル内に記述します。サードパーティまたは拡張モジュールの場合、アノテーションは[スタブ `.pyi` ファイル](https://peps.python.org/pep-0484/#stub-files)に記述できます。


<a id="s2.21.1-definition"></a>
<a id="2211-definition"></a>

<a id="typed-code-definition"></a>
#### 2.21.1 定義

型アノテーション（「型ヒント」とも呼ばれます）は、関数またはメソッドの引数と戻り値のためのものです:

```python
def func(a: int) -> list[int]:
```

同様の構文を使用して変数の型を宣言することもできます:

```python
a: SomeType = some_func()
```

<a id="s2.21.2-pros"></a>
<a id="2212-pros"></a>

<a id="typed-code-pros"></a>
#### 2.21.2 長所

型アノテーションはコードの可読性と保守性を向上させます。型チェッカーは多くのランタイムエラーをビルド時のエラーに変換し、[パワー機能](#power-features)の使用を減らします。

<a id="s2.21.3-cons"></a>
<a id="2213-cons"></a>

<a id="typed-code-cons"></a>
#### 2.21.3 短所

型宣言を最新の状態に保つ必要があります。有効なコードだと思うものに型エラーが表示される場合があります。[型チェッカー](https://github.com/google/pytype)の使用により、[パワー機能](#power-features)の使用が制限される場合があります。

<a id="s2.21.4-decision"></a>
<a id="2214-decision"></a>

<a id="typed-code-decision"></a>
#### 2.21.4 結論

コードを更新する際に Python の型分析を有効にすることを強く推奨します。パブリック API を追加または変更する際は、型アノテーションを含め、ビルドシステムで pytype によるチェックを有効にしてください。静的解析は Python にとって比較的新しいため、（誤って推論された型などの）望ましくない副作用が一部のプロジェクトでの採用を妨げる可能性があることを認識しています。そのような状況では、型アノテーション採用を現在妨げている問題を説明する TODO またはバグへのリンクを、BUILD ファイルまたはコード自体の適切な場所にコメントとして追加することが推奨されます。
<a id="s3-python-style-rules"></a>
<a id="3-python-style-rules"></a>

<a id="python-style-rules"></a>
## 3 Python スタイルのルール

<a id="s3.1-semicolons"></a>
<a id="31-semicolons"></a>

<a id="semicolons"></a>
### 3.1 セミコロン

行をセミコロンで終端しないでください。また、セミコロンを使用して2つの文を同じ行に置かないでください。

<a id="s3.2-line-length"></a>
<a id="32-line-length"></a>

<a id="line-length"></a>
### 3.2 行の長さ

最大行長は *80 文字* です。

80 文字制限の明示的な例外:

-   長いインポート文。
-   コメント内の URL、パス名、または長いフラグ。
-   URL やパス名のような、行をまたいで分割するのが不便な空白を含まない長い文字列のモジュールレベル定数。
    -   Pylint の無効化コメント。（例: `# pylint: disable=invalid-name`）

[明示的な行の継続](https://docs.python.org/3/reference/lexical_analysis.html#explicit-line-joining)のためにバックスラッシュを使用しないでください。

代わりに、Python の[括弧、角括弧、波括弧内での暗黙の行結合](http://docs.python.org/reference/lexical_analysis.html#implicit-line-joining)を利用してください。必要であれば、式の周りに余分な括弧を追加できます。

このルールは文字列内のバックスラッシュでエスケープされた改行を禁止するものではないことに注意してください（[以下](#strings)を参照）。

```python
Yes: foo_bar(self, width, height, color='black', design=None, x='foo',
             emphasis=None, highlight=0)
```

```python

Yes: if (width == 0 and height == 0 and
         color == 'red' and emphasis == 'strong'):

     (bridge_questions.clarification_on
      .average_airspeed_of.unladen_swallow) = 'African or European?'

     with (
         very_long_first_expression_function() as spam,
         very_long_second_expression_function() as beans,
         third_thing() as eggs,
     ):
       place_order(eggs, beans, spam, beans)
```

```python
No:  if width == 0 and height == 0 and \
         color == 'red' and emphasis == 'strong':

     bridge_questions.clarification_on \
         .average_airspeed_of.unladen_swallow = 'African or European?'

     with very_long_first_expression_function() as spam, \
           very_long_second_expression_function() as beans, \
           third_thing() as eggs:
       place_order(eggs, beans, spam, beans)
```

リテラル文字列が1行に収まらない場合は、暗黙の行結合のために括弧を使用してください。

```python
x = ('This will build a very long long '
     'long long long long long long string')
```

可能な限り高い構文レベルで行を折り返すことを優先してください。行を2回折り返す必要がある場合は、両方とも同じ構文レベルで折り返してください。

```python
Yes: bridgekeeper.answer(
         name="Arthur", quest=questlib.find(owner="Arthur", perilous=True))

     answer = (a_long_line().of_chained_methods()
               .that_eventually_provides().an_answer())

     if (
         config is None
         or 'editor.language' not in config
         or config['editor.language'].use_spaces is False
     ):
       use_tabs()
```

```python
No: bridgekeeper.answer(name="Arthur", quest=questlib.find(
        owner="Arthur", perilous=True))

    answer = a_long_line().of_chained_methods().that_eventually_provides(
        ).an_answer()

    if (config is None or 'editor.language' not in config or config[
        'editor.language'].use_spaces is False):
      use_tabs()

```

コメント内では、必要に応じて長い URL を独立した行に配置してください。

```python
Yes:  # See details at
      # http://www.example.com/us/developer/documentation/api/content/v2.0/csv_file_name_extension_full_specification.html
```

```python
No:  # See details at
     # http://www.example.com/us/developer/documentation/api/content/\
     # v2.0/csv_file_name_extension_full_specification.html
```

上記の行継続の例でのインデントに注意してください。説明は[インデント](#s3.4-indentation)セクションを参照してください。

[ドキュメント文字列](#docstrings)のサマリー行は 80 文字の制限内に収める必要があります。

行が 80 文字を超え、[Black](https://github.com/psf/black) や [Pyink](https://github.com/google/pyink) 自動フォーマッタが制限以下に抑えるのに役立たないその他すべての場合、その行はこの最大値を超えることが許可されます。上記の注意事項に従って、合理的な場合は作者が手動で行を分割することが推奨されます。

<a id="s3.3-parentheses"></a>
<a id="33-parentheses"></a>

<a id="parentheses"></a>
### 3.3 括弧

括弧は控えめに使用してください。

タプルの周りに括弧を使用しても構いませんが、必須ではありません。暗黙の行継続やタプルを示す場合を除き、return 文や条件文では括弧を使用しないでください。

```python
Yes: if foo:
         bar()
     while x:
         x = bar()
     if x and y:
         bar()
     if not x:
         bar()
     # 1 アイテムのタプルの場合、() はカンマよりも視覚的に明確です。
     onesie = (foo,)
     return foo
     return spam, beans
     return (spam, beans)
     for (x, y) in dict.items(): ...
```

```python
No:  if (x):
         bar()
     if not(x):
         bar()
     return (foo)
```

<a id="s3.4-indentation"></a>
<a id="34-indentation"></a>

<a id="indentation"></a>
### 3.4 インデント

コードブロックは *4 つのスペース* でインデントしてください。

タブは決して使用しないでください。暗黙の行継続では、ラップされた要素を垂直に揃えるか（[行の長さの例](#s3.2-line-length)を参照）、4 スペースのぶら下がりインデントを使用してください。閉じ括弧（丸括弧、角括弧、波括弧）は式の末尾に配置するか、別の行に配置できますが、その場合は対応する開き括弧と同じインデントにすべきです。

```python
Yes:   # 開きデリミタに揃える。
       foo = long_function_name(var_one, var_two,
                                var_three, var_four)
       meal = (spam,
               beans)

       # 辞書内で開きデリミタに揃える。
       foo = {
           'long_dictionary_key': value1 +
                                  value2,
           ...
       }

       # 4 スペースのぶら下がりインデント; 最初の行には何も置かない。
       foo = long_function_name(
           var_one, var_two, var_three,
           var_four)
       meal = (
           spam,
           beans)

       # 4 スペースのぶら下がりインデント; 最初の行には何も置かない、
       # 閉じ括弧は新しい行に。
       foo = long_function_name(
           var_one, var_two, var_three,
           var_four
       )
       meal = (
           spam,
           beans,
       )

       # 辞書での 4 スペースのぶら下がりインデント。
       foo = {
           'long_dictionary_key':
               long_dictionary_value,
           ...
       }
```

```python
No:    # 最初の行にコードがあるのは禁止。
       foo = long_function_name(var_one, var_two,
           var_three, var_four)
       meal = (spam,
           beans)

       # 2 スペースのぶら下がりインデントは禁止。
       foo = long_function_name(
         var_one, var_two, var_three,
         var_four)

       # 辞書でぶら下がりインデントなし。
       foo = {
           'long_dictionary_key':
           long_dictionary_value,
           ...
       }
```

<a id="s3.4.1-trailing-comma"></a>
<a id="s3.4.1-trailing-commas"></a>
<a id="s3.4.1-trailing_comma"></a>
<a id="s3.4.1-trailing_commas"></a>
<a id="341-trailing_comma"></a>
<a id="341-trailing_commas"></a>
<a id="trailing_comma"></a>
<a id="trailing_commas"></a>

<a id="trailing-comma"></a>
#### 3.4.1 アイテムシーケンスの末尾カンマ

アイテムシーケンスの末尾カンマは、閉じコンテナトークン `]`、`)`、`}` が最後の要素と同じ行に表示されない場合、および単一要素のタプルの場合にのみ推奨されます。末尾カンマの存在は、Python コード自動フォーマッタ [Black](https://github.com/psf/black) や [Pyink](https://github.com/google/pyink) に対して、最後の要素の後の `,` が存在する場合に、アイテムのコンテナを1行に1アイテムでフォーマットするよう指示するヒントとしても使用されます。

```python
Yes:   golomb3 = [0, 1, 3]
       golomb4 = [
           0,
           1,
           4,
           6,
       ]
```

```python
No:    golomb4 = [
           0,
           1,
           4,
           6,]
```

<a id="s3.5-blank-lines"></a>
<a id="35-blank-lines"></a>

<a id="blank-lines"></a>
### 3.5 空行

トップレベルの定義（関数定義またはクラス定義）の間には2行の空行を入れてください。メソッド定義の間、および `class` のドキュメント文字列と最初のメソッドの間には1行の空行を入れてください。`def` 行の後に空行を入れないでください。関数やメソッド内では、適切と判断される場所で単一の空行を使用してください。

空行は定義に固定する必要はありません。例えば、関数、クラス、メソッドの定義の直前にある関連コメントは意味がある場合があります。コメントがドキュメント文字列の一部としてより有用かどうかを検討してください。

<a id="s3.6-whitespace"></a>
<a id="36-whitespace"></a>

<a id="whitespace"></a>
### 3.6 空白

句読点の周りのスペースの使用については、標準的な組版ルールに従ってください。

括弧、角括弧、波括弧の内側にはスペースを入れないでください。

```python
Yes: spam(ham[1], {'eggs': 2}, [])
```

```python
No:  spam( ham[ 1 ], { 'eggs': 2 }, [ ] )
```

カンマ、セミコロン、コロンの前にはスペースを入れないでください。カンマ、セミコロン、コロンの後にはスペースを入れてください（ただし行末の場合を除く）。

```python
Yes: if x == 4:
         print(x, y)
     x, y = y, x
```

```python
No:  if x == 4 :
         print(x , y)
     x , y = y , x
```

引数リスト、インデックス、スライスを開始する開き括弧/角括弧の前にはスペースを入れないでください。

```python
Yes: spam(1)
```

```python
No:  spam (1)
```

```python
Yes: dict['key'] = list[index]
```

```python
No:  dict ['key'] = list [index]
```

行末にスペースを残さないでください。

二項演算子の両側には1つのスペースで囲んでください: 代入（`=`）、比較（`==, <, >, !=, <>, <=, >=, in, not in, is, is not`）、ブール演算（`and, or, not`）。算術演算子（`+`, `-`, `*`, `/`, `//`, `%`, `**`, `@`）の周りのスペースの挿入には、よりよい判断を使用してください。

```python
Yes: x == 1
```

```python
No:  x<1
```

キーワード引数を渡す場合やデフォルトパラメータ値を定義する場合には `=` の周りにスペースを使用しないでください。ただし、1つの例外があります: [型アノテーションが存在する場合](#typing-default-values)、デフォルトパラメータ値の `=` の周りにスペースを*使用*してください。

```python
Yes: def complex(real, imag=0.0): return Magic(r=real, i=imag)
Yes: def complex(real, imag: float = 0.0): return Magic(r=real, i=imag)
```

```python
No:  def complex(real, imag = 0.0): return Magic(r = real, i = imag)
No:  def complex(real, imag: float=0.0): return Magic(r = real, i = imag)
```

連続する行のトークンを垂直に揃えるためにスペースを使用しないでください。メンテナンスの負担になります（`:`、`#`、`=` などに適用されます）:

```python
Yes:
  foo = 1000  # comment
  long_name = 2  # comment that should not be aligned

  dictionary = {
      'foo': 1,
      'long_name': 2,
  }
```

```python
No:
  foo       = 1000  # comment
  long_name = 2     # comment that should not be aligned

  dictionary = {
      'foo'      : 1,
      'long_name': 2,
  }
```


<a id="Python_Interpreter"></a>
<a id="s3.7-shebang-line"></a>
<a id="37-shebang-line"></a>

<a id="shebang-line"></a>
### 3.7 シバン行

ほとんどの `.py` ファイルは `#!` 行で始める必要はありません。プログラムのメインファイルは `#!/usr/bin/env python3`（virtualenv をサポートするため）または `#!/usr/bin/python3` で始めてください（[PEP-394](https://peps.python.org/pep-0394/) に従って）。

この行はカーネルが Python インタプリタを見つけるために使用しますが、モジュールをインポートする際には Python によって無視されます。直接実行されることを意図したファイルにのみ必要です。

<a id="s3.8-comments-and-docstrings"></a>
<a id="s3.8-comments"></a>
<a id="38-comments-and-docstrings"></a>

<a id="documentation"></a>
### 3.8 コメントとドキュメント文字列

モジュール、関数、メソッドのドキュメント文字列およびインラインコメントには適切なスタイルを使用してください。

<a id="s3.8.1-comments-in-doc-strings"></a>
<a id="381-docstrings"></a>
<a id="comments-in-doc-strings"></a>

<a id="docstrings"></a>
#### 3.8.1 ドキュメント文字列

Python はコードをドキュメント化するために*ドキュメント文字列*を使用します。ドキュメント文字列は、パッケージ、モジュール、クラス、または関数の最初の文である文字列です。これらの文字列はオブジェクトの `__doc__` メンバーを通じて自動的に抽出でき、`pydoc` によって使用されます。（モジュールに対して `pydoc` を実行して、どのように見えるか試してみてください。）ドキュメント文字列には常に三重ダブルクォート `"""` 形式を使用してください（[PEP 257](https://peps.python.org/pep-0257/) に従って）。ドキュメント文字列は、ピリオド、疑問符、感嘆符で終わるサマリー行（80 文字を超えない1つの物理行）として構成すべきです。より多く書く場合（推奨）、空行を1行置き、その後に残りのドキュメント文字列を最初の行の最初のクォートと同じカーソル位置から始めてください。ドキュメント文字列のフォーマットに関するガイドラインの詳細は以下にあります。

<a id="s3.8.2-comments-in-modules"></a>
<a id="382-modules"></a>
<a id="comments-in-modules"></a>

<a id="module-docs"></a>
#### 3.8.2 モジュール

すべてのファイルにはライセンスの定型文を含めるべきです。プロジェクトで使用されるライセンスに適した定型文を選択してください（例えば、Apache 2.0、BSD、LGPL、GPL）。

ファイルはモジュールの内容と使用法を説明するドキュメント文字列で始めるべきです。
```python
"""モジュールまたはプログラムの1行のサマリー。ピリオドで終了。

1行の空行を入れてください。このドキュメント文字列の残りは、
モジュールまたはプログラムの全体的な説明を含むべきです。オプションで、
エクスポートされたクラスや関数の簡単な説明や使用例を
含めることもできます。

典型的な使用例:

  foo = ClassFoo()
  bar = foo.function_bar()
"""
```


<a id="s3.8.2.1-test-modules"></a>

<a id="test-docs"></a>
##### 3.8.2.1 テストモジュール

テストファイルのモジュールレベルのドキュメント文字列は必須ではありません。追加情報を提供できる場合にのみ含めるべきです。

例としては、テストの実行方法に関する詳細、通常でないセットアップパターンの説明、外部環境への依存性などがあります。

```python
"""この blaze テストはゴールデンファイルを使用します。

それらのファイルは `google3` ディレクトリから
`blaze run //foo/bar:foo_test -- --update_golden_files` を実行して更新できます。
"""
```

新しい情報を提供しないドキュメント文字列は使用すべきではありません。

```python
"""Tests for foo.bar."""
```

<a id="s3.8.3-functions-and-methods"></a>
<a id="383-functions-and-methods"></a>
<a id="functions-and-methods"></a>

<a id="function-docs"></a>
#### 3.8.3 関数とメソッド

このセクションでは、「関数」はメソッド、関数、ジェネレータ、またはプロパティを意味します。

以下の特性を1つ以上持つすべての関数にはドキュメント文字列が必須です:

-   パブリック API の一部である
-   自明でないサイズ
-   自明でないロジック

ドキュメント文字列は、関数のコードを読まずに関数の呼び出しを書けるだけの十分な情報を提供すべきです。ドキュメント文字列は関数の呼び出し構文とそのセマンティクスを説明すべきですが、一般的にはその実装の詳細は記述すべきではありません（その詳細が関数の使用方法に関連する場合を除く）。例えば、副作用として引数の1つを変更する関数は、ドキュメント文字列にそのことを記載すべきです。それ以外の、呼び出し元には関係ない関数の実装の微妙だが重要な詳細は、関数のドキュメント文字列よりもコードに沿ったコメントとして表現する方が適切です。

ドキュメント文字列は説明的スタイル（`"""Fetches rows from a Bigtable."""`）または命令的スタイル（`"""Fetch rows from a Bigtable."""`）で書くことができますが、ファイル内でスタイルは一貫している必要があります。`@property` データディスクリプタのドキュメント文字列は、属性または <a href="#doc-function-args">関数の引数</a>のドキュメント文字列と同じスタイルを使用すべきです（`"""Returns the Bigtable path."""` ではなく `"""The Bigtable path."""`）。

関数の特定の側面は、以下にリストされた特別なセクションでドキュメント化すべきです。各セクションはコロンで終わる見出し行で始まります。見出し以外のすべてのセクションは、2または4スペースのぶら下がりインデントを維持すべきです（ファイル内で一貫してください）。これらのセクションは、関数の名前とシグネチャが十分に情報を提供し、1行のドキュメント文字列で適切に説明できる場合は省略できます。

<a id="doc-function-args"></a>
[*Args:*](#doc-function-args)
:   各パラメータを名前で列挙してください。説明は名前の後に続き、コロンに続いてスペースまたは改行で区切られます。説明が1行の80文字に収まらないほど長い場合は、パラメータ名よりも2または4スペース多いぶら下がりインデントを使用してください（ファイル内の他のドキュメント文字列と一貫してください）。コードに対応する型アノテーションが含まれていない場合、説明には必要な型を含めるべきです。関数が `*foo`（可変長引数リスト）や `**bar`（任意のキーワード引数）を受け入れる場合、`*foo` と `**bar` としてリストすべきです。

<a id="doc-function-returns"></a>
[*Returns:*（ジェネレータの場合は *Yields:*）](#doc-function-returns)
:   型アノテーションが提供しない型情報を含め、戻り値のセマンティクスを説明してください。関数が None のみを返す場合、このセクションは不要です。ドキュメント文字列が "Return"、"Returns"、"Yield"、"Yields" で始まる場合（例: `"""Returns row from Bigtable as a tuple of strings."""`）*かつ*開始文が戻り値を説明するのに十分な場合も省略できます。古い 'NumPy スタイル'（[例](https://numpy.org/doc/1.24/reference/generated/numpy.linalg.qr.html)）を模倣しないでください。このスタイルでは、タプルの戻り値を個別の名前を持つ複数の戻り値として文書化していました（タプルについて言及しません）。代わりに、そのような戻り値を次のように説明してください: "Returns: A tuple (mat_a, mat_b), where mat_a is ..., and ..."。ドキュメント文字列内の補助的な名前は、関数本体で使用される内部名と必ずしも対応する必要はありません（それらは API の一部ではないため）。関数が `yield` を使用する（ジェネレータである）場合、`Yields:` セクションは、呼び出しが評価するジェネレータオブジェクト自体ではなく、`next()` によって返されるオブジェクトをドキュメント化すべきです。

<a id="doc-function-raises"></a>
[*Raises:*](#doc-function-raises)
:   インターフェースに関連するすべての例外を、説明と共にリストしてください。*Args:* で説明したのと同様の、例外名 + コロン + スペースまたは改行とぶら下がりインデントのスタイルを使用してください。ドキュメント文字列で指定された API が違反された場合に発生する例外をドキュメント化すべきではありません（これは逆説的に、API 違反時の動作を API の一部にしてしまうためです）。

```python
def fetch_smalltable_rows(
    table_handle: smalltable.Table,
    keys: Sequence[bytes | str],
    require_all_keys: bool = False,
) -> Mapping[bytes, tuple[str, ...]]:
    """Smalltable から行を取得します。

    table_handle で表される Table インスタンスから、
    指定されたキーに関連する行を取得します。文字列キーは UTF-8 エンコードされます。

    Args:
        table_handle: 開かれた smalltable.Table インスタンス。
        keys: 取得する各テーブル行のキーを表す文字列のシーケンス。
          文字列キーは UTF-8 エンコードされます。
        require_all_keys: True の場合、すべてのキーに値が設定されている行のみが
          返されます。

    Returns:
        キーから対応する取得されたテーブル行データへのマッピング辞書。
        各行は文字列のタプルとして表されます。例えば:

        {b'Serak': ('Rigel VII', 'Preparer'),
         b'Zim': ('Irk', 'Invader'),
         b'Lrrr': ('Omicron Persei 8', 'Emperor')}

        返されるキーは常にバイト列です。keys 引数のキーが
        辞書にない場合、その行はテーブルに見つかりませんでした
        （require_all_keys は False であったはずです）。

    Raises:
        IOError: smalltable へのアクセス中にエラーが発生しました。
    """
```

同様に、改行を伴う `Args:` のこのバリエーションも許可されています:

```python
def fetch_smalltable_rows(
    table_handle: smalltable.Table,
    keys: Sequence[bytes | str],
    require_all_keys: bool = False,
) -> Mapping[bytes, tuple[str, ...]]:
    """Smalltable から行を取得します。

    table_handle で表される Table インスタンスから、
    指定されたキーに関連する行を取得します。文字列キーは UTF-8 エンコードされます。

    Args:
      table_handle:
        開かれた smalltable.Table インスタンス。
      keys:
        取得する各テーブル行のキーを表す文字列のシーケンス。
        文字列キーは UTF-8 エンコードされます。
      require_all_keys:
        True の場合、すべてのキーに値が設定されている行のみが返されます。

    Returns:
      キーから対応する取得されたテーブル行データへのマッピング辞書。
      各行は文字列のタプルとして表されます。例えば:

      {b'Serak': ('Rigel VII', 'Preparer'),
       b'Zim': ('Irk', 'Invader'),
       b'Lrrr': ('Omicron Persei 8', 'Emperor')}

      返されるキーは常にバイト列です。keys 引数のキーが
      辞書にない場合、その行はテーブルに見つかりませんでした
      （require_all_keys は False であったはずです）。

    Raises:
      IOError: smalltable へのアクセス中にエラーが発生しました。
    """
```

<a id="s3.8.3.1-overridden-methods"></a>

<a id="overridden-method-docs"></a>
##### 3.8.3.1 オーバーライドされたメソッド

基底クラスのメソッドをオーバーライドするメソッドは、[`@override`](https://typing-extensions.readthedocs.io/en/latest/#override)（`typing_extensions` または `typing` モジュールから）で明示的にデコレートされている場合、ドキュメント文字列は必要ありません。ただし、オーバーライドするメソッドの動作が基底メソッドの契約を実質的に改良する場合、または詳細を提供する必要がある場合（例: 追加の副作用のドキュメント化）、少なくともそれらの違いを含むドキュメント文字列がオーバーライドするメソッドに必要です。

```python
from typing_extensions import override

class Parent:
  def do_something(self):
    """親メソッド、ドキュメント文字列を含む。"""

# 子クラス、override でアノテーションされたメソッド。
class Child(Parent):
  @override
  def do_something(self):
    pass
```

```python
# 子クラス、しかし @override デコレータなし、ドキュメント文字列が必要。
class Child(Parent):
  def do_something(self):
    pass

# ドキュメント文字列は自明、@override はドキュメントが基底クラスにあることを
# 示すのに十分。
class Child(Parent):
  @override
  def do_something(self):
    """See base class."""
```

<a id="s3.8.4-comments-in-classes"></a>
<a id="384-classes"></a>
<a id="comments-in-classes"></a>

<a id="class-docs"></a>
#### 3.8.4 クラス

クラスはクラス定義の下に、クラスを説明するドキュメント文字列を持つべきです。[プロパティ](#properties)を除くパブリック属性は、`Attributes` セクションでドキュメント化し、[関数の `Args`](#doc-function-args) セクションと同じフォーマットに従うべきです。

```python
class SampleClass:
    """クラスのサマリーをここに記述。

    クラスの詳細情報...
    クラスの詳細情報...

    Attributes:
        likes_spam: SPAM が好きかどうかを示すブール値。
        eggs: 産んだ卵の整数カウント。
    """

    def __init__(self, likes_spam: bool = False):
        """スパムの好みに基づいてインスタンスを初期化します。

        Args:
          likes_spam: インスタンスがこの好みを示すかどうかを定義します。
        """
        self.likes_spam = likes_spam
        self.eggs = 0

    @property
    def butter_sticks(self) -> int:
        """持っているバタースティックの数。"""
```

すべてのクラスのドキュメント文字列は、クラスインスタンスが何を表すかを説明する1行のサマリーで始まるべきです。これは、`Exception` のサブクラスも例外が何を表すかを説明すべきであり、発生する可能性のあるコンテキストではないことを意味します。クラスのドキュメント文字列は、クラスがクラスであるという不要な情報を繰り返すべきではありません。

```python
# Yes:
class CheeseShopAddress:
  """チーズショップの住所。

  ...
  """

class OutOfCheeseError(Exception):
  """チーズがもうありません。"""
```

```python
# No:
class CheeseShopAddress:
  """チーズショップの住所を説明するクラス。

  ...
  """

class OutOfCheeseError(Exception):
  """チーズがもうない場合に発生します。"""
```

<a id="s3.8.5-block-and-inline-comments"></a>
<a id="comments-in-block-and-inline"></a>
<a id="s3.8.5-comments-in-block-and-inline"></a>
<a id="385-block-and-inline-comments"></a>

<a id="comments"></a>
#### 3.8.5 ブロックコメントとインラインコメント

コメントを入れる最後の場所は、コードのトリッキーな部分です。次の[コードレビュー](http://en.wikipedia.org/wiki/Code_review)で説明する必要があるなら、今コメントすべきです。複雑な操作には、操作の開始前に数行のコメントを入れてください。自明でない操作には行末にコメントを入れてください。

```python
# 配列内で i がどこにあるかを見つけるために、重み付き辞書検索を
# 使用します。配列内の最大の数と配列サイズに基づいて
# 位置を外挿し、正確な数を取得するために
# 二分探索を行います。

if i & (i-1) == 0:  # i が 0 または 2 のべき乗の場合 True。
```

可読性を向上させるために、これらのコメントはコードから少なくとも2スペース離れた位置からコメント文字 `#` で始め、その後にコメント本文の前に少なくとも1つのスペースを入れてください。

一方、コードを説明してはいけません。コードを読んでいる人は、あなたよりも Python をよく知っている（ただし、あなたが何をしようとしているかは知らない）と仮定してください。

```python
# 悪いコメント: b 配列を調べて、i が出現するたびに
# 次の要素が i+1 であることを確認する
```

<!-- 次のセクションは C++ スタイルガイドからコピーされています。 -->

<a id="s3.8.6-punctuation-spelling-and-grammar"></a>
<a id="386-punctuation-spelling-and-grammar"></a>
<a id="spelling"></a>
<a id="punctuation"></a>
<a id="grammar"></a>

<a id="punctuation-spelling-grammar"></a>
#### 3.8.6 句読点、スペル、文法

句読点、スペル、文法に注意してください。下手に書かれたコメントよりも、上手に書かれたコメントの方が読みやすいです。

コメントは散文と同様に読みやすくあるべきで、適切な大文字使用と句読点が必要です。多くの場合、文の断片よりも完全な文の方が読みやすいです。行末のコメントなどの短いコメントは、時にはより非公式であっても構いませんが、スタイルは一貫させるべきです。

コードレビュアーがセミコロンを使うべきところでカンマを使っていると指摘するのはイライラするかもしれませんが、ソースコードが高いレベルの明快さと可読性を維持することは非常に重要です。適切な句読点、スペル、文法がその目標に役立ちます。
<a id="s3.10-strings"></a>
<a id="310-strings"></a>

<a id="strings"></a>
### 3.10 文字列

パラメータがすべて文字列であっても、文字列のフォーマットには [f-string](https://docs.python.org/3/reference/lexical_analysis.html#f-strings)、`%` 演算子、または `format` メソッドを使用してください。文字列フォーマットオプションの選択には最善の判断を使用してください。`+` での単一の結合は問題ありませんが、`+` でフォーマットしないでください。

```python
Yes: x = f'name: {name}; score: {n}'
     x = '%s, %s!' % (imperative, expletive)
     x = '{}, {}'.format(first, second)
     x = 'name: %s; score: %d' % (name, n)
     x = 'name: %(name)s; score: %(score)d' % {'name':name, 'score':n}
     x = 'name: {}; score: {}'.format(name, n)
     x = a + b
```

```python
No: x = first + ', ' + second
    x = 'name: ' + name + '; score: ' + str(n)
```

ループ内で `+` と `+=` 演算子を使用して文字列を蓄積することを避けてください。一部の条件では、加算による文字列の蓄積は線形ではなく二次の実行時間になる可能性があります。このような一般的な蓄積は CPython で最適化される場合がありますが、それは実装の詳細です。最適化が適用される条件は予測が容易ではなく、変更される可能性があります。代わりに、各部分文字列をリストに追加し、ループ終了後にリストを `''.join` するか、各部分文字列を `io.StringIO` バッファに書き込んでください。これらのテクニックは一貫して償却された線形実行時間の計算量を持ちます。

```python
Yes: items = ['<table>']
     for last_name, first_name in employee_list:
         items.append('<tr><td>%s, %s</td></tr>' % (last_name, first_name))
     items.append('</table>')
     employee_table = ''.join(items)
```

```python
No: employee_table = '<table>'
    for last_name, first_name in employee_list:
        employee_table += '<tr><td>%s, %s</td></tr>' % (last_name, first_name)
    employee_table += '</table>'
```

ファイル内では文字列のクォート文字の選択に一貫性を持たせてください。`'` または `"` を選んでそれに統一してください。文字列内のクォート文字をバックスラッシュエスケープする必要を避けるために、別のクォート文字を使用しても構いません。

```python
Yes:
  Python('Why are you hiding your eyes?')
  Gollum("I'm scared of lint errors.")
  Narrator('"Good!" thought a happy Python reviewer.')
```

```python
No:
  Python("Why are you hiding your eyes?")
  Gollum('The lint. It burns. It burns us.')
  Gollum("Always the great lint. Watching. Watching.")
```

複数行の文字列には `'''` よりも `"""` を優先してください。プロジェクトは、通常の文字列にも `'` を使用する場合に限り、非ドキュメント文字列のすべての複数行文字列に `'''` を使用することを選択できます。ドキュメント文字列は `"""` を使用する必要があります。

複数行の文字列はプログラムの残りのインデントと一緒に流れません。文字列に余分なスペースを埋め込むのを避ける必要がある場合は、連結された単一行文字列か、各行の先頭のスペースを除去する [`textwrap.dedent()`](https://docs.python.org/3/library/textwrap.html#textwrap.dedent) を使用した複数行文字列を使用してください:

```python
  No:
  long_string = """This is pretty ugly.
Don't do this.
"""
```

```python
  Yes:
  long_string = """This is fine if your use case can accept
      extraneous leading spaces."""
```

```python
  Yes:
  long_string = ("And this is fine if you cannot accept\n" +
                 "extraneous leading spaces.")
```

```python
  Yes:
  long_string = ("And this too is fine if you cannot accept\n"
                 "extraneous leading spaces.")
```

```python
  Yes:
  import textwrap

  long_string = textwrap.dedent("""\
      This is also fine, because textwrap.dedent()
      will collapse common leading spaces in each line.""")
```

ここでバックスラッシュを使用することは、[明示的な行継続](#line-length)の禁止に違反しないことに注意してください。この場合、バックスラッシュは文字列リテラル内の[改行をエスケープ](https://docs.python.org/3/reference/lexical_analysis.html#string-and-bytes-literals)しています。

<a id="s3.10.1-logging"></a>
<a id="3101-logging"></a>
<a id="logging"></a>

<a id="logging"></a>
#### 3.10.1 ロギング

パターン文字列（%-プレースホルダ付き）を最初の引数として期待するロギング関数の場合: 常に最初の引数として文字列リテラル（f-string ではなく！）を使用し、パターンパラメータを後続の引数として呼び出してください。一部のロギング実装は、展開されていないパターン文字列をクエリ可能なフィールドとして収集します。また、出力するように設定されていないロガーのためにメッセージをレンダリングする時間の浪費を防ぎます。

```python
  Yes:
  import tensorflow as tf
  logger = tf.get_logger()
  logger.info('TensorFlow Version is: %s', tf.__version__)
```

```python
  Yes:
  import os
  from absl import logging

  logging.info('Current $PAGER is: %s', os.getenv('PAGER', default=''))

  homedir = os.getenv('HOME')
  if homedir is None or not os.access(homedir, os.W_OK):
    logging.error('Cannot write to home directory, $HOME=%r', homedir)
```

```python
  No:
  import os
  from absl import logging

  logging.info('Current $PAGER is:')
  logging.info(os.getenv('PAGER', default=''))

  homedir = os.getenv('HOME')
  if homedir is None or not os.access(homedir, os.W_OK):
    logging.error(f'Cannot write to home directory, $HOME={homedir!r}')
```

<a id="s3.10.2-error-messages"></a>
<a id="3102-error-messages"></a>
<a id="error-messages"></a>

<a id="error-messages"></a>
#### 3.10.2 エラーメッセージ

エラーメッセージ（例: `ValueError` などの例外のメッセージ文字列、またはユーザーに表示されるメッセージ）は3つのガイドラインに従うべきです:

1.  メッセージは実際のエラー条件に正確に一致する必要があります。

2.  補間された部分は常にそれとして明確に識別できる必要があります。

3.  シンプルな自動処理（例: grep）を可能にすべきです。

```python
  Yes:
  if not 0 <= p <= 1:
    raise ValueError(f'Not a probability: {p=}')

  try:
    os.rmdir(workdir)
  except OSError as error:
    logging.warning('Could not remove directory (reason: %r): %r',
                    error, workdir)
```

```python
  No:
  if p < 0 or p > 1:  # 問題: float('nan') の場合も false になる！
    raise ValueError(f'Not a probability: {p=}')

  try:
    os.rmdir(workdir)
  except OSError:
    # 問題: メッセージが正しくない可能性のある仮定をしている:
    # 削除は他の理由で失敗した可能性があり、これをデバッグ
    # しなければならない人を誤解させる。
    logging.warning('Directory already was deleted: %s', workdir)

  try:
    os.rmdir(workdir)
  except OSError:
    # 問題: メッセージは必要以上に grep しにくく、
    # `workdir` のすべての可能な値に対して普遍的に混乱しないわけではない。
    # 例えば workdir = 'deleted' のような名前でライブラリ関数を
    # 呼び出すことを想像してみてください。警告は次のようになります:
    # "The deleted directory could not be deleted."
    logging.warning('The %s directory could not be deleted.', workdir)
```

<a id="s3.11-files-sockets-closeables"></a>
<a id="s3.11-files-and-sockets"></a>
<a id="311-files-and-sockets"></a>
<a id="files-and-sockets"></a>

<a id="files"></a>
### 3.11 ファイル、ソケット、および類似のステートフルリソース

ファイルやソケットの使用が終わったら、明示的に閉じてください。このルールは、内部的にソケットを使用するクローズ可能なリソース（データベース接続など）や、同様の方法で閉じる必要のある他のリソースにも自然に拡張されます。いくつかの例を挙げると、[mmap](https://docs.python.org/3/library/mmap.html) マッピング、[h5py File オブジェクト](https://docs.h5py.org/en/stable/high/file.html)、[matplotlib.pyplot figure ウィンドウ](https://matplotlib.org/2.1.0/api/_as_gen/matplotlib.pyplot.close.html)なども含まれます。

ファイル、ソケット、またはその他のステートフルオブジェクトを不必要に開いたままにすることには多くの欠点があります:

-   ファイルディスクリプタなどの限られたシステムリソースを消費する可能性があります。このようなオブジェクトを多数扱うコードは、使用後すぐにシステムに返さないと、それらのリソースを不必要に使い果たす可能性があります。
-   ファイルを開いたままにすると、ファイルの移動や削除、ファイルシステムのアンマウントなどの他のアクションが妨げられる可能性があります。
-   プログラム全体で共有されるファイルやソケットは、論理的に閉じた後に誤って読み書きされる可能性があります。実際に閉じられている場合、それらからの読み書きの試みは例外を発生させ、問題をより早く知ることができます。

さらに、ファイルやソケット（および同様に動作するいくつかのリソース）はオブジェクトが破棄されたときに自動的に閉じられますが、オブジェクトの寿命をリソースの状態に結合させることは悪いプラクティスです:

-   ランタイムが実際に `__del__` メソッドを呼び出すタイミングの保証はありません。異なる Python 実装は、遅延ガベージコレクションなどの異なるメモリ管理技術を使用し、オブジェクトの寿命を任意かつ無期限に増加させる可能性があります。
-   ファイルへの予期しない参照（例: グローバル変数や例外トレースバック内）が、意図したよりも長く保持する可能性があります。

自動クリーンアップに観察可能な副作用があるファイナライザに依存することは、数十年にわたり複数の言語で繰り返し重大な問題につながることが再発見されてきました（例えば Java に関する[この記事](https://wiki.sei.cmu.edu/confluence/display/java/MET12-J.+Do+not+use+finalizers)を参照）。

ファイルと類似のリソースを管理する望ましい方法は、[`with` 文](http://docs.python.org/reference/compound_stmts.html#the-with-statement)を使用することです:

```python
with open("hello.txt") as hello_file:
    for line in hello_file:
        print(line)
```

`with` 文をサポートしないファイルライクオブジェクトの場合は、`contextlib.closing()` を使用してください:

```python
import contextlib

with contextlib.closing(urllib.urlopen("http://www.python.org/")) as front_page:
    for line in front_page:
        print(line)
```

コンテキストベースのリソース管理が実行不可能なまれなケースでは、コードのドキュメントでリソースの寿命がどのように管理されるかを明確に説明する必要があります。

<a id="s3.12-todo-comments"></a>
<a id="312-todo-comments"></a>

<a id="todo"></a>
### 3.12 TODO コメント

一時的なコード、短期的な解決策、または十分だが完璧ではないコードには `TODO` コメントを使用してください。

`TODO` コメントはすべて大文字の `TODO` で始まり、続いてコロン、そしてコンテキストを含むリソースへのリンク（理想的にはバグ参照）を記述します。バグ参照は、バグが追跡されフォローアップコメントがあるため望ましいです。このコンテキストの後に、ハイフン `-` で導入される説明文字列を続けてください。目的は、詳細を取得する方法がわかるように検索可能な一貫した `TODO` フォーマットを持つことです。

```python
# TODO: crbug.com/192795 - Investigate cpufreq optimizations.
```

古いスタイルで、以前は推奨されていましたが、新しいコードでは非推奨です:

```python
# TODO(crbug.com/192795): Investigate cpufreq optimizations.
# TODO(yourusername): Use a "\*" here for concatenation operator.
```

コンテキストとして個人やチームを参照する TODO を追加することを避けてください:

```python
# TODO: @yourusername - File an issue and use a '*' for repetition.
```

`TODO` が「将来のある時点で何かをする」という形式の場合、非常に具体的な日付（「2009年11月までに修正」）または非常に具体的なイベント（「すべてのクライアントが XML レスポンスを処理できるようになったらこのコードを削除」）を含めるようにしてください。これにより、将来のコードメンテナーが理解できます。Issue がこれを追跡するのに理想的です。

<a id="s3.13-imports-formatting"></a>
<a id="313-imports-formatting"></a>

<a id="imports-formatting"></a>
### 3.13 インポートのフォーマット

インポートは個別の行にすべきです。[`typing` と `collections.abc` のインポートには例外](#typing-imports)があります。

例:

```python
Yes: from collections.abc import Mapping, Sequence
     import os
     import sys
     from typing import Any, NewType
```

```python
No:  import os, sys
```


インポートは常にファイルの先頭、モジュールコメントとドキュメント文字列の直後、モジュールのグローバルと定数の前に配置されます。インポートは最も汎用的なものから最も限定的なものへグループ化すべきです:

1.  Python の future インポート文。例えば:

    ```python
    from __future__ import annotations
    ```

    詳細は[上記](#from-future-imports)を参照してください。

2.  Python 標準ライブラリのインポート。例えば:

    ```python
    import sys
    ```

3.  [サードパーティ](https://pypi.org/)のモジュールまたはパッケージのインポート。例えば:

    ```python
    import tensorflow as tf
    ```

4.  コードリポジトリのサブパッケージのインポート。例えば:

    ```python
    from otherproject.ai import mind
    ```

5.  **非推奨:** このファイルと同じトップレベルのサブパッケージの一部であるアプリケーション固有のインポート。例えば:

    ```python
    from myproject.backend.hgwells import time_machine
    ```

    古い SATELLITE Python スタイルコードでこれを行っているのを見かけるかもしれませんが、もう必要ありません。**新しいコードはこれを気にしないことが推奨されます。** アプリケーション固有のサブパッケージのインポートを他のサブパッケージのインポートと同じように扱ってください。

各グループ内では、各モジュールの完全なパッケージパスに従って、大文字小文字を無視して辞書順にソートすべきです（`from path import ...` の `path`）。コードはオプションでインポートセクション間に空行を配置できます。

```python
import collections
import queue
import sys

from absl import app
from absl import flags
import bs4
import cryptography
import tensorflow as tf

from book.genres import scifi
from myproject.backend import huxley
from myproject.backend.hgwells import time_machine
from myproject.backend.state_machine import main_loop
from otherproject.ai import body
from otherproject.ai import mind
from otherproject.ai import soul

# 古いスタイルのコードでは、これらのインポートがここにあるかもしれません:
#from myproject.backend.hgwells import time_machine
#from myproject.backend.state_machine import main_loop
```


<a id="s3.14-statements"></a>
<a id="314-statements"></a>

<a id="statements"></a>
### 3.14 文

一般的に1行に1つの文のみです。

ただし、文全体が1行に収まる場合に限り、テストの結果をテストと同じ行に置くことができます。特に、`try` と `except` は両方とも同じ行に収まらないため、`try`/`except` ではこれを行うことはできません。`else` がない場合に限り、`if` でのみこれを行うことができます。

```python
Yes:

  if foo: bar(foo)
```

```python
No:

  if foo: bar(foo)
  else:   baz(foo)

  try:               bar(foo)
  except ValueError: baz(foo)

  try:
      bar(foo)
  except ValueError: baz(foo)
```

<a id="s3.15-accessors"></a>
<a id="s3.15-access-control"></a>
<a id="315-access-control"></a>
<a id="access-control"></a>
<a id="accessors"></a>

<a id="getters-and-setters"></a>
### 3.15 Getter と Setter

変数の値の取得や設定に意味のある役割や動作を提供する場合に、getter と setter 関数（アクセサやミューテータとも呼ばれます）を使用すべきです。

特に、変数の取得や設定が複雑であるか、コストが大きい場合（現在または合理的な将来において）に使用すべきです。

例えば、getter/setter のペアが単に内部属性を読み書きするだけの場合、内部属性を代わりに public にすべきです。一方、変数の設定が何らかの状態の無効化や再構築を意味する場合、setter 関数にすべきです。関数の呼び出しは、潜在的に自明でない操作が発生していることを示唆します。あるいは、シンプルなロジックが必要な場合は[プロパティ](#properties)がオプションになるかもしれません。また、getter と setter が不要になるようにリファクタリングすることも考えられます。

Getter と setter は[命名規則](#s3.16-naming)のガイドラインに従うべきで、`get_foo()` と `set_foo()` のようにします。

過去の動作がプロパティを通じたアクセスを許可していた場合、新しい getter/setter 関数をプロパティにバインドしないでください。古い方法で変数にアクセスしようとするコードは、複雑さの変更を認識させるために目に見えて壊れるべきです。

<a id="s3.16-naming"></a>
<a id="316-naming"></a>

<a id="naming"></a>
### 3.16 命名規則

`module_name`, `package_name`, `ClassName`, `method_name`, `ExceptionName`, `function_name`, `GLOBAL_CONSTANT_NAME`, `global_var_name`, `instance_var_name`, `function_parameter_name`, `local_var_name`, `query_proper_noun_for_thing`, `send_acronym_via_https`.


名前は説明的であるべきです。これには関数、クラス、変数、属性、ファイル、およびその他のあらゆる種類の名前付きエンティティが含まれます。

略語を避けてください。特に、プロジェクト外の読者にとって曖昧であったり馴染みのない略語を使用しないでください。また、単語内の文字を削除することによる略語は使用しないでください。

常に `.py` ファイル名拡張子を使用してください。ダッシュは決して使用しないでください。

<a id="s3.16.1-names-to-avoid"></a>
<a id="3161-names-to-avoid"></a>

<a id="names-to-avoid"></a>
#### 3.16.1 避けるべき名前

-   以下の特定の許可されたケースを除く、1文字の名前:

    -   カウンタやイテレータ（例: `i`, `j`, `k`, `v` など）
    -   `try/except` 文の例外識別子としての `e`。
    -   `with` 文のファイルハンドルとしての `f`
    -   制約のないプライベート[型変数](#typing-type-var)（例: `_T = TypeVar("_T")`, `_P = ParamSpec("_P")`）
    -   参考論文やアルゴリズムの確立された表記法と一致する名前（[数学的表記法](#math-notation)を参照）

    1文字の命名を乱用しないよう注意してください。一般的に言って、記述性は名前のスコープの可視性に比例すべきです。例えば、`i` は5行のコードブロックでは問題ない名前かもしれませんが、複数のネストされたスコープ内では、おそらく曖昧すぎます。

-   パッケージ/モジュール名のダッシュ (`-`)

-   `__double_leading_and_trailing_underscore__` 名前（Python が予約）

-   攻撃的な用語

-   変数の型を不必要に含む名前（例: `id_to_name_dict`）

<a id="s3.16.2-naming-conventions"></a>
<a id="3162-naming-convention"></a>

<a id="naming-conventions"></a>
#### 3.16.2 命名規約

-   「内部」はモジュール内部、またはクラス内の protected や private を意味します。

-   単一のアンダースコア (`_`) を先頭に付けることは、モジュール変数と関数の保護にいくらかのサポートがあります（リンターが protected メンバーアクセスをフラグします）。ユニットテストがテスト対象のモジュールから protected 定数にアクセスすることは問題ないことに注意してください。

-   ダブルアンダースコア (`__`、別名 "dunder") をインスタンス変数やメソッドの先頭に付けると、その変数やメソッドはクラスに対して事実上プライベートになります（名前マングリングを使用）。可読性とテスト容易性に影響し、*本当に*プライベートではないため、使用を推奨しません。単一のアンダースコアを優先してください。

-   関連するクラスとトップレベル関数をモジュール内にまとめて配置してください。Java とは異なり、1モジュールに1クラスに制限する必要はありません。

-   クラス名には CapWords を使用しますが、モジュール名には lower\_with\_under.py を使用してください。CapWords.py という名前の古いモジュールもありますが、モジュールがたまたまクラスと同じ名前の場合に混乱するため、現在は推奨されていません（「待って -- `import StringIO` と書いたのか、`from StringIO import StringIO` と書いたのか？」）。

-   新しい*ユニットテスト*ファイルは PEP 8 準拠の lower\_with\_under メソッド名に従います。例えば、`test_<method_under_test>_<state>` です。CapWords 関数名に従うレガシーモジュールとの一貫性(*)のために、名前の論理的なコンポーネントを区切るために `test` で始まるメソッド名にアンダースコアが表示される場合があります。考えられるパターンの1つは `test<MethodUnderTest>_<state>` です。

<a id="s3.16.3-file-naming"></a>
<a id="3163-file-naming"></a>

<a id="file-naming"></a>
#### 3.16.3 ファイルの命名

Python のファイル名は `.py` 拡張子を持ち、ダッシュ (`-`) を含んではなりません。これにより、インポートおよびユニットテストが可能になります。拡張子なしで実行可能ファイルにアクセスしたい場合は、シンボリックリンクまたは `exec "$0.py" "$@"` を含むシンプルな bash ラッパーを使用してください。

<a id="s3.16.4-guidelines-derived-from-guidos-recommendations"></a>
<a id="3164-guidelines-derived-from-guidos-recommendations"></a>

<a id="guidelines-derived-from-guidos-recommendations"></a>
#### 3.16.4 [Guido](https://en.wikipedia.org/wiki/Guido_van_Rossum) の推奨から導かれたガイドライン

<table rules="all" border="1" summary="Guido の推奨からのガイドライン"
       cellspacing="2" cellpadding="2">

  <tr>
    <th>型</th>
    <th>Public</th>
    <th>Internal</th>
  </tr>

  <tr>
    <td>パッケージ</td>
    <td><code>lower_with_under</code></td>
    <td></td>
  </tr>

  <tr>
    <td>モジュール</td>
    <td><code>lower_with_under</code></td>
    <td><code>_lower_with_under</code></td>
  </tr>

  <tr>
    <td>クラス</td>
    <td><code>CapWords</code></td>
    <td><code>_CapWords</code></td>
  </tr>

  <tr>
    <td>例外</td>
    <td><code>CapWords</code></td>
    <td></td>
  </tr>

  <tr>
    <td>関数</td>
    <td><code>lower_with_under()</code></td>
    <td><code>_lower_with_under()</code></td>
  </tr>

  <tr>
    <td>グローバル/クラス定数</td>
    <td><code>CAPS_WITH_UNDER</code></td>
    <td><code>_CAPS_WITH_UNDER</code></td>
  </tr>

  <tr>
    <td>グローバル/クラス変数</td>
    <td><code>lower_with_under</code></td>
    <td><code>_lower_with_under</code></td>
  </tr>

  <tr>
    <td>インスタンス変数</td>
    <td><code>lower_with_under</code></td>
    <td><code>_lower_with_under</code> (protected)</td>
  </tr>

  <tr>
    <td>メソッド名</td>
    <td><code>lower_with_under()</code></td>
    <td><code>_lower_with_under()</code> (protected)</td>
  </tr>

  <tr>
    <td>関数/メソッドパラメータ</td>
    <td><code>lower_with_under</code></td>
    <td></td>
  </tr>

  <tr>
    <td>ローカル変数</td>
    <td><code>lower_with_under</code></td>
    <td></td>
  </tr>

</table>
<a id="s3.17-main"></a>
<a id="317-main"></a>

<a id="math-notation"></a>
#### 3.16.5 数学的表記法

数学的に重いコードでは、参考論文やアルゴリズムの確立された表記法と一致する場合、スタイルガイドに違反する短い変数名が望ましいです。

確立された表記法に基づく名前を使用する場合:

1.  すべての命名規約のソースを、できれば学術リソース自体へのハイパーリンクと共に、コメントまたはドキュメント文字列に引用してください。ソースにアクセスできない場合は、命名規約を明確にドキュメント化してください。
2.  コンテキスト外で遭遇する可能性がはるかに高いパブリック API には、PEP8 準拠の `descriptive_names` を優先してください。
3.  狭いスコープの `pylint: disable=invalid-name` ディレクティブを使用して警告を抑制してください。少数の変数の場合は、各変数の行末コメントとしてディレクティブを使用してください。それ以上の場合は、ブロックの先頭にディレクティブを適用してください。

<a id="main"></a>
### 3.17 Main

Python では、`pydoc` やユニットテストはモジュールがインポート可能であることを要求します。ファイルが実行可能として使用されることを意図している場合、その主要な機能は `main()` 関数に入れるべきであり、メインプログラムを実行する前に常に `if __name__ == '__main__'` をチェックすべきです。これにより、モジュールがインポートされたときに実行されません。

[absl](https://github.com/abseil/abseil-py) を使用する場合は、`app.run` を使用してください:

```python
from absl import app
...

def main(argv: Sequence[str]):
    # 非フラグ引数を処理
    ...

if __name__ == '__main__':
    app.run(main)
```

それ以外の場合は、以下を使用してください:

```python
def main():
    ...

if __name__ == '__main__':
    main()
```

トップレベルのすべてのコードは、モジュールがインポートされたときに実行されます。ファイルが `pydoc` される際に実行されるべきではない関数の呼び出し、オブジェクトの作成、その他の操作を行わないよう注意してください。

<a id="s3.18-function-length"></a>
<a id="318-function-length"></a>

<a id="function-length"></a>
### 3.18 関数の長さ

小さくて焦点の絞られた関数を優先してください。

長い関数が適切な場合もあることは認識していますので、関数の長さに対してハードリミットは設けません。関数が約40行を超える場合、プログラムの構造を損なわずに分割できるかどうかを考えてください。

長い関数が現在完璧に動作していても、数ヶ月後に誰かが新しい動作を追加するかもしれません。これにより、見つけにくいバグが発生する可能性があります。関数を短くシンプルに保つことで、他の人がコードを読みやすく、変更しやすくなります。

一部のコードで作業する際に、長くて複雑な関数を見つけることがあるかもしれません。既存のコードを変更することを恐れないでください: そのような関数での作業が困難であること、エラーのデバッグが難しいこと、またはいくつかの異なるコンテキストでその一部を使用したい場合は、関数をより小さく管理しやすい部分に分割することを検討してください。

<a id="s3.19-type-annotations"></a>
<a id="319-type-annotations"></a>

<a id="type-annotations"></a>
### 3.19 型アノテーション

<a id="s3.19.1-general-rules"></a>
<a id="s3.19.1-general"></a>
<a id="3191-general-rules"></a>

<a id="typing-general"></a>
#### 3.19.1 一般的なルール

*   [型ヒント](https://docs.python.org/3/library/typing.html)に精通してください。

*   `self` や `cls` のアノテーションは一般的に不要です。適切な型情報のために必要な場合は [`Self`](https://docs.python.org/3/library/typing.html#typing.Self) を使用できます。例えば:

    ```python
    from typing import Self

    class BaseClass:
      @classmethod
      def create(cls) -> Self:
        ...

      def difference(self, other: Self) -> float:
        ...
    ```

*   同様に、`__init__` の戻り値のアノテーションを強制する必要はありません（`None` が唯一の有効なオプションです）。

*   他の変数や戻り値の型が表現できない場合は、`Any` を使用してください。

*   モジュール内のすべての関数にアノテーションを付ける必要はありません。

    -   少なくともパブリック API にはアノテーションを付けてください。
    -   一方での安全性と明確さ、他方での柔軟性の間で適切なバランスを取るために判断を使用してください。
    -   型関連のエラーが発生しやすいコード（以前のバグや複雑さ）にアノテーションを付けてください。
    -   理解しにくいコードにアノテーションを付けてください。
    -   型の観点から安定したコードにアノテーションを付けてください。多くの場合、成熟したコードのすべての関数に柔軟性を失いすぎることなくアノテーションを付けることができます。

<a id="s3.19.2-line-breaking"></a>
<a id="3192-line-breaking"></a>

<a id="typing-line-breaking"></a>
#### 3.19.2 行の折り返し

既存の[インデント](#indentation)ルールに従うようにしてください。

アノテーション後、多くの関数シグネチャは「1行に1パラメータ」になります。戻り値の型にも独自の行を与えるために、最後のパラメータの後にカンマを配置できます。

```python
def my_method(
    self,
    first_var: int,
    second_var: Foo,
    third_var: Bar | None,
) -> int:
  ...
```

変数名と型アノテーションの間ではなく、常に変数間で改行することを優先してください。ただし、すべてが同じ行に収まる場合はそのまま記述してください。

```python
def my_method(self, first_var: int) -> int:
  ...
```

関数名、最後のパラメータ、戻り値の型の組み合わせが長すぎる場合は、新しい行で4スペースインデントしてください。行の折り返しを使用する場合は、各パラメータと戻り値の型をそれぞれ独自の行に配置し、閉じ括弧を `def` と揃えることを優先してください:

```python
Yes:
def my_method(
    self,
    other_arg: MyLongType | None,
) -> tuple[MyLongType1, MyLongType1]:
  ...
```

オプションとして、戻り値の型を最後のパラメータと同じ行に配置できます:

```python
Okay:
def my_method(
    self,
    first_var: int,
    second_var: int) -> dict[OtherLongType, MyLongType]:
  ...
```

`pylint` では閉じ括弧を新しい行に移動して開き括弧と揃えることができますが、これは可読性が低下します。

```python
No:
def my_method(self,
              other_arg: MyLongType | None,
             ) -> dict[OtherLongType, MyLongType]:
  ...
```

上記の例のように、型を分割しないことを優先してください。ただし、時には1行には長すぎる場合があります（サブタイプは分割しないように努めてください）。

```python
def my_method(
    self,
    first_var: tuple[list[MyLongType1],
                     list[MyLongType2]],
    second_var: list[dict[
        MyLongType3, MyLongType4]],
) -> None:
  ...
```

単一の名前と型が長すぎる場合は、型の[エイリアス](#typing-aliases)の使用を検討してください。最後の手段は、コロンの後で改行して4スペースインデントすることです。

```python
Yes:
def my_function(
    long_variable_name:
        long_module_name.LongTypeName,
) -> None:
  ...
```

```python
No:
def my_function(
    long_variable_name: long_module_name.
        LongTypeName,
) -> None:
  ...
```

<a id="s3.19.3-forward-declarations"></a>
<a id="3193-forward-declarations"></a>

<a id="forward-declarations"></a>
#### 3.19.3 前方宣言

まだ定義されていないクラス名（同じモジュールから）を使用する必要がある場合 -- 例えば、そのクラスの宣言内でクラス名が必要な場合、またはコード内で後で定義されるクラスを使用する場合 -- `from __future__ import annotations` を使用するか、クラス名に文字列を使用してください。

```python
Yes:
from __future__ import annotations

class MyClass:
  def __init__(self, stack: Sequence[MyClass], item: OtherClass) -> None:

class OtherClass:
  ...
```

```python
Yes:
class MyClass:
  def __init__(self, stack: Sequence['MyClass'], item: 'OtherClass') -> None:

class OtherClass:
  ...
```

<a id="s3.19.4-default-values"></a>
<a id="3194-default-values"></a>

<a id="typing-default-values"></a>
#### 3.19.4 デフォルト値

[PEP-008](https://peps.python.org/pep-0008/#other-recommendations) に従い、型アノテーションとデフォルト値の両方を持つ引数に対して*のみ* `=` の周りにスペースを使用してください。

```python
Yes:
def func(a: int = 0) -> int:
  ...
```

```python
No:
def func(a:int=0) -> int:
  ...
```

<a id="s3.19.5-nonetype"></a>
<a id="s3.19.5-none-type"></a>
<a id="3195-nonetype"></a>

<a id="none-type"></a>
#### 3.19.5 NoneType

Python の型システムでは、`NoneType` は「第一級」の型であり、型付けの目的では `None` は `NoneType` のエイリアスです。引数が `None` になり得る場合、宣言する必要があります！新しい Python 3.10+ コードで推奨される `|` ユニオン型式、または古い `Optional` と `Union` 構文を使用できます。

暗黙の `a: str = None` を `a: str | None = None` と解釈させるのではなく、明示的な `X | None` を使用してください。以前のバージョンの型チェッカーは `a: str = None` を `a: str | None = None` として解釈することを許可していましたが、もはや推奨される動作ではありません。

```python
Yes:
def modern_or_union(a: str | int | None, b: str | None = None) -> str:
  ...
def union_optional(a: Union[str, int, None], b: Optional[str] = None) -> str:
  ...
```

```python
No:
def nullable_union(a: Union[None, str]) -> str:
  ...
def implicit_optional(a: str = None) -> str:
  ...
```

<a id="s3.19.6-type-aliases"></a>
<a id="s3.19.6-aliases"></a>
<a id="3196-type-aliases"></a>
<a id="typing-aliases"></a>

<a id="type-aliases"></a>
#### 3.19.6 型エイリアス

複雑な型のエイリアスを宣言できます。エイリアスの名前は CapWorded であるべきです。エイリアスがこのモジュール内でのみ使用される場合は \_Private にすべきです。

`: TypeAlias` アノテーションは 3.10+ バージョンでのみサポートされていることに注意してください。

```python
from typing import TypeAlias

_LossAndGradient: TypeAlias = tuple[tf.Tensor, tf.Tensor]
ComplexTFMap: TypeAlias = Mapping[str, _LossAndGradient]
```

<a id="s3.19.7-ignoring-types"></a>
<a id="s3.19.7-ignore"></a>
<a id="3197-ignoring-types"></a>

<a id="typing-ignore"></a>
#### 3.19.7 型の無視

特別なコメント `# type: ignore` で行の型チェックを無効にできます。

`pytype` には特定のエラーに対する無効化オプションがあります（lint に似ています）:

```python
# pytype: disable=attribute-error
```

<a id="s3.19.8-typing-variables"></a>
<a id="s3.19.8-comments"></a>
<a id="3198-typing-internal-variables"></a>

<a id="typing-variables"></a>
#### 3.19.8 型付け変数

<a id="annotated-assignments"></a>
[*アノテーション付き代入*](#annotated-assignments)
:   内部変数の型が推論しにくいまたは不可能な場合は、アノテーション付き代入で型を指定してください -- 変数名と値の間にコロンと型を使用します（デフォルト値を持つ関数引数と同じです）:

    ```python
    a: Foo = SomeUndecoratedFunction()
    ```

<a id="type-comments"></a>
[*型コメント*](#type-comments)
:   コードベースに残っているのを見かけるかもしれませんが（Python 3.6 以前では必要でした）、行末の `# type: <type name>` コメントの使用をこれ以上追加しないでください:

    ```python
    a = SomeUndecoratedFunction()  # type: Foo
    ```

<a id="s3.19.9-tuples-vs-lists"></a>
<a id="s3.19.9-tuples"></a>
<a id="3199-tuples-vs-lists"></a>

<a id="typing-tuples"></a>
#### 3.19.9 タプル vs リスト

型付きリストは単一の型のオブジェクトのみを含むことができます。型付きタプルは、単一の繰り返し型か、異なる型の固定数の要素を持つことができます。後者は関数からの戻り値の型としてよく使用されます。

```python
a: list[int] = [1, 2, 3]
b: tuple[int, ...] = (1, 2, 3)
c: tuple[int, str, float] = (1, "2", 3.5)
```

<a id="s3.19.10-typevars"></a>
<a id="s3.19.10-type-var"></a>
<a id="31910-typevar"></a>
<a id="typing-type-var"></a>

<a id="typevars"></a>
#### 3.19.10 型変数

Python の型システムには[ジェネリクス](https://docs.python.org/3/library/typing.html#generics)があります。`TypeVar` や `ParamSpec` などの型変数は、それらを使用する一般的な方法です。

例:

```python
from collections.abc import Callable
from typing import ParamSpec, TypeVar
_P = ParamSpec("_P")
_T = TypeVar("_T")
...
def next(l: list[_T]) -> _T:
  return l.pop()

def print_when_called(f: Callable[_P, _T]) -> Callable[_P, _T]:
  def inner(*args: _P.args, **kwargs: _P.kwargs) -> _T:
    print("Function was called")
    return f(*args, **kwargs)
  return inner
```

`TypeVar` は制約を付けることができます:

```python
AddableType = TypeVar("AddableType", int, float, str)
def add(a: AddableType, b: AddableType) -> AddableType:
  return a + b
```

`typing` モジュールの一般的な定義済み型変数は `AnyStr` です。`bytes` または `str` であり得、すべて同じ型でなければならない複数のアノテーションに使用してください。

```python
from typing import AnyStr
def check_length(x: AnyStr) -> AnyStr:
  if len(x) <= 42:
    return x
  raise ValueError()
```

型変数は、以下のすべての基準を満たさない限り、記述的な名前を持たなければなりません:

*   外部から見えない
*   制約がない

```python
Yes:
  _T = TypeVar("_T")
  _P = ParamSpec("_P")
  AddableType = TypeVar("AddableType", int, float, str)
  AnyFunction = TypeVar("AnyFunction", bound=Callable)
```

```python
No:
  T = TypeVar("T")
  P = ParamSpec("P")
  _T = TypeVar("_T", int, float, str)
  _F = TypeVar("_F", bound=Callable)
```

<a id="s3.19.11-string-types"></a>
<a id="s3.19.11-strings"></a>
<a id="31911-string-types"></a>

<a id="typing-strings"></a>
#### 3.19.11 文字列型

> 新しいコードでは `typing.Text` を使用しないでください。Python 2/3 互換性のためだけのものです。

文字列/テキストデータには `str` を使用してください。バイナリデータを扱うコードには `bytes` を使用してください。

```python
def deals_with_text_data(x: str) -> str:
  ...
def deals_with_binary_data(x: bytes) -> bytes:
  ...
```

関数のすべての文字列型が常に同じ場合、例えば上のコードで戻り値の型が引数の型と同じ場合は、[AnyStr](#typing-type-var) を使用してください。

<a id="s3.19.12-imports-for-typing"></a>
<a id="s3.19.12-imports"></a>
<a id="31912-imports-for-typing"></a>

<a id="typing-imports"></a>
#### 3.19.12 型付けのためのインポート

静的解析と型チェックをサポートするために使用される `typing` または `collections.abc` モジュールからのシンボル（型、関数、定数を含む）については、常にシンボル自体をインポートしてください。これにより、一般的なアノテーションがより簡潔になり、世界中で使用されている型付けプラクティスと一致します。`typing` と `collections.abc` モジュールからは、1行で複数の特定のシンボルをインポートすることが明示的に許可されています。例えば:

```python
from collections.abc import Mapping, Sequence
from typing import Any, Generic, cast, TYPE_CHECKING
```

このインポート方法はローカル名前空間にアイテムを追加するため、`typing` や `collections.abc` の名前はキーワードと同様に扱い、型付きかどうかにかかわらず Python コード内で定義すべきではありません。モジュール内の型と既存の名前の間に衝突がある場合は、`import x as y` を使用してインポートしてください。

```python
from typing import Any as AnyType
```

関数シグネチャにアノテーションを付ける場合、`list` のような具象型よりも `collections.abc.Sequence` のような抽象コンテナ型を優先してください。具象型を使用する必要がある場合（例えば、型付き要素の `tuple`）、`typing` モジュールのパラメトリック型エイリアス（例: `typing.Tuple`）よりも `tuple` のような組み込み型を優先してください。

```python
from typing import List, Tuple

def transform_coordinates(original: List[Tuple[float, float]]) ->
    List[Tuple[float, float]]:
  ...
```

```python
from collections.abc import Sequence

def transform_coordinates(original: Sequence[tuple[float, float]]) ->
    Sequence[tuple[float, float]]:
  ...
```

<a id="s3.19.13-conditional-imports"></a>
<a id="31913-conditional-imports"></a>

<a id="typing-conditional-imports"></a>
#### 3.19.13 条件付きインポート

型チェックに必要な追加インポートをランタイムで回避する必要がある例外的なケースでのみ、条件付きインポートを使用してください。このパターンは推奨されません。トップレベルのインポートを許可するようにコードをリファクタリングするなどの代替案を優先すべきです。

型アノテーションにのみ必要なインポートは、`if TYPE_CHECKING:` ブロック内に配置できます。

-   条件付きでインポートされた型は文字列として参照する必要があります。これは、アノテーション式が実際に評価される Python 3.6 との前方互換性のためです。
-   型付けにのみ使用されるエンティティのみをここで定義すべきです。これにはエイリアスも含まれます。そうしないと、モジュールがランタイムでインポートされないため、ランタイムエラーになります。
-   ブロックはすべての通常のインポートの直後に配置すべきです。
-   型付けインポートリストに空行があってはなりません。
-   このリストを通常のインポートリストと同様にソートしてください。
```python
import typing
if typing.TYPE_CHECKING:
  import sketch
def f(x: "sketch.Sketch"): ...
```

<a id="s3.19.14-circular-dependencies"></a>
<a id="s3.19.14-circular-deps"></a>
<a id="31914-circular-dependencies"></a>

<a id="typing-circular-deps"></a>
#### 3.19.14 循環依存

型付けによって引き起こされる循環依存はコードの悪臭です。そのようなコードはリファクタリングの良い候補です。技術的には循環依存を維持することは可能ですが、各モジュールが他に依存する必要があるため、さまざまなビルドシステムがそれを許可しません。

循環依存のインポートを作成するモジュールを `Any` で置き換えてください。意味のある名前で[エイリアス](#typing-aliases)を設定し、このモジュールの実際の型名を使用してください（`Any` の任意の属性は `Any` です）。エイリアスの定義は最後のインポートから1行離して配置すべきです。

```python
from typing import Any

some_mod = Any  # some_mod.py がこのモジュールをインポートしている。
...

def my_method(self, var: "some_mod.SomeType") -> None:
  ...
```

<a id="typing-generics"></a>
<a id="s3.19.15-generics"></a>
<a id="31915-generics"></a>

<a id="generics"></a>
#### 3.19.15 ジェネリクス

アノテーションを付ける際は、パラメータリストで[ジェネリック](https://docs.python.org/3/library/typing.html#generics)型の型パラメータを指定することを優先してください。そうしないと、ジェネリクスのパラメータは [`Any`](https://docs.python.org/3/library/typing.html#the-any-type) と仮定されます。

```python
# Yes:
def get_names(employee_ids: Sequence[int]) -> Mapping[int, str]:
  ...
```

```python
# No:
# これは get_names(employee_ids: Sequence[Any]) -> Mapping[Any, Any] と解釈される
def get_names(employee_ids: Sequence) -> Mapping:
  ...
```

ジェネリックの最適な型パラメータが `Any` の場合は、明示的にしてください。ただし、多くの場合 [`TypeVar`](#typing-type-var) の方が適切かもしれないことを覚えておいてください:

```python
# No:
def get_names(employee_ids: Sequence[Any]) -> Mapping[Any, str]:
  """指定された ID に対する従業員 ID から従業員名へのマッピングを返します。"""
```

```python
# Yes:
_T = TypeVar('_T')
def get_names(employee_ids: Sequence[_T]) -> Mapping[_T, str]:
  """指定された ID に対する従業員 ID から従業員名へのマッピングを返します。"""
```


<a id="4-parting-words"></a>

<a id="consistency"></a>
## 4 最後に

*一貫性を保ってください*。

コードを編集する場合、数分かけて周囲のコードを見て、そのスタイルを判断してください。インデックス変数名に `_idx` サフィックスを使用しているなら、あなたもそうすべきです。コメントの周りにハッシュマークの小さな箱がある場合は、あなたのコメントにもハッシュマークの小さな箱を付けてください。

スタイルガイドラインを持つことのポイントは、コーディングの共通の語彙を持つことで、人々が*どう言っているか*ではなく*何を言っているか*に集中できるようにすることです。ここではグローバルなスタイルルールを提示して、人々が語彙を知るようにしていますが、ローカルなスタイルも重要です。ファイルに追加したコードが周囲の既存のコードとは大きく異なって見える場合、読者がそれを読もうとしたときにリズムが乱れます。

ただし、一貫性には限界があります。一貫性はよりローカルに、そしてグローバルスタイルで指定されていない選択肢により強く適用されます。一貫性は一般的に、新しいスタイルの利点や、コードベースが新しいスタイルに収束する傾向を考慮せずに、古いスタイルで物事を行う正当化として使用すべきではありません。
