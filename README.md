# Markdown Package Specification

Markdown Package（`.mdpkg`）は、Markdown 文書と、その文書から参照される画像、図、図のソースデータ、その他の付随リソースを、1 つの ZIP アーカイブにまとめるためのファイルフォーマットです。

専用アプリケーションによる統合された閲覧・編集を可能にしつつ、専用アプリケーションが利用できない場合でも、`.mdpkg` を ZIP として展開することで、一般的な Markdown Viewer や画像 Viewer、テキストエディタを用いて内容を閲覧・復元できることを重視しています。

## Design Goals

Markdown Package は、次の設計原則を重視します。

* Markdown を文書の中心形式として使用する
* ZIP を単なるコンテナとして使用する
* パッケージ内部では、可能な限り既存の標準的なファイル形式を使用する
* 専用 Viewer がなくても、ZIP を展開すれば内容を閲覧できるようにする
* PlantUML や Mermaid などの図について、編集可能なソースとレンダリング済み画像を共存させられるようにする
* 独自バイナリ形式への依存を避ける
* 特定のアプリケーションへのロックインを避ける
* 長期保存および将来のデータ救出を容易にする

例えば、次のような `.mdpkg` ファイルを想定しています。

```text
example.mdpkg
└─ ZIP
   ├─ README.md
   ├─ manifest.json
   ├─ diagrams/
   │  ├─ architecture.puml
   │  └─ architecture.svg
   ├─ images/
   │  └─ screenshot.png
   └─ attachments/
      └─ sample.json
```

Markdown からは、通常の相対パスでレンダリング済みリソースを参照します。

```markdown
# System Architecture

![Architecture](diagrams/architecture.svg)
```

このため、`.mdpkg` を展開した後の `README.md` は、一般的な Markdown Viewer でもそのまま閲覧できます。

## Specification

Markdown Package の詳細な仕様は、以下を参照してください。

* [Markdown Package File Format Specification](./specification.md)

仕様では、主に以下を定義します。

* ZIP コンテナの構造
* `README.md` の扱い
* `manifest.json` の形式
* パッケージ内リソースの参照方法
* PlantUML、Mermaid、Graphviz などの図データの扱い
* レンダリング済み画像との関連付け
* パスおよびセキュリティ要件
* Reader / Writer の適合要件

## Example Package Structure

典型的な Markdown Package は、次のような構造になります。

```text
/
├─ README.md
├─ manifest.json
├─ diagrams/
│  ├─ architecture.puml
│  ├─ architecture.svg
│  ├─ sequence.mmd
│  └─ sequence.svg
├─ images/
│  └─ screenshot.png
└─ attachments/
   └─ sample.json
```

`manifest.json` の例:

```json
{
  "format": "mdpkg",
  "version": "1.0",
  "entrypoint": "README.md",
  "title": "Example Document",
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

## Graceful Degradation

Markdown Package における重要な設計目標の 1 つは、専用アプリケーションへの依存を最小限にすることです。

専用 Viewer が利用できない場合でも、次の方法で内容を閲覧できます。

```text
document.mdpkg
    ↓
ZIP として展開
    ↓
README.md
images/*.png
diagrams/*.svg
diagrams/*.puml
diagrams/*.mmd
attachments/*
    ↓
一般的なツールで閲覧・編集
```

`manifest.json` は、図ソースとレンダリング済み画像の関連付けなど、Markdown Package 固有の追加情報を提供します。

ただし、基本的な文書表示は `manifest.json` に依存しないことを原則とします。

## Implementations

Markdown Package は、特定の Viewer、Editor、CLI、ライブラリに依存しないオープンなファイルフォーマットとして設計されています。

この仕様に基づくソフトウェアは、誰でも自由に実装できます。

例えば、以下のような実装を想定しています。

* Markdown Package Viewer
* Markdown Package Editor
* `.mdpkg` 作成 CLI
* `.mdpkg` 展開 CLI
* バリデータ
* Java / JavaScript / Rust / Go などの Reader / Writer ライブラリ
* Markdown から `.mdpkg` を生成するビルドツール
* `.mdpkg` から HTML / PDF へ変換するツール

この仕様を実装するために、許可の取得やライセンス料の支払いは必要ありません。

## Repository Structure

このリポジトリでは、Markdown Package ファイルフォーマットそのものの仕様を管理します。

```text
.
├─ README.md
├─ specification.md
└─ LICENSE
```

## File Extension

Markdown Package は、次のファイル拡張子を使用します。

```text
.mdpkg
```

例:

```text
system-design.mdpkg
manual.mdpkg
architecture.mdpkg
```

実体は ZIP アーカイブです。

## Compatibility

Markdown Package は、パッケージ内部のデータについて可能な限り既存形式を使用します。

代表的な形式として、以下を想定しています。

* Markdown
* JSON
* SVG
* PNG
* JPEG
* WebP
* PlantUML
* Mermaid
* Graphviz DOT
* draw.io
* Excalidraw
* JSON
* YAML
* CSV

将来的に新しいリソース形式を追加する場合でも、既存 Reader が未知のリソースを無視しながら基本的な Markdown 文書を表示できるよう、後方互換性を重視します。

## License

この Markdown Package Specification は、[Creative Commons Attribution 4.0 International](https://creativecommons.org/licenses/by/4.0/)（CC BY 4.0）の下で提供されます。

SPDX License Identifier:

```text
CC-BY-4.0
```

仕様書の複製、再配布、改変、翻訳、商用利用などは、CC BY 4.0 の条件に従って行うことができます。

なお、CC BY 4.0 はこの仕様書そのものに適用されます。

Markdown Package（`.mdpkg`）ファイルフォーマットを実装、生成、解析、表示、編集するソフトウェアについて、この仕様書と同じライセンスを使用する必要はありません。

The Markdown Package (`.mdpkg`) file format may be implemented, generated, parsed, displayed, or edited by anyone without requiring permission or payment of license fees.

Software implementing this specification is not required to use the CC BY 4.0 license.
