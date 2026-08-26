# Markdown Package (.mdpkg) ファイルフォーマット仕様書

## 1. 概要

Markdown Package（以下、MDPKG）は、1 つ以上の Markdown 文書と、それらの文書から参照される画像、図、図のソースデータ、その他の付随リソースを 1 つの ZIP アーカイブにまとめるためのファイルフォーマットです。

ファイル拡張子は `.mdpkg` とします。

MDPKG は、専用アプリケーションによる高機能な表示・編集を可能にしつつ、専用アプリケーションが利用できない場合でも、ZIP アーカイブを展開することで一般的な Markdown Viewer や画像 Viewer、テキストエディタなどを用いて内容を閲覧できることを重視します。

MDPKG の基本方針は次のとおりです。

- ZIP をコンテナ形式として使用する
- 本文は一般的な Markdown として保存する
- 複数の Markdown 文書を格納できる
- Markdown 文書および関連リソースをサブディレクトリに配置できる
- Markdown から参照する画像は一般的な画像形式を使用する
- PlantUML や Mermaid などの図については、必要に応じて編集可能なソースとレンダリング済み画像の両方を格納する
- MDPKG 固有のメタデータがなくても、基本的な文書内容を閲覧できるようにする
- パッケージ内部では可能な限り既存の標準的なファイル形式を使用する
- 独自 Viewer へのロックインを避ける
- 長期保存および将来のデータ救出を容易にする



## 2. ファイル拡張子

MDPKG ファイルは次の拡張子を使用します。

```text
.mdpkg
```

例:

```text
system-design.mdpkg
manual.mdpkg
architecture-document.mdpkg
```



## 3. コンテナ形式

MDPKG は ZIP 形式のアーカイブです。

MDPKG 対応アプリケーションは `.mdpkg` ファイルを ZIP アーカイブとして読み取らなければなりません。

ZIP アーカイブ内のファイル名は UTF-8 で格納するものとします。

ユーザーは必要に応じて `.mdpkg` ファイルを一般的な ZIP 展開ツールで展開できるものとします。

例えば次のファイルがある場合、

```text
system-design.mdpkg
```

ZIP 展開ツールによって次のようなディレクトリとして展開できます。

```text
system-design/
├─ index.md
├─ manifest.json
├─ getting-started/
│  ├─ introduction.md
│  ├─ diagrams/
│  │  ├─ flow.puml
│  │  └─ flow.svg
│  └─ images/
│     └─ screenshot.png
└─ guides/
   ├─ advanced.md
   └─ images/
      └─ configuration.png
```



## 4. 設計原則

### 4.1 Graceful Degradation

MDPKG は、専用アプリケーションが存在しない環境でも可能な限り内容を閲覧できなければなりません。

1. `.mdpkg` ファイルを ZIP として展開する
2. `manifest.json` の `entrypoint` に指定された Markdown 文書を開く
3. 一般的な Markdown Viewer で本文を閲覧する
4. 必要に応じて Markdown 文書間の相対リンクをたどる
5. Markdown から参照される画像を一般的な画像 Viewer で閲覧する
6. 必要に応じて PlantUML、Mermaid、Graphviz などのソースを対応ツールで開く

### 4.2 Markdown の自己完結性

各 Markdown 文書は、MDPKG 固有のメタデータを解釈しなくても可能な限り文書として成立するべきです。

したがって、 `manifest.json` はエントリポイントの特定などパッケージレベルの情報には使用されますが、個々の Markdown 文書の本文や基本的な表示内容を解釈するために必須であってはなりません。

### 4.3 標準形式の利用

MDPKG 内部では、可能な限り既存の標準形式または広く利用されている形式を使用します。

例:

- Markdown
- JSON
- SVG
- PNG
- JPEG
- WebP
- PlantUML
- Mermaid
- Graphviz DOT
- draw.io XML
- Excalidraw JSON

MDPKG 固有のバイナリデータ形式は、原則として定義しません。



## 5. 基本ディレクトリ構造

MDPKG v2 では、Markdown 文書および関連リソースを任意のサブディレクトリに配置できます。

```text
/
├─ index.md
├─ manifest.json
├─ getting-started/
│  ├─ introduction.md
│  ├─ diagrams/
│  └─ images/
└─ guides/
   ├─ advanced.md
   └─ attachments/
```

`images`、`diagrams`、`attachments` などのディレクトリは、ルートディレクトリだけでなく任意のサブディレクトリに配置できます。

これらのディレクトリ名は予約語ではなく、リソースを整理するための推奨名称です。

最小の MDPKG は次の構造です。

```text
/
├─ index.md
└─ manifest.json
```

`index.md` というファイル名は一例であり、エントリポイントの Markdown 文書名は任意です。


## 6. Markdown 文書

### 6.1 概要

MDPKG v2 では、1 つ以上の Markdown 文書を格納できます。

パッケージを開いた際に最初に表示する Markdown 文書は、`manifest.json` の `entrypoint` で指定します。

Markdown 文書はルートディレクトリだけでなく、任意のサブディレクトリに配置できます。

v1 では `README.md` が必須でしたが、 v2 では必須ではありません。

### 6.2 文字コード

すべての Markdown 文書は UTF-8 で保存しなければなりません。

UTF-8 BOM は使用しないことを推奨します。

### 6.3 Markdown 方言

MDPKG v2 の推奨 Markdown 方言は GitHub Flavored Markdown（GFM）です。

### 6.4 文書タイトル

各 Markdown 文書のタイトルは、その文書内に出現する最初の H1 見出しとします。

Markdown 文書に H1 見出しが存在しない場合、その文書のタイトルは未定義とします。

MDPKG 対応アプリケーションは、表示上の代替としてファイル名を使用しても構いません。

### 6.5 リソース参照

パッケージ内リソースは相対パスで参照します。

例:

```markdown
![スクリーンショット](images/screenshot.png)
```

```markdown
![システム構成図](diagrams/architecture.svg)
```

絶対ファイルパスは使用してはなりません。

禁止例:

```markdown
![画像](C:/Users/example/image.png)
```

```markdown
![画像](file:///home/user/image.png)
```

相対パスは、その参照を記述している Markdown 文書が存在するディレクトリを基準として解決します。

Markdown 文書間も通常の相対リンクで参照できます。

### 6.6 パッケージ外参照

MDPKG v2 では、サブディレクトリ間の参照のために `..` を含む相対パスを使用できます。

ただし、参照パスを正規化した結果がパッケージルートより上位を指してはなりません。

## 7. manifest.json

### 7.1 概要

`manifest.json` は MDPKG 固有のメタデータを格納するファイルです。

`manifest.json` はルートディレクトリに配置します。

```text
/manifest.json
```

基本的な Markdown 表示は `manifest.json` に依存してはなりません。

`manifest.json` は主に次の目的で使用します。

- MDPKG フォーマットの識別
- フォーマットバージョンの識別
- 文書メタデータ
- エントリポイントの指定
- 図ソースとレンダリング済み画像の関連付け
- リソース種別の明示
- 将来の拡張情報

### 7.2 最小構成

```json
{
  "format": "mdpkg",
  "version": "2.0",
  "entrypoint": "index.md"
}
```

### 7.3 推奨構成

```json
{
  "format": "mdpkg",
  "version": "2.0",
  "entrypoint": "index.md",
  "description": "Example System のアーキテクチャ設計書",
  "resources": [
    {
      "source": "diagrams/architecture.puml",
      "rendered": "diagrams/architecture.svg",
      "type": "plantuml"
    }
  ]
}
```



### 7.4 文字コード

`manifest.json` は UTF-8 で保存しなければなりません。

UTF-8 BOM は使用しないことを推奨します。

## 8. manifest.json のフィールド

### 8.1 format

必須フィールドです。

固定値として次を指定します。

```json
"format": "mdpkg"
```

MDPKG 対応アプリケーションは、この値を用いて MDPKG であることを確認できます。

### 8.2 version

必須フィールドです。

MDPKG のフォーマットバージョンを文字列で指定します。

```json
"version": "2.0"
```

### 8.3 entrypoint

必須フィールドです。

パッケージを開いた際に最初に表示する Markdown 文書への、パッケージルートからの相対パスを指定します。

```json
"entrypoint": "index.md"
```

`entrypoint` はパッケージ内部に存在する Markdown 文書を指さなければなりません。

### 8.4 description

任意フィールドです。

文書の簡単な説明を指定します。

```json
"description": "Example System のアーキテクチャ設計書"
```

### 8.5 resources

任意フィールドです。

図ソースとレンダリング済みリソースなどの関係を定義します。

`source` および `rendered` はパッケージルートからの相対パスとして指定します。

例:

```json
{
  "resources": [
    {
      "source": "diagrams/architecture.puml",
      "rendered": "diagrams/architecture.svg",
      "type": "plantuml"
    },
    {
      "source": "diagrams/sequence.mmd",
      "rendered": "diagrams/sequence.svg",
      "type": "mermaid"
    }
  ]
}
```



## 9. リソース

`images`、`diagrams`、`attachments` などのディレクトリは任意のサブディレクトリに配置できます。

これらの名称は予約語ではありません。

## 10. 図データ

### 10.1 基本方針

編集可能な図については、可能な限り次の両方を格納することを推奨します。

1. 図のソースデータ
2. レンダリング済み画像

例:

```text
diagrams/
├─ architecture.puml
└─ architecture.svg
```

Markdown からはレンダリング済み画像を参照します。

```markdown
![アーキテクチャ](diagrams/architecture.svg)
```

専用 MDPKG アプリケーションは `manifest.json` を参照することで、SVG と PlantUML ソースの関係を認識できます。

図ソース（PlantUML、Mermaid、Graphviz などのテキスト形式）は UTF-8 で保存しなければなりません。

### 10.2 PlantUML

推奨拡張子:

```text
.puml
```

例:

```text
diagrams/architecture.puml
diagrams/architecture.svg
```

manifest:

```json
{
  "source": "diagrams/architecture.puml",
  "rendered": "diagrams/architecture.svg",
  "type": "plantuml"
}
```

### 10.3 Mermaid

推奨拡張子:

```text
.mmd
```

例:

```text
diagrams/sequence.mmd
diagrams/sequence.svg
```

manifest:

```json
{
  "source": "diagrams/sequence.mmd",
  "rendered": "diagrams/sequence.svg",
  "type": "mermaid"
}
```

### 10.4 Graphviz

推奨拡張子:

```text
.dot
```

manifest:

```json
{
  "source": "diagrams/dependencies.dot",
  "rendered": "diagrams/dependencies.svg",
  "type": "graphviz"
}
```

### 10.5 その他の図形式

将来的に次のような形式を使用できます。

- draw.io
- Excalidraw
- D2
- Vega
- Vega-Lite
- その他のテキストベースまたは公開仕様を持つ図形式

未知の図形式を含む MDPKG であっても、レンダリング済み画像が存在する場合には、通常の Markdown Viewer で文書を閲覧可能であることが望まれます。



## 11. レンダリング済み図

レンダリング済み図には SVG を第一候補として推奨します。

理由は次のとおりです。

- ベクター形式である
- 拡大しても劣化しない
- テキスト主体の図との相性がよい
- ブラウザで表示できる
- PlantUML、Mermaid、Graphviz などから生成しやすい

必要に応じて PNG なども使用できます。



## 12. 外部 URL

Markdown 内から HTTP または HTTPS URL を参照すること自体は許可できます。

例:

```markdown
[公式サイト](https://example.com/)
```

ただし、外部画像やその他の外部リソースを自動取得するかどうかは Viewer のポリシーによります。

例:

```markdown
![外部画像](https://example.com/image.png)
```

MDPKG 対応 Viewer は、セキュリティおよびプライバシー保護のため、外部リソースへのアクセスを制限しても構いません。

オフライン可搬性を保つため、文書表示に必須のリソースはパッケージ内部に格納することを推奨します。



## 13. セキュリティ

### 13.1 ZIP Slip 対策

MDPKG 対応アプリケーションは ZIP エントリを展開または読み込む際に、パッケージルート外への書き込みを防止しなければなりません。

### 13.2 パス・トラバーサル

Markdown、manifest、およびその他の内部参照について、正規化後のパスがパッケージルート外を指すことを禁止します。

MDPKG v2 では `..` を含む相対パスを使用できますが、正規化後の参照先は必ずパッケージ内部に留まらなければなりません。

### 13.3 HTML

Markdown 内の raw HTML の扱いは Viewer 実装に依存します。

安全性を優先する Viewer は raw HTML を無効化またはサニタイズすることを推奨します。

特に次の要素には注意が必要です。

```html
<script>
<iframe>
<object>
<embed>
```

### 13.4 JavaScript

MDPKG 文書に含まれる JavaScript を自動実行してはなりません。

### 13.5 SVG

MDPKG 対応 Viewer は、SVG を表示する際に適切なサニタイズまたは隔離を行わなければなりません。

### 13.6 file URI

Markdown 内からローカルファイルを参照する `file:` URI は原則として禁止します。

例:

```text
file:///etc/passwd
```

```text
file:///C:/Users/example/private.txt
```

### 13.7 図レンダラー

PlantUML などの図レンダラーが外部ファイルやネットワークへアクセスできる場合、MDPKG Viewer はアクセスを制限するべきです。



## 14. パス表現

### 14.1 区切り文字

MDPKG 内部のパスでは `/` を使用します。

推奨:

```text
images/example.png
```

非推奨:

```text
images\example.png
```

### 14.2 相対パス

内部リソース参照および Markdown 文書間リンクは相対パスとします。

Markdown 内の相対パスは、その Markdown 文書が存在するディレクトリを基準に解決します。

`manifest.json` 内の `entrypoint`、`resources[].source`、`resources[].rendered` などのパスは、パッケージルートを基準に解決します。

### 14.3 親ディレクトリ

Markdown 内では `..` を含む相対パスを使用できます。

ただし、正規化後の参照先がパッケージルートを越えてはなりません。

### 14.4 大文字・小文字

ファイル名の大文字・小文字を区別する環境が存在するため、参照時にはファイル名の大文字・小文字を正確に一致させなければなりません。

## 15. MIME Type

正式な MIME Type が登録されるまでの暫定 MIME Type として、次の形式を使用できます。

```text
application/x-mdpkg
```

将来的に正式な登録を行う場合は、別途 MIME Type を定義します。



## 16. MDPKG 対応 Viewer

MDPKG Viewer は最低限、次の機能を提供することを推奨します。

- `.mdpkg` の ZIP コンテナ読み込み
- `manifest.json` の読み込み
- `entrypoint` に指定された Markdown 文書のレンダリング
- 複数 Markdown 文書の読み込み
- サブディレクトリの取り扱い
- Markdown 文書間の相対リンクの解決
- パッケージ内画像の表示
- 相対リソース参照の解決
- パッケージ外パスへのアクセス防止

追加機能として、次を実装できます。

- PlantUML の再レンダリング
- Mermaid の再レンダリング
- Graphviz の再レンダリング
- 図ソースの表示
- Markdown 編集
- 図ソース編集
- 再パッケージ
- 検索
- 目次表示
- 添付ファイル閲覧
- 印刷
- HTML 出力
- PDF 出力



## 17. MDPKG Editor

編集機能を持つアプリケーションは、複数の Markdown 文書およびサブディレクトリを扱えることを推奨します。

また、図ソースを変更した場合に対応するレンダリング済み画像を再生成することを推奨します。

例えば、

```text
diagrams/architecture.puml
```

を編集した場合、

```text
diagrams/architecture.svg
```

を更新します。

これにより、専用 Viewer を使用しない環境でも最新の図を閲覧できます。



## 18. MDPKG 作成時の推奨処理

MDPKG Writer は、複数の Markdown 文書、画像、添付ファイル、図ソース、レンダリング済み画像、`manifest.json` を ZIP パッケージ化して `.mdpkg` を生成します。

## 19. 展開後の可読性

MDPKG の重要な互換性要件として、ZIP 展開後のディレクトリは、そのまま通常の Markdown プロジェクトとして扱えることを推奨します。

例えば次のパッケージを展開した場合、

```text
design.mdpkg
```

次の状態になります。

```text
design/
├─ index.md
├─ manifest.json
├─ diagrams/
│  ├─ system.puml
│  └─ system.svg
└─ images/
   └─ ui.png
```

ユーザーは `index.md` を一般的な Markdown Viewer で開くことで、文書を閲覧できます。


ユーザーは `manifest.json` の `entrypoint` に指定された Markdown 文書を一般的な Markdown Viewer で開き、通常の Markdown リンクをたどることで他の文書を閲覧できます。

## 20. 例

### 20.1 パッケージ構造

```text
example.mdpkg
└─ ZIP
   ├─ manifest.json
   ├─ index.md
   ├─ architecture/
   │  ├─ overview.md
   │  ├─ diagrams/
   │  │  ├─ architecture.puml
   │  │  └─ architecture.svg
   │  └─ images/
   │     └─ screenshot.png
   └─ operation/
      ├─ guide.md
      └─ attachments/
         └─ sample.json
```



## 21. バージョニング

MDPKG のフォーマットバージョンは `manifest.json` の `version` で管理します。

```json
{
  "version": "2.0"
}
```

バージョン番号は次の形式を推奨します。

```text
MAJOR.MINOR
```

例:

```text
1.0
1.1
2.0
```

### MAJOR

後方互換性を破壊する変更で更新します。

### MINOR

既存仕様との後方互換性を維持した拡張で更新します。

MDPKG Reader は、自身が認識できない追加フィールドを可能な限り無視することを推奨します。



## 22. 将来拡張

MDPKG v2 では、複数 Markdown 文書およびサブディレクトリによる階層化をサポートします。

文書の論理的なナビゲーション構造や表示順序については定義せず、パッケージ内のファイルおよびディレクトリ構造として管理します。

将来的には次の拡張を検討できます。

- ナビゲーション
- 目次メタデータ
- テーマ
- 印刷設定
- 文書言語
- 著者情報
- 作成日時
- 更新日時
- カスタム CSS
- 数式
- コード実行結果
- 埋め込みデータセット
- デジタル署名
- チェックサム
- 暗号化
- 差分更新

これらの拡張を導入する場合でも、可能な限り ZIP 展開後の可読性を維持することを推奨します。



## 23. 非目標

MDPKG v2 では、次の機能を必須要件としません。

- 文書の論理的なナビゲーション構造
- 文書の表示順序を定義するメタデータ
- Word のような完全な WYSIWYG レイアウト
- ページ単位の固定レイアウト
- 独自フォントの必須埋め込み
- JavaScript アプリケーションの埋め込み
- マクロ
- 任意コード実行
- DRM
- 暗号化
- リアルタイム共同編集

MDPKG は固定レイアウト文書形式ではなく、Markdown を中心とした可搬性の高い文書パッケージ形式を目的とします。



## 24. 適合性

### 24.1 MDPKG Package

MDPKG v2 に適合するパッケージは、最低限次の条件を満たさなければなりません。

- ZIP 形式である
- `.mdpkg` 拡張子を使用する
- ルートに `manifest.json` が存在する
- `manifest.json` の `format` が `mdpkg` である
- `manifest.json` に `version` が存在する
- `version` の MAJOR version が `2` である
- `manifest.json` に `entrypoint` が存在する
- `entrypoint` がパッケージ内部に存在する Markdown 文書を指している
- すべての Markdown 文書が UTF-8 である
- パッケージ内部リソースへの参照を正規化した結果がパッケージルートを越えない

### 24.2 MDPKG Reader

MDPKG v2 Reader は最低限次を満たさなければなりません。

- ZIP コンテナを読み取れる
- `manifest.json` を解析できる
- `entrypoint` に指定された Markdown 文書を読み取れる
- 複数 Markdown 文書を読み取れる
- サブディレクトリ内の Markdown 文書を扱える
- Markdown を表示できる
- Markdown 文書間の相対リンクを解決できる
- Markdown 文書を基準とした相対リソース参照を解決できる
- `..` を含む参照を正規化できる
- パッケージ外へのパス・トラバーサルを防止する

### 24.3 MDPKG Writer

MDPKG v2 Writer は最低限次を満たさなければなりません。

- 適合する ZIP コンテナを生成できる
- Markdown 文書を UTF-8 で生成する
- 複数 Markdown 文書を格納できる
- サブディレクトリを保持できる
- 有効な `manifest.json` を生成する
- `entrypoint` に有効な Markdown 文書を指定する
- 内部参照を相対パスとして生成する
- 正規化後にパッケージ外を参照するパスを生成しない

## 25. 推奨事項

MDPKG 文書の作成者には次を推奨します。

- `entrypoint` に指定された Markdown 文書から、主要な Markdown 文書へ通常の Markdown リンクでたどれるようにする
- 各 Markdown 文書に、その文書のタイトルとなる H1 見出しを記述する
- 関連する Markdown、画像、図、添付ファイルを必要に応じて同じサブディレクトリ周辺にまとめる
- 文書表示に必要なリソースをすべてパッケージに含める
- 図ソースだけでなくレンダリング済み画像も含める
- 図のレンダリング形式には SVG を優先する
- 独自 Markdown 拡張への依存を避ける
- パッケージ内部では標準的な形式を使用する
- 絶対パスを使用しない
- OS 固有のパス表現を使用しない
- 外部ネットワークへの依存を避ける
- 専用 MDPKG Viewer がなくても内容を復元できるようにする



## 26. 設計思想

MDPKG において ZIP は文書形式そのものではなく、複数の既存形式を 1 つのファイルとして配布するためのコンテナです。

MDPKG の長期的な可搬性は、専用アプリケーションの永続性ではなく、パッケージ内部のデータが一般的なツールで理解可能であることによって確保します。

MDPKG v2 では、複数 Markdown 文書とディレクトリ階層を導入しますが、文書の論理構造を独自メタデータへ閉じ込めません。

Markdown 文書同士は通常の相対リンクで接続し、画像、図、添付ファイルも通常の相対パスで参照します。

そのため MDPKG は、次の状態を理想とします。

```text
専用 Viewer がある
    ↓
統合された便利な文書として閲覧・編集できる

専用 Viewer がない
    ↓
ZIP を展開する

    ↓

index.md
SVG / PNG
PlantUML
Mermaid
JSON
その他の標準形式

    ↓

一般的なツールで内容を閲覧・復元できる
```

MDPKG は、専用アプリケーションが存在しなくなった場合でも、文書そのものが失われないことを重要な設計目標とします。

