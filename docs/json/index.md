# SATELLITE JSON スタイルガイド

リビジョン 0.9

---

## 重要な注意事項

### このガイドの詳細表示について

このスタイルガイドには、初期状態では非表示になっている詳細が多数含まれています。原文では三角形のアイコンでマークされており、クリックすると展開されます。本翻訳版ではすべての詳細を展開した状態で記載しています。

---

## はじめに

このスタイルガイドは、SATELLITEにおけるJSON APIの構築に関するガイドラインと推奨事項を文書化したものです。一般的に、JSON APIは [JSON.org](https://www.json.org) にある仕様に従うべきです。このスタイルガイドは、SATELLITEのJSON APIが標準的な外観と操作感を持つように、特定のケースを明確化し標準化するものです。これらのガイドラインは、RPCベースおよびRESTベースの両方のAPIにおけるJSONリクエストとレスポンスに適用されます。

---

## 定義

このスタイルガイドの目的上、以下の用語を定義します：

- **property（プロパティ）** - JSONオブジェクト内の名前/値のペア
- **property name（プロパティ名）** - プロパティの名前（キー）の部分
- **property value（プロパティ値）** - プロパティの値の部分

```json
{
  // 名前/値のペアがまとめて1つの「プロパティ」になる。
  "propertyName": "propertyValue"
}
```

JavaScriptの `number` 型はすべての浮動小数点数を包含する広範な型です。このガイドでは、`number` はJavaScriptの `number` 型を指し、`integer` は整数を指すものとします。

---

## 一般ガイドライン

### コメント

**JSONオブジェクトにコメントを含めてはならない。**

コメントはJSONオブジェクトに含めるべきではありません。このスタイルガイドの一部の例にはコメントが含まれていますが、これは例を説明するためのみに使用されています。

```json
// 悪い例
{
  // 以下の例にはコメントが含まれていることがありますが、
  // JSONにコメントを含めないでください。
  "propertyName": "propertyValue"
}
```

### ダブルクォート

**ダブルクォートを使用する。**

プロパティにクォートが必要な場合は、ダブルクォートを使用しなければなりません。すべてのプロパティ名はダブルクォートで囲む必要があります。文字列型のプロパティ値はダブルクォートで囲む必要があります。それ以外の値の型（真偽値や数値など）はダブルクォートで囲むべきではありません。

### フラットなデータ vs 構造化された階層

**データを便宜的に恣意的にグループ化すべきではない。**

データ要素はJSON表現において「フラット化」すべきです。データを便宜的に恣意的にグループ化すべきではありません。

ただし、単一の構造を表すプロパティの集合のような場合は、構造化された階層を維持することが理にかなう場合もあります。このようなケースは慎重に検討し、意味的に妥当な場合にのみ使用すべきです。例えば、住所は2つの方法で表現できますが、構造化された方法のほうが開発者にとってはおそらく分かりやすいでしょう：

フラットな住所：

```json
{
  "company": "SATELLITE",
  "website": "https://www.google.com/",
  "addressLine1": "111 8th Ave",
  "addressLine2": "4th Floor",
  "state": "NY",
  "city": "New York",
  "zip": "10011"
}
```

構造化された住所：

```json
{
  "company": "SATELLITE",
  "website": "https://www.google.com/",
  "address": {
    "line1": "111 8th Ave",
    "line2": "4th Floor",
    "state": "NY",
    "city": "New York",
    "zip": "10011"
  }
}
```

---

## プロパティ名のガイドライン

### プロパティ名の形式

**意味のあるプロパティ名を選択する。**

プロパティ名は以下のガイドラインに従う必要があります：

- プロパティ名は、定義された意味を持つ意味のある名前であるべきです。
- プロパティ名はキャメルケースのASCII文字列でなければなりません。
- 最初の文字は、英字、アンダースコア（`_`）、またはドル記号（`$`）でなければなりません。
- 2番目以降の文字は、英字、数字、アンダースコア、またはドル記号を使用できます。
- JavaScriptの予約語は避けるべきです（JavaScriptの予約語リストは後述します）。

これらのガイドラインは、JavaScript識別子の命名ガイドラインに準拠しています。これにより、JavaScriptクライアントがドット記法でプロパティにアクセスできます（例：`result.thisIsAnInstanceVariable`）。以下はプロパティを1つ持つオブジェクトの例です：

```json
{
  "thisPropertyIsAnIdentifier": "identifier value"
}
```

### JSONマップのキー名

**JSONマップではキー名に任意のUnicode文字を使用できる。**

JSONオブジェクトがマップとして使用される場合、プロパティ名の命名規則は適用されません。マップ（連想配列とも呼ばれます）は、キーを使用して対応する値にアクセスする、任意のキー/値ペアを持つデータ型です。JSONオブジェクトとJSONマップは実行時には同じに見えますが、この区別はAPIの設計において重要です。APIのドキュメントで、JSONオブジェクトがマップとして使用される場合はその旨を示すべきです。

マップのキーはプロパティ名の命名ガイドラインに従う必要はありません。マップのキーには任意のUnicode文字を含めることができます。クライアントは、マップで一般的なブラケット記法を使用してこれらのプロパティにアクセスできます（例：`result.thumbnails["72"]`）。

```json
{
  // "address"プロパティは住所の各部分を保持するサブオブジェクト
  "address": {
    "addressLine1": "123 Anystreet",
    "city": "Anytown",
    "state": "XX",
    "zip": "00000"
  },
  // "thumbnails"プロパティはピクセルサイズとそのサイズのサムネイルURLを
  // マッピングするマップ
  "thumbnails": {
    "72": "https://url.to.72px.thumbnail",
    "144": "https://url.to.144px.thumbnail"
  }
}
```

### 予約済みプロパティ名

**特定のプロパティ名はサービス間で一貫して使用するために予約されている。**

予約済みプロパティ名の詳細と完全なリストは、このガイドの後半で説明します。サービスは、定義された意味以外の目的でこれらのプロパティ名を使用することを避けるべきです。

### 単数形 vs 複数形のプロパティ名

**配列型には複数形のプロパティ名を使用する。その他のプロパティ名はすべて単数形にする。**

配列には通常複数の項目が含まれるため、複数形のプロパティ名がこれを反映します。この例は以下の予約名で確認できます。`items` プロパティ名は、アイテムオブジェクトの配列を表すため複数形です。その他のほとんどのフィールドは単数形です。

例外もあります。特に数値のプロパティ値を参照する場合です。例えば、予約名の中で `totalItems` は `totalItem` よりも自然です。ただし、技術的にはこれはスタイルガイドに違反していません。`totalItems` は `totalOfItems`（「アイテムの合計」）と解釈でき、`total` は単数形（スタイルガイドに準拠）で、`OfItems` は合計を修飾しています。フィールド名を `itemCount` に変更すれば単数形に見えます。

```json
{
  // 単数形
  "author": "lisa",
  // 兄弟姉妹の配列、複数形
  "siblings": ["bart", "maggie"],
  // "totalItem"は不自然
  "totalItems": 10,
  // "itemCount"のほうが良いかもしれない
  "itemCount": 10
}
```

### 命名の衝突

**新しいプロパティ名を選択するか、APIをバージョニングすることで命名の衝突を回避する。**

将来、予約リストに新しいプロパティが追加される可能性があります。JSONには名前空間の概念がありません。命名の衝突がある場合、通常は新しいプロパティ名を選択するか、バージョニングすることで解決できます。例えば、以下のJSONオブジェクトから始めるとします：

```json
{
  "apiVersion": "1.0",
  "data": {
    "recipeName": "pizza",
    "ingredients": ["tomatoes", "cheese", "sausage"]
  }
}
```

将来 `ingredients` を予約語にしたい場合、2つの方法があります：

1）別の名前を選択する：

```json
{
  "apiVersion": "1.0",
  "data": {
    "recipeName": "pizza",
    "ingredientsData": "Some new property",
    "ingredients": ["tomatoes", "cheese", "sausage"]
  }
}
```

2）メジャーバージョンの境界でプロパティ名を変更する：

```json
{
  "apiVersion": "2.0",
  "data": {
    "recipeName": "pizza",
    "ingredients": "Some new property",
    "recipeIngredients": ["tomatos", "cheese", "sausage"]
  }
}
```

---

## プロパティ値のガイドライン

### プロパティ値の形式

**プロパティ値は、真偽値、数値、Unicode文字列、オブジェクト、配列、または `null` でなければならない。**

[JSON.org](https://www.json.org) の仕様は、プロパティ値で許可されるデータの型を正確に規定しています。これには、真偽値、数値、Unicode文字列、オブジェクト、配列、`null` が含まれます。JavaScript式は許可されません。APIはすべての値についてこの仕様をサポートし、特定のプロパティに最も適切なデータ型を選択すべきです（数値を表すには数値を使用するなど）。

良い例：

```json
{
  "canPigsFly": null,
  "areWeThereYet": false,
  "answerToLife": 42,
  "name": "Bart",
  "moreData": {},
  "things": []
}
```

悪い例：

```json
// 悪い例
{
  "aVariableName": aVariableName,
  "functionFoo": function() { return 1; }
}
```

### 空の値 / null 値

**空の値や `null` 値は削除を検討する。**

プロパティがオプションであったり、空または `null` の値を持つ場合、そのプロパティをJSONから削除することを検討してください。ただし、そのプロパティの存在に強い意味的理由がある場合は除きます。

```json
{
  "volume": 10,

  // "balance"プロパティの値はゼロだが、残しておくべきである。
  // "0"は「均等なバランス」を意味するため（値は左バランスの"-1"や
  // 右バランスの"+1"になりうる）。
  "balance": 0

  // "currentlyPlaying"プロパティはnullなので省略可能。
  // "currentlyPlaying": null
}
```

### 列挙型の値

**列挙型の値は文字列として表現すべきである。**

APIが成長するにつれ、列挙型の値は追加、削除、変更される可能性があります。文字列を列挙型の値として使用することで、下流のクライアントが列挙型の値の変更を優雅に処理できるようになります。

Javaコード：

```java
public enum Color {
  WHITE,
  BLACK,
  RED,
  YELLOW,
  BLUE
}
```

JSONオブジェクト：

```json
{
  "color": "WHITE"
}
```

---

## プロパティ値のデータ型

前述の通り、プロパティ値の型は真偽値、数値、文字列、オブジェクト、配列、または `null` でなければなりません。ただし、特定の値を扱う際に標準的なデータ型のセットを定義しておくと便利です。これらのデータ型は常に文字列ですが、容易にパースできるように特定の形式でフォーマットされます。

### 日付のプロパティ値

**日付はRFC 3339で推奨される形式でフォーマットすべきである。**

日付は [RFC 3339](https://www.ietf.org/rfc/rfc3339.txt) で推奨される形式の文字列にすべきです。

```json
{
  "lastUpdate": "2007-11-06T16:34:41.000Z"
}
```

### 時間の長さのプロパティ値

**時間の長さはISO 8601で推奨される形式でフォーマットすべきである。**

時間の長さの値は [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations) で推奨される形式の文字列にすべきです。

```json
{
  // 3年6ヶ月4日12時間30分5秒
  "duration": "P3Y6M4DT12H30M5S"
}
```

### 緯度/経度のプロパティ値

**緯度/経度はISO 6709で推奨される形式でフォーマットすべきである。**

緯度/経度は [ISO 6709](https://en.wikipedia.org/wiki/ISO_6709) で推奨される形式の文字列にすべきです。さらに、±DD.DDDD±DDD.DDDD の度数形式が推奨されます。

```json
{
  // 自由の女神の緯度/経度
  "statueOfLiberty": "+40.6894-074.0447"
}
```

---

## JSON構造と予約済みプロパティ名

API間で一貫したインターフェースを維持するために、JSONオブジェクトは以下に示す構造に従うべきです。この構造はJSONで行われるリクエストとレスポンスの両方に適用されます。この構造内には、特定の用途のために予約されたプロパティ名がいくつかあります。これらのプロパティは必須ではありません。つまり、各予約プロパティは0回または1回出現する可能性があります。ただし、サービスがこれらのプロパティを必要とする場合、この命名規則に従うことが推奨されます。以下はJSON構造のスキーマで、[Orderly](http://orderly-json.org/) 形式で表現されています（これはさらに [JSONSchema](http://json-schema.org/) にコンパイルできます）。このガイドの最後にJSON構造の例を示します。

```
object {
  string apiVersion?;
  string context?;
  string id?;
  string method?;
  object {
    string id?
  }* params?;
  object {
    string kind?;
    string fields?;
    string etag?;
    string id?;
    string lang?;
    string updated?; # RFC 3339でフォーマットされた日付
    boolean deleted?;
    integer currentItemCount?;
    integer itemsPerPage?;
    integer startIndex?;
    integer totalItems?;
    integer pageIndex?;
    integer totalPages?;
    string pageLinkTemplate /^https?:/ ?;
    object {}* next?;
    string nextLink?;
    object {}* previous?;
    string previousLink?;
    object {}* self?;
    string selfLink?;
    object {}* edit?;
    string editLink?;
    array [
      object {}*;
    ] items?;
  }* data?;
  object {
    integer code?;
    string message?;
    array [
      object {
        string domain?;
        string reason?;
        string message?;
        string location?;
        string locationType?;
        string extendedHelp?;
        string sendReport?;
      }*;
    ] errors?;
  }* error?;
}*;
```

JSONオブジェクトにはいくつかのトップレベルプロパティがあり、その後に `data` オブジェクトまたは `error` オブジェクトのいずれか（両方ではなく）が続きます。各プロパティの説明は以下の通りです。

---

## トップレベルの予約済みプロパティ名

JSONオブジェクトのトップレベルには以下のプロパティを含めることができます。

### apiVersion

プロパティ値の型：string
親：-

リクエストではサービスAPIの希望バージョンを表し、レスポンスでは提供されるサービスAPIのバージョンを表します。`apiVersion` は常に存在すべきです。これはデータのバージョンとは関係ありません。データのバージョニングはetagなどの別のメカニズムで処理すべきです。

例：

```json
{ "apiVersion": "2.1" }
```

### context

プロパティ値の型：string
親：-

クライアントがこの値を設定し、サーバーがレスポンスでデータをエコーバックします。これはJSON-Pやバッチ処理の場面で有用であり、ユーザーが `context` を使用してレスポンスとリクエストを紐付けることができます。このプロパティはトップレベルプロパティです。なぜなら、レスポンスが成功であってもエラーであっても `context` は存在すべきだからです。`context` は `id` とは異なり、`context` はユーザーが指定するのに対し、`id` はサービスが割り当てます。

例：

リクエスト #1：
```
https://www.google.com/myapi?context=bart
```

リクエスト #2：
```
https://www.google.com/myapi?context=lisa
```

レスポンス #1：
```json
{
  "context": "bart",
  "data": {
    "items": []
  }
}
```

レスポンス #2：
```json
{
  "context": "lisa",
  "data": {
    "items": []
  }
}
```

両方のレスポンスを処理する共通のJavaScriptハンドラーコード：

```javascript
function handleResponse(response) {
  if (response.result.context == "bart") {
    // ページの「Bart」セクションを更新する。
  } else if (response.result.context == "lisa") {
    // ページの「Lisa」セクションを更新する。
  }
}
```

### id

プロパティ値の型：string
親：-

レスポンスに対するサーバー提供の識別子です（レスポンスが成功かエラーかに関わらず）。これはサーバーログとクライアントで受信した個々のレスポンスを紐付けるために有用です。

例：

```json
{ "id": "1" }
```

### method

プロパティ値の型：string
親：-

データに対して実行する操作、または実行された操作を表します。JSONリクエストの場合、`method` プロパティはデータに対して実行する操作を示すために使用できます。JSONレスポンスの場合、`method` プロパティはデータに対して実行された操作を示すことができます。

JSON-RPCリクエストにおいて `method` が `params` プロパティに対して実行する操作を示す例：

```json
{
  "method": "people.get",
  "params": {
    "userId": "@me",
    "groupId": "@self"
  }
}
```

### params

プロパティ値の型：object
親：-

このオブジェクトはRPCリクエストに送信する入力パラメータのマップとして機能します。`method` プロパティと組み合わせてRPC関数を実行するために使用できます。RPC関数がパラメータを必要としない場合、このプロパティは省略できます。

例：

```json
{
  "method": "people.get",
  "params": {
    "userId": "@me",
    "groupId": "@self"
  }
}
```

### data

プロパティ値の型：object
親：-

レスポンスからのすべてのデータのコンテナです。このプロパティ自体にも多くの予約プロパティ名があり、以下で説明します。サービスはこのオブジェクトに独自のデータを自由に追加できます。JSONレスポンスには `data` オブジェクトまたは `error` オブジェクトのいずれかを含めるべきですが、両方は含めません。`data` と `error` の両方が存在する場合、`error` オブジェクトが優先されます。

### error

プロパティ値の型：object
親：-

エラーが発生したことを示し、エラーの詳細を含みます。エラー形式は、サービスから返される1つまたは複数のエラーをサポートします。JSONレスポンスには `data` オブジェクトまたは `error` オブジェクトのいずれかを含めるべきですが、両方は含めません。`data` と `error` の両方が存在する場合、`error` オブジェクトが優先されます。

例：

```json
{
  "apiVersion": "2.0",
  "error": {
    "code": 404,
    "message": "File Not Found",
    "errors": [{
      "domain": "Calendar",
      "reason": "ResourceNotFoundException",
      "message": "File Not Found"
    }]
  }
}
```

---

## dataオブジェクトの予約済みプロパティ名

JSONオブジェクトの `data` プロパティには以下のプロパティを含めることができます。

### data.kind

プロパティ値の型：string
親：`data`

`kind` プロパティは、この特定のオブジェクトがどのような種類の情報を格納しているかを示すガイドとして機能します。`data` レベル、`items` レベル、またはさまざまな種類のオブジェクトを区別するのに役立つ任意のオブジェクト内に存在できます。`kind` オブジェクトが存在する場合、オブジェクト内の最初のプロパティであるべきです（詳細については後述の「プロパティの順序」セクションを参照）。

例：

```json
// "Kind"はPicasa APIで"album"を示す。
{"data": {"kind": "album"}}
```

### data.fields

プロパティ値の型：string
親：`data`

部分的なGETを行う際のレスポンスに存在するフィールド、または部分的なPATCHを行う際のリクエストに存在するフィールドを表します。このプロパティは部分的なGET/PATCHの時のみ存在すべきであり、空であるべきではありません。

例：

```json
{
  "data": {
    "kind": "user",
    "fields": "author,id",
    "id": "bart",
    "author": "Bart"
  }
}
```

### data.etag

プロパティ値の型：string
親：`data`

レスポンスのetagを表します。GData APIにおけるETagの詳細については、[https://code.google.com/apis/gdata/docs/2.0/reference.html#ResourceVersioning](https://code.google.com/apis/gdata/docs/2.0/reference.html#ResourceVersioning) を参照してください。

例：

```json
{"data": {"etag": "W/\"C0QBRXcycSp7ImA9WxRVFUk.\""}}
```

### data.id

プロパティ値の型：string
親：`data`

オブジェクトを参照するためのグローバルに一意な文字列です。`id` プロパティの具体的な詳細はサービスに委ねられます。

例：

```json
{"data": {"id": "12345"}}
```

### data.lang

プロパティ値の型：string（BCP 47で指定される形式）
親：`data`（または任意の子要素）

このオブジェクト内の残りのプロパティの言語を示します。このプロパティはHTMLの `lang` プロパティやXMLの `xml:lang` プロパティを模倣しています。値は [BCP 47](https://www.rfc-editor.org/rfc/bcp/bcp47.txt) で定義された言語値であるべきです。単一のJSONオブジェクトに複数の言語のデータが含まれる場合、`lang` プロパティの適切な配置場所の開発と文書化はサービスの責任です。

例：

```json
{"data": {
  "items": [
    { "lang": "en",
      "title": "Hello world!" },
    { "lang": "fr",
      "title": "Bonjour monde!" }
  ]}
}
```

### data.updated

プロパティ値の型：string（RFC 3339で指定される形式）
親：`data`

サービスによって定義された、アイテムが最後に更新された日時（[RFC 3339](https://www.ietf.org/rfc/rfc3339.txt)）を示します。

例：

```json
{"data": {"updated": "2007-11-06T16:34:41.000Z"}}
```

### data.deleted

プロパティ値の型：boolean
親：`data`（または任意の子要素）

マーカー要素であり、存在する場合、含まれるエントリが削除されたことを示します。`deleted` が存在する場合、その値は `true` でなければなりません。`false` の値は混乱を招く可能性があるため避けるべきです。

例：

```json
{"data": {
  "items": [
    { "title": "A deleted entry",
      "deleted": true
    }
  ]}
}
```

### data.items

プロパティ値の型：array
親：`data`

プロパティ名 `items` は、アイテムの配列を表すために予約されています（例：Picasaの写真、YouTubeの動画）。この構造は、現在の結果に関連するコレクションの標準的な配置場所を提供することを目的としています。例えば、JSON出力は `items` 配列でページングする汎用的なページネーションシステムに接続できます。`items` が存在する場合、`data` オブジェクト内の最後のプロパティであるべきです（詳細については後述の「プロパティの順序」セクションを参照）。

例：

```json
{
  "data": {
    "items": [
      { /* オブジェクト #1 */ },
      { /* オブジェクト #2 */ }
    ]
  }
}
```

---

## ページングの予約済みプロパティ名

以下のプロパティは `data` オブジェクト内に配置され、アイテムリストのページングに使用されます。一部の用語や概念は [OpenSearch仕様](https://www.opensearch.org/) から借用しています。

以下のページングプロパティにより、さまざまなスタイルのページングが可能です：

- **前へ/次へページング** - ユーザーが一度に1ページずつリストを前後に移動できます。`nextLink` と `previousLink` プロパティ（後述の「リンクの予約済みプロパティ名」セクションで説明）がこのスタイルのページングに使用されます。
- **インデックスベースのページング** - ユーザーがアイテムリスト内の特定のアイテム位置に直接ジャンプできます。例えば、アイテム200から始まる10アイテムを読み込むために、開発者はクエリ文字列 `?startIndex=200` を持つURLにユーザーを誘導できます。
- **ページベースのページング** - ユーザーがアイテム内の特定のページに直接ジャンプできます。これはインデックスベースのページングに似ていますが、新しいページのアイテムインデックスを計算する追加の手順を省きます。例えば、アイテム番号200にジャンプする代わりに、開発者はページ20にジャンプできます。ページベースのページング中のURLではクエリ文字列 `?page=1` や `?page=20` を使用できます。`pageIndex` と `totalPages` プロパティがこのスタイルのページングに使用されます。

これらのプロパティを使用してページングを実装する例は、このガイドの最後に示します。

### data.currentItemCount

プロパティ値の型：integer
親：`data`

この結果セット内のアイテム数です。`items.length` と等しくなるべきであり、便利なプロパティとして提供されます。例えば、開発者が検索アイテムのセットをリクエストし、ページあたり10アイテムを要求したとします。その検索の総セットには14アイテムあります。アイテムの最初のページには10アイテムが含まれるため、`itemsPerPage` と `currentItemCount` はどちらも「10」になります。次のページには残りの4アイテムが含まれます。`itemsPerPage` は依然として「10」ですが、`currentItemCount` は「4」になります。

例：

```json
{
  "data": {
    // "itemsPerPage"は必ずしも"currentItemCount"と一致しない
    "itemsPerPage": 10,
    "currentItemCount": 4
  }
}
```

### data.itemsPerPage

プロパティ値の型：integer
親：`data`

結果内のアイテム数です。これは必ずしも `data.items` 配列のサイズではありません。アイテムの最後のページを表示している場合、`data.items` のサイズは `itemsPerPage` より小さい可能性があります。ただし、`data.items` のサイズは `itemsPerPage` を超えるべきではありません。

例：

```json
{
  "data": {
    "itemsPerPage": 10
  }
}
```

### data.startIndex

プロパティ値の型：integer
親：`data`

`data.items` の最初のアイテムのインデックスです。一貫性のため、`startIndex` は1ベースであるべきです。例えば、アイテムの最初のセットの最初のアイテムは `startIndex` が1であるべきです。ユーザーが次のデータセットをリクエストした場合、`startIndex` は10になる可能性があります。

例：

```json
{
  "data": {
    "startIndex": 1
  }
}
```

### data.totalItems

プロパティ値の型：integer
親：`data`

このセットで利用可能なアイテムの総数です。例えば、ユーザーが100件のブログ投稿を持っている場合、レスポンスには10アイテムしか含まれないかもしれませんが、`totalItems` は100になります。

例：

```json
{
  "data": {
    "totalItems": 100
  }
}
```

### data.pagingLinkTemplate

プロパティ値の型：string
親：`data`

ユーザーが後続のページングリンクを計算する方法を示すURIテンプレートです。URIテンプレートにはいくつかの予約変数名もあります：読み込むアイテム番号を表す `{index}` と、読み込むページ番号を表す `{pageIndex}` です。

例：

```json
{
  "data": {
    "pagingLinkTemplate": "https://www.google.com/search/hl=en&q=chicago+style+pizza&start={index}&sa=N"
  }
}
```

### data.pageIndex

プロパティ値の型：integer
親：`data`

アイテムの現在のページのインデックスです。一貫性のため、`pageIndex` は1ベースであるべきです。例えば、アイテムの最初のページの `pageIndex` は1です。`pageIndex` はアイテムベースのページングプロパティから計算することもできます：`pageIndex = floor(startIndex / itemsPerPage) + 1`。

例：

```json
{
  "data": {
    "pageIndex": 1
  }
}
```

### data.totalPages

プロパティ値の型：integer
親：`data`

結果セット内の総ページ数です。`totalPages` はアイテムベースのページングプロパティから計算することもできます：`totalPages = ceiling(totalItems / itemsPerPage)`。

例：

```json
{
  "data": {
    "totalPages": 50
  }
}
```

---

## リンクの予約済みプロパティ名

以下のプロパティは `data` オブジェクト内に配置され、他のリソースへの参照を表します。リンクプロパティには2つの形式があります：1）任意の種類の参照（JSON-RPCオブジェクトなど）を含むことができるオブジェクト、2）リソースへのURIを表すURI文字列（常に「Link」で終わります）。

### data.self / data.selfLink

プロパティ値の型：object / string
親：`data`

selfリンクはアイテムのデータを取得するために使用できます。例えば、ユーザーのPicasaアルバムのリストでは、`items` 配列内の各アルバムオブジェクトに、その特定のアルバムに関連するデータを取得するために使用できる `selfLink` を含めることができます。

例：

```json
{
  "data": {
    "self": { },
    "selfLink": "https://www.google.com/feeds/album/1234"
  }
}
```

### data.edit / data.editLink

プロパティ値の型：object / string
親：`data`

editリンクは、ユーザーが更新または削除リクエストを送信できる場所を示します。これはRESTベースのAPIで有用です。このリンクはユーザーがこのアイテムを更新/削除できる場合にのみ存在する必要があります。

例：

```json
{
  "data": {
    "edit": { },
    "editLink": "https://www.google.com/feeds/album/1234/edit"
  }
}
```

### data.next / data.nextLink

プロパティ値の型：object / string
親：`data`

nextリンクは、さらにデータを取得する方法を示します。次のデータセットを読み込む場所を指します。`itemsPerPage`、`startIndex`、`totalItems` プロパティと組み合わせて、データのページングに使用できます。

例：

```json
{
  "data": {
    "next": { },
    "nextLink": "https://www.google.com/feeds/album/1234/next"
  }
}
```

### data.previous / data.previousLink

プロパティ値の型：object / string
親：`data`

previousリンクは、さらにデータを取得する方法を示します。前のデータセットを読み込む場所を指します。`itemsPerPage`、`startIndex`、`totalItems` プロパティと組み合わせて、データのページングに使用できます。

例：

```json
{
  "data": {
    "previous": { },
    "previousLink": "https://www.google.com/feeds/album/1234/next"
  }
}
```

---

## errorオブジェクトの予約済みプロパティ名

JSONオブジェクトの `error` プロパティには以下のプロパティを含めることができます。

### error.code

プロパティ値の型：integer
親：`error`

このエラーのコードを表します。このプロパティの値は通常HTTPレスポンスコードになります。複数のエラーがある場合、`code` は最初のエラーのエラーコードになります。

例：

```json
{
  "error":{
    "code": 404
  }
}
```

### error.message

プロパティ値の型：string
親：`error`

エラーに関する詳細情報を提供する、人間が読めるメッセージです。複数のエラーがある場合、`message` は最初のエラーのメッセージになります。

例：

```json
{
  "error":{
    "message": "File Not Found"
  }
}
```

### error.errors

プロパティ値の型：array
親：`error`

エラーに関する追加情報のコンテナです。サービスが複数のエラーを返す場合、`errors` 配列の各要素は異なるエラーを表します。

例：

```json
{ "error": { "errors": [] } }
```

### error.errors[].domain

プロパティ値の型：string
親：`error.errors`

このエラーを発生させたサービスの一意な識別子です。これにより、サービス固有のエラー（例：カレンダーへのイベント挿入エラー）と一般的なプロトコルエラー（例：ファイルが見つからない）を区別できます。

例：

```json
{
  "error":{
    "errors": [{"domain": "Calendar"}]
  }
}
```

### error.errors[].reason

プロパティ値の型：string
親：`error.errors`

このエラーの一意な識別子です。`error.code` プロパティとは異なり、これはHTTPレスポンスコードではありません。

例：

```json
{
  "error":{
    "errors": [{"reason": "ResourceNotFoundException"}]
  }
}
```

### error.errors[].message

プロパティ値の型：string
親：`error.errors`

エラーに関する詳細情報を提供する、人間が読めるメッセージです。エラーが1つだけの場合、このフィールドは `error.message` と一致します。

例：

```json
{
  "error":{
    "code": 404,
    "message": "File Not Found",
    "errors": [{"message": "File Not Found"}]
  }
}
```

### error.errors[].location

プロパティ値の型：string
親：`error.errors`

エラーの場所です（その値の解釈は `locationType` に依存します）。

例：

```json
{
  "error":{
    "errors": [{"location": ""}]
  }
}
```

### error.errors[].locationType

プロパティ値の型：string
親：`error.errors`

`location` プロパティをどのように解釈すべきかを示します。

例：

```json
{
  "error":{
    "errors": [{"locationType": ""}]
  }
}
```

### error.errors[].extendedHelp

プロパティ値の型：string
親：`error.errors`

エラーについてさらに詳しい情報を提供する可能性のあるヘルプテキストのURIです。

例：

```json
{
  "error":{
    "errors": [{"extendedHelp": "https://url.to.more.details.example.com/"}]
  }
}
```

### error.errors[].sendReport

プロパティ値の型：string
親：`error.errors`

エラー状態に関するデータを収集するためにサービスが使用するレポートフォームのURIです。このURIにはリクエストを説明するパラメータがプリロードされているべきです。

例：

```json
{
  "error":{
    "errors": [{"sendReport": "https://report.example.com/"}]
  }
}
```

---

## プロパティの順序

プロパティはJSONオブジェクト内で任意の順序にすることができます。ただし、場合によってはプロパティの順序がパーサーによるデータの迅速な解釈を助け、パフォーマンスの向上につながることがあります。一例として、パフォーマンスとメモリが重要で不必要なパースを避けるべきモバイル環境でのプルパーサーがあります。

### kindプロパティ

**`kind` はオブジェクト内の最初のプロパティであるべきである。**

パーサーが生のJSONストリームを特定のオブジェクトにパースする責任があるとします。`kind` プロパティは、パーサーが適切なオブジェクトをインスタンス化するためのガイドとなります。したがって、JSONオブジェクト内の最初のプロパティであるべきです。これはオブジェクトが `kind` プロパティを持つ場合にのみ適用されます（通常 `data` と `items` プロパティ内に見られます）。

### itemsプロパティ

**`items` は `data` オブジェクト内の最後のプロパティであるべきである。**

これにより、各個別アイテムを読み取る前にコレクションのすべてのプロパティを読み取ることができます。アイテムが多数ある場合、開発者がデータからのフィールドのみを必要とする場合に、それらのアイテムを不必要にパースすることを回避できます。

### プロパティ順序の例

```json
// "kind"プロパティは"album"と"photo"を区別する。
// "Kind"は常に親オブジェクト内の最初のプロパティ。
// "items"プロパティは"data"オブジェクト内の最後のプロパティ。
{
  "data": {
    "kind": "album",
    "title": "My Photo Album",
    "description": "An album in the user's account",
    "items": [
      {
        "kind": "photo",
        "title": "My First Photo"
      }
    ]
  }
}
```

---

## 例

### YouTube JSON API

YouTube JSON APIのレスポンスオブジェクトの例です。YouTubeのJSON APIについて詳しくは [https://code.google.com/apis/youtube/2.0/developers_guide_jsonc.html](https://code.google.com/apis/youtube/2.0/developers_guide_jsonc.html) を参照してください。

```json
{
  "apiVersion": "2.0",
  "data": {
    "updated": "2010-02-04T19:29:54.001Z",
    "totalItems": 6741,
    "startIndex": 1,
    "itemsPerPage": 1,
    "items": [
      {
        "id": "BGODurRfVv4",
        "uploaded": "2009-11-17T20:10:06.000Z",
        "updated": "2010-02-04T06:25:57.000Z",
        "uploader": "docchat",
        "category": "Animals",
        "title": "From service dog to SURFice dog",
        "description": "Surf dog Ricochets inspirational video ...",
        "tags": [
          "Surf dog",
          "dog surfing",
          "dog",
          "golden retriever"
        ],
        "thumbnail": {
          "default": "https://i.ytimg.com/vi/BGODurRfVv4/default.jpg",
          "hqDefault": "https://i.ytimg.com/vi/BGODurRfVv4/hqdefault.jpg"
        },
        "player": {
          "default": "https://www.youtube.com/watch?v=BGODurRfVv4&feature=youtube_gdata",
          "mobile": "https://m.youtube.com/details?v=BGODurRfVv4"
        },
        "content": {
          "1": "rtsp://v5.cache6.c.youtube.com/CiILENy73wIaGQn-Vl-0uoNjBBMYDSANFEgGUgZ2aWRlb3MM/0/0/0/video.3gp",
          "5": "https://www.youtube.com/v/BGODurRfVv4?f=videos&app=youtube_gdata",
          "6": "rtsp://v7.cache7.c.youtube.com/CiILENy73wIaGQn-Vl-0uoNjBBMYESARFEgGUgZ2aWRlb3MM/0/0/0/video.3gp"
        },
        "duration": 315,
        "rating": 4.96,
        "ratingCount": 2043,
        "viewCount": 1781691,
        "favoriteCount": 3363,
        "commentCount": 1007,
        "commentsAllowed": true
      }
    ]
  }
}
```

### ページングの例

この例は、SATELLITE検索のアイテムがどのようにJSONオブジェクトとして表現されるかを示しており、特にページング変数に注目しています。

このサンプルは説明目的のみです。以下のAPIは実際には存在しません。

以下はサンプルのJSON表現です：

```json
{
  "apiVersion": "2.1",
  "id": "1",
  "data": {
    "query": "chicago style pizza",
    "time": "0.1",
    "currentItemCount": 10,
    "itemsPerPage": 10,
    "startIndex": 11,
    "totalItems": 2700000,
    "nextLink": "https://www.google.com/search?hl=en&q=chicago+style+pizza&start=20&sa=N",
    "previousLink": "https://www.google.com/search?hl=en&q=chicago+style+pizza&start=0&sa=N",
    "pagingLinkTemplate": "https://www.google.com/search/hl=en&q=chicago+style+pizza&start={index}&sa=N",
    "items": [
      {
        "title": "Pizz'a Chicago Home Page"
      }
    ]
  }
}
```

上記のスクリーンショットの各色付きボックスがどのように表現されるかは以下の通りです：

- 結果 **11** - 20 / 約 2,700,000件 = `startIndex`
- 結果 11 - **20** / 約 2,700,000件 = `startIndex + currentItemCount - 1`
- 結果 11 - 20 / 約 **2,700,000**件 = `totalItems`
- **検索結果** = `items`（適切にフォーマットされる）
- **前へ/次へ** = `previousLink` / `nextLink`
- **「Gooooooooooogle」内の番号付きリンク** = `pageLinkTemplate` から導出。開発者は `{index}` の値を計算し、それらの値を `pageLinkTemplate` に代入する責任があります。`pageLinkTemplate` の `{index}` 変数は以下のように計算されます：
  - インデックス #1 = 0 * itemsPerPage = 0
  - インデックス #2 = 2 * itemsPerPage = 10
  - インデックス #3 = 3 * itemsPerPage = 20
  - インデックス #N = N * itemsPerPage

---

## 付録

### 付録A：JavaScriptの予約語

**プロパティ名で避けるべきJavaScript予約語のリスト。**

以下の語はJavaScript言語によって予約されており、ドット記法で参照できません。このリストは現時点でのキーワードに関する最善の知識を表しています。リストは特定の実行環境に基づいて変更または変動する可能性があります。

[ECMAScript言語仕様 第5版](http://www.ecma-international.org/publications/standards/Ecma-262.htm) より：

```
abstract
boolean break byte
case catch char class const continue
debugger default delete do double
else enum export extends
false final finally float for function
goto
if implements import in instanceof int interface
let long
native new null
package private protected public
return
short static super switch synchronized
this throw throws transient true try typeof
var volatile void
while with
yield
```

---

このページのコンテンツは、特に[記載](https://code.google.com/policies.html)がない限り、[Creative Commons Attribution 3.0 License](https://creativecommons.org/licenses/by/3.0/) の下でライセンスされており、コードサンプルは [Apache 2.0 License](https://www.apache.org/licenses/LICENSE-2.0) の下でライセンスされています。

リビジョン 0.9
