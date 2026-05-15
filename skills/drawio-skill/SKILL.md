---
name: drawio-skill
version: 1.5.2
description: ユーザーが図表、フローチャート、アーキテクチャ図、または可視化を求めたときに使用する。また、3 つ以上のコンポーネントを持つシステム、複雑なデータフロー、視覚化したほうがわかりやすい関係について説明するときには、能動的に使用する。draw.io デスクトップのネイティブ CLI を使い、ローカルで `.drawio` XML ファイルを生成して PNG/SVG/PDF にエクスポートする。
license: MIT
homepage: https://github.com/Agents365-ai/drawio-skill
compatibility: draw.io デスクトップアプリの CLI が PATH に含まれている必要がある（macOS/Linux/Windows）。セルフチェックのステップにはビジョン対応モデル（例：Claude Sonnet/Opus）が必要。利用できない場合は、セルフチェックを丁寧にスキップする。
platforms: [macos, linux, windows]
metadata: {"openclaw":{"requires":{"anyBins":["draw.io","drawio"]},"emoji":"📐","os":["darwin","linux","win32"],"install":[{"id":"brew-drawio","kind":"brew","formula":"drawio","bins":["draw.io"],"label":"Install draw.io via Homebrew","os":["darwin"]}]},"hermes":{"tags":["drawio","diagram","flowchart","architecture","visualization","uml"],"category":"design","requires_tools":["draw.io"],"related_skills":["mermaid","excalidraw","plantuml"]},"author":"Agents365-ai","version":"1.5.2"}
---

# Draw.io 図表

## 概要

draw.io デスクトップアプリの CLI を使い、ローカルで `.drawio` XML ファイルを生成して PNG/SVG/PDF/JPG にエクスポートします。

**対応フォーマット：** PNG、SVG、PDF、JPG。ブラウザの自動操作は不要です。

PNG、SVG、PDF のエクスポートは `--embed-diagram`（`-e`）に対応しています。エクスポートしたファイルには図表の XML 全体が含まれるため、draw.io で開けば編集可能な図表を復元できます。埋め込みを示すには二重拡張子（`name.drawio.png`）を使います。

## 同梱リソース

ワークフローでこれらのいずれかが必要になったときに、必要に応じて読み込みます。最初からコンテキストに入れておく必要はありません。

| ファイル | 読み込むタイミング |
|---|---|
| `references/diagram-types.md` | ユーザーが特定の図表タイプ（ERD、UML クラス、シーケンス、アーキテクチャ、ML/DL、フローチャート）を指定したとき |
| `references/style-presets.md` | ユーザーがスタイルプリセットの学習・保存・一覧表示・デフォルト設定・削除を依頼したとき、または有効なプリセットを解決して適用ルールが必要になったとき |
| `references/style-extraction.md` | 学習フローの途中で抽出手順が必要になったとき（`style-presets.md` から呼ばれます） |
| `references/troubleshooting.md` | エクスポートが失敗したとき、ビジョンが PNG を拒否したとき、レンダリングがおかしいとき |
| `scripts/repair_png.py` | `-e` 付きの PNG エクスポートのたびに使用 — draw.io の切り詰められた IEND チャンクを修正します（issue #8） |
| `scripts/encode_drawio_url.py` | CLI が使えず、ブラウザフォールバック用の diagrams.net URL が必要なとき |

## 前提条件

draw.io デスクトップアプリがインストールされており、CLI にアクセスできる必要があります：

**macOS サンドボックス／サンドボックス分離環境（例：codex.app）の注意：** サンドボックス化された一部の macOS 環境では、draw.io デスクトップ CLI を呼び出すと（`draw.io --version` でも）プロセスがクラッシュしたり、出力が空になったりすることがあります。その場合は、CLI を**このサンドボックス環境では利用不可**として扱い、サンドボックス内でリトライを繰り返さないでください。CLI でのエクスポート作業は、**サンドボックスの外のホスト環境**で行うのが望ましく、それが難しい場合はブラウザフォールバックや XML のみの出力で代替します。

```bash
# macOS（Homebrew — 推奨；CLI バイナリは `drawio`、`draw.io` ではない）
brew install --cask drawio
drawio --version

# macOS（PATH に無い場合のフルパス）
/Applications/draw.io.app/Contents/MacOS/draw.io --version

# Windows
"C:\Program Files\draw.io\draw.io.exe" --version

# Linux
draw.io --version
```

未インストールの場合は draw.io デスクトップをインストールしてください：
- macOS：`brew install --cask drawio`、または https://github.com/jgraph/drawio-desktop/releases からダウンロード
- Windows：https://github.com/jgraph/drawio-desktop/releases からインストーラーをダウンロード
- Linux：https://github.com/jgraph/drawio-desktop/releases から `.deb`/`.rpm` をダウンロード — **snap は使わない**（AppArmor サンドボックスがサーバー上で secrets/keyring を拒否し、クラッシュの原因になります）

## ワークフロー

ワークフローを始める前に、ユーザーの要求が十分に具体的かを確認します。重要な情報が足りない場合は、1〜3 個に絞って質問します：
- **図表タイプ** — どのプリセットを使うか？（ERD、UML、シーケンス、アーキテクチャ、ML/DL、フローチャート、または特定なし）
- **出力フォーマット** — PNG（デフォルト）、SVG、PDF、JPG のどれか？
- **出力先** — デフォルトはユーザーの作業ディレクトリ。ユーザーが明示的にパスを指定した場合（例：「`./artifacts/` に置いて」）はそれに従う。指定がなければわざわざ尋ねない。
- **規模・粒度** — コンポーネントはいくつあるか？特定の技術やラベルはあるか？

要求にすでにこれらの情報が含まれている場合や、明らかにシンプルな依頼（例：「X のフローチャートを描いて」）の場合は、確認をスキップします。

**ステップ 0 — 有効なプリセットを解決します。** この生成に適用するユーザー定義のスタイルプリセットがあるかどうかを判定します。

- ユーザーのメッセージに、スタイルプリセットを明確に指す表現がないかを探します："use my `<name>` style"、"with my `<name>` style"、"in `<name>` mode"、"in the style of `<name>`"。単なる `with <name>` は**該当しません**（"draw a diagram with redis" はスタイルではなくコンポーネント名です）。一致が見つかれば → 有効なプリセット = `<name>`。
- 見つからなければ、`~/.drawio-skill/styles/` で `"default": true` のファイルを探します。あれば → 有効なプリセット = そのファイル。
- それも無ければ → 有効なプリセット無し。ワークフローの残りでは組み込みの色／形状／エッジ規約を使います。

プリセット JSON はまず `~/.drawio-skill/styles/<name>.json` から読み込み、見つからなければ `<this-skill-dir>/styles/built-in/<name>.json` を試します。指定されたプリセットがどちらにも存在しない場合は、ユーザーに「その名前は見つかりません」と伝え、利用可能なプリセット（ユーザーディレクトリ + 組み込み）を一覧表示し、処理を止めます。**黙ってデフォルトにフォールバックしないでください。**

プリセットを正常に読み込めたら、返信の冒頭で *"Using preset `<name>` (confidence: `<level>`)."* と一言入れます。プリセットが色／形状／エッジ／フォントの決定にどう影響するかは、下の **Applying a preset** サブセクションを参照してください。

1. **依存関係の確認** — `draw.io --version` が成功することを確認し、正しい CLI パスを選ぶためにプラットフォームを把握する。
2. **計画** — 形状、関係、レイアウト方向（LR または TB）を決め、階層／レイヤーごとにグループ化する。
3. **生成** — `.drawio` XML ファイルをディスクに書き出す。デフォルトの出力先はユーザーの作業ディレクトリ。ユーザーが出力パスやディレクトリ（例：`./artifacts/`、`docs/images/`）を指定した場合はそちらを使い、対象ディレクトリは `mkdir -p` で先に作っておく。ステップ 4 と 7 の PNG/SVG/PDF エクスポートでも同じ出力先を使う。
4. **ドラフトをエクスポート** — CLI を実行してプレビュー用の PNG を生成する。**このステップでは `-e` を付けないこと**。`-e` で付加される `zTXt mxGraphModel` チャンクが原因で、ステップ 5 でビジョン API（Claude を含む）が 400 "Could not process image" を返してしまう。クリーンなプレビューは `<name>.png`（単一拡張子）として保存する。XML の埋め込みは最終エクスポート（ステップ 7）でだけ行う。
5. **セルフチェック** — エージェントの組み込みビジョン機能で、エクスポートした PNG を読み取り、明らかな問題を見つけてユーザーに見せる前に自動で直す（Claude Sonnet/Opus のようなビジョン対応モデルが必要）。PNG の読み取りで 400 / "Could not process image" が返ってきた場合は、ほぼ確実に誤って `-e` を付けてエクスポートしている。`-e` なしで再エクスポートして 1 回だけリトライする。それでも失敗したら、セルフチェックをスキップしてステップ 6 に進む。
6. **レビューループ** — ユーザーに画像を見せ、フィードバックを集め、的を絞って XML を編集し、再エクスポートする、という流れを承認されるまで繰り返す。
7. **最終エクスポート** — 承認されたバージョンを、要求されたすべてのフォーマットへ再エクスポートする。ここでは `-e` を使う（PNG/SVG/PDF）ので、成果物は draw.io で編集可能なまま保てる。埋め込み済みであることを示すために `<name>.drawio.png` という名前で保存する。**`-e` 付きの PNG については、続けてすぐに `python3 <this-skill-dir>/scripts/repair_png.py <name>.drawio.png` を実行すること。** draw.io CLI は `-e` PNG 出力時に IEND チャンクを切り詰める（8 バイトの欠落）ため、ビジョン API や厳密な PNG デコーダが受け付けない壊れたファイルを生成してしまう（issue #8）。最後にファイルパスを報告する。

**`draw.io --version` がクラッシュするか何も出力しない場合（codex.app のような制限された macOS サンドボックス分離環境でよくあります）：**
- サンドボックス内で CLI 呼び出しを繰り返さないこと。
- ステップ 4・5・6・7（CLI エクスポート + PNG ベースのレビュー）をスキップし、**ブラウザフォールバック**（`scripts/encode_drawio_url.py`）を使うか、`.drawio` XML のみを提供する。
- ユーザーが PNG/SVG/PDF の出力を必要としている場合は、**サンドボックスの外のホスト環境**でエクスポートコマンドを実行し、出来上がったファイルを共有するよう依頼する。

エスカレーションのルール：
- バイナリが PATH（または既知のアプリパス）に存在するが、実行が異常終了、出力が空、Electron の起動失敗、ディスプレイ／セッションのエラー、あるいはサンドボックス制限と思われる理由で失敗した場合は、フォールバックする前に 1 回だけリトライする。
- バイナリ自体が無い場合は、より積極的に探そうとせず、インストール手順の案内かフォールバックに進む。

### ステップ 5：セルフチェック

ドラフトの PNG をエクスポートしたら、エージェントのビジョン機能（例：Claude の画像入力）で画像を読み取り、ユーザーに見せる前に以下の問題を確認します。エージェントがビジョンに対応していない場合は、セルフチェックをスキップして PNG をそのまま提示します。

**重要：** ここで読み取るドラフト PNG は、必ず `-e` **なし**でエクスポートしてください。draw.io の `-e` フラグは、Anthropic のビジョン API が 400 "Could not process image" で拒否する PNG（IEND チャンクの type+CRC の 8 バイトが欠落）を出力します（issue #8）。プレビューでは `-e` を付けないのが最もシンプルな対処です。ステップ 7 の最終エクスポートでは `-e` を残し、修復スクリプトを実行します。ここで 400 エラーが返ってきたら、`-e` なしで再エクスポートして 1 回だけリトライしてください。それでも別の理由で失敗するようなら、セルフチェックをスキップしてステップ 6 に進みます。

| チェック項目 | 着目点 | 自動修正の動作 |
|-------|-----------------|-----------------|
| 形状の重なり | 2 つ以上の形状が積み重なっている | 形状を 200px 以上ずらして離す |
| ラベルの見切れ | テキストが形状の枠で切れている | ラベルに合わせて形状の幅／高さを広げる |
| 接続の欠落 | 矢印が見た目上どの形状にもつながっていない | エッジの `source`/`target` の id が既存のセルと一致しているかを確認する |
| キャンバス外の形状 | 負の座標、または本体グループから極端に離れた位置にある形状 | 本体クラスターの近くの正の座標に移動する |
| エッジと形状の重なり | エッジ／矢印が無関係な形状を見た目上突き抜けている | ウェイポイント（`<Array as="points">`）を追加して形状を迂回するか、形状同士の間隔を広げる |
| エッジ同士の重なり | 複数のエッジが同じ経路で重なって見える | 形状の周囲で入出力点を分散させる（`exitX`/`entryX` の値を変える） |

- セルフチェックは**最大 2 ラウンド**。2 回直しても問題が残るなら、その状態でユーザーに見せる。
- 修正のたびに再エクスポートし、新しい PNG を改めて読み直す。

### ステップ 6：レビューループ

セルフチェックが終わったら、エクスポートした画像を表示し、ユーザーにフィードバックを求めます。

**的を絞った編集ルール** — フィードバックの種類ごとに、最小限の XML 変更で対応します：

| ユーザーの要望 | XML の編集内容 |
|-------------|----------------|
| X の色を変える | `value` が X に一致する `mxCell` を探し、`style` の `fillColor`/`strokeColor` を更新する |
| 新しいノードを追加 | 次に空いている `id` で `mxCell` 頂点を追加し、関連するノードの近くに配置する |
| ノードを削除 | 対象の `mxCell` 頂点と、その id を `source`/`target` に持つエッジを削除する |
| 形状 X を移動 | 該当する `mxCell` の `mxGeometry` の `x`/`y` を更新する |
| 形状 X をリサイズ | 該当する `mxCell` の `mxGeometry` の `width`/`height` を更新する |
| A から B への矢印を追加 | A と B の id に対応する `source`/`target` を持つ `mxCell` エッジを新規追加する |
| ラベルテキストを変更 | 該当する `mxCell` の `value` 属性を更新する |
| レイアウト方向を変更 | **全体を作り直す** — 新しい方向で XML を再生成する |

**ルール：**
- 単一の要素を変更する場合：既存の XML をそのまま編集する（これまでのイテレーションで詰めたレイアウトを保てる）。
- レイアウト全体を変更する場合（例：LR↔TB の切り替え、「やり直し」）：XML を最初から再生成する。
- 各イテレーションでは同じ `{name}.png`（`-e` なし）を上書きする。`v1`、`v2`、`v3` のようなファイルを作らない。`-e` はステップ 7 の最終エクスポート専用。
- 編集を適用したら、再エクスポートして更新後の画像を表示する。
- ユーザーが「承認」「完了」「LGTM」などと言うまでループを続ける。
- **安全弁：** イテレーションが 5 ラウンドに達したら、draw.io デスクトップで `.drawio` ファイルを開いて微調整するようユーザーに提案する。

### ステップ 7：最終エクスポート

ユーザーから承認を得たら、次の処理を行います：
- 要求されたフォーマット（PNG、SVG、PDF、JPG）すべてにエクスポート。指定が無ければデフォルトで PNG にする。
- `.drawio` のソースファイルと、エクスポートした画像（複数ある場合はすべて）のパスを報告する。
- **自動起動：** draw.io デスクトップで `.drawio` ファイルを開いて微調整するかをユーザーに尋ねる。`open diagram.drawio`（macOS）、`xdg-open`（Linux）、`start`（Windows）など。
- ファイルが保存され、使える状態になっていることを確認する。

## スタイルプリセット

**スタイルプリセット**は、ユーザーのビジュアル設定（パレット、形状、フォント、エッジ）をまとめた、名前付きの JSON ファイルです。有効なときは、このスキルの組み込みの色／形状規約をすべてプリセットの内容で置き換えます。

SKILL.md のステップ 0.5 でプリセット名を解決する際の**検索順序**：
1. `~/.drawio-skill/styles/<name>.json` — ユーザープリセット（`git pull` でも残る）
2. `<this-skill-dir>/styles/built-in/<name>.json` — 同梱の組み込み（`default`、`corporate`、`handdrawn`）

ファイル操作の前に、ユーザー指定の名前は必ず小文字に変換してください（スキーマは小文字を強制します）。

**それ以外のすべて — 学習フロー（ファイルからのプリセット抽出）、管理操作（一覧／デフォルト／削除／名前変更）、適用ルール（色の解決、形状キーワード、エッジ、フォント、追加要素、図表タイププリセットとの組み合わせ）、検証 — については `references/style-presets.md` を参照してください。** これらのフローをユーザーが実行したときや、現在の生成に有効なプリセットを適用しなければならないときにのみ読み込みます。

## Draw.io XML 構造

### ファイルスケルトン

```xml
<?xml version="1.0" encoding="UTF-8"?>
<mxfile host="drawio" version="26.0.0">
  <diagram name="Page-1">
    <mxGraphModel>
      <root>
        <mxCell id="0" />
        <mxCell id="1" parent="0" />
        <!-- user shapes start at id="2" -->
      </root>
    </mxGraphModel>
  </diagram>
</mxfile>
```

**ルール：**
- `id="0"` と `id="1"` は必須のルートセル。絶対に省略しないこと。
- ユーザーの形状は `id="2"` から始め、以降は順番にインクリメントする。
- すべての形状は `parent="1"` を持つ（コンテナの中にある場合は、コンテナの id を使う）。
- 正しくレンダリングされるよう、すべてのテキストでスタイルに `html=1` を指定する。
- **XML コメント内で `--` を絶対に使わない**。XML 仕様で禁止されており、パースエラーの原因になる。
- 属性値の中の特殊文字はエスケープする：`&amp;`、`&lt;`、`&gt;`、`&quot;`。
- **ラベル内で改行を入れたい場合：** `value` 属性の中ではリテラルの `\n` ではなく `&#xa;` を使う。例：`value="Line 1&#xa;Line 2"`。

### 形状タイプ（頂点）

| スタイルキーワード | 用途 |
|--------------|---------|
| `rounded=0` | 通常の矩形（デフォルト） |
| `rounded=1` | 角丸矩形 — サービス、モジュール用 |
| `ellipse;` | 円／楕円 — 開始／終了、データベース用 |
| `rhombus;` | ひし形 — 判定ポイント用 |
| `shape=mxgraph.aws4.resourceIcon;` | AWS アイコン |
| `shape=cylinder3;` | 円柱 — データベース用 |
| `swimlane;` | タイトルバー付きのグループ／コンテナ |

### 必須プロパティ

```xml
<!-- Rectangle / rounded box -->
<mxCell id="2" value="Label" style="rounded=1;whiteSpace=wrap;html=1;fillColor=#dae8fc;strokeColor=#6c8ebf;" vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="160" height="60" as="geometry" />
</mxCell>

<!-- Cylinder (database) -->
<mxCell id="3" value="DB" style="shape=cylinder3;whiteSpace=wrap;html=1;fillColor=#f5f5f5;strokeColor=#666666;fontColor=#333333;" vertex="1" parent="1">
  <mxGeometry x="350" y="100" width="120" height="80" as="geometry" />
</mxCell>

<!-- Diamond (decision) -->
<mxCell id="4" value="Check?" style="rhombus;whiteSpace=wrap;html=1;fillColor=#fff2cc;strokeColor=#d6b656;" vertex="1" parent="1">
  <mxGeometry x="100" y="220" width="160" height="80" as="geometry" />
</mxCell>
```

### コンテナとグループ

ネストされた要素を持つアーキテクチャ図では、draw.io の親子の入れ子関係を使ってください。大きな形状の上に小さな形状を**ただ重ねるだけ**ではいけません。

| 種類 | スタイル | 使い所 |
|------|-------|-------------|
| **グループ**（見えない） | `group;pointerEvents=0;` | 枠線が不要で、コンテナ自体に接続が無い場合 |
| **スイムレーン**（タイトル付き） | `swimlane;startSize=30;` | コンテナにタイトルバーが必要、またはコンテナ自体に接続がある場合 |
| **カスタムコンテナ** | 任意の形状に `container=1;pointerEvents=0;` を追加 | 自身に接続を持たない汎用のコンテナとして使う任意の形状 |

**主なルール：**
- 子要素どうしの接続を吸収させないコンテナには、スタイルに `pointerEvents=0;` を追加する。
- 子は `parent="containerId"` を設定し、座標は**コンテナを基準にした相対座標**にする。

```xml
<!-- Swimlane container -->
<mxCell id="svc1" value="User Service" style="swimlane;startSize=30;fillColor=#dae8fc;strokeColor=#6c8ebf;" vertex="1" parent="1">
  <mxGeometry x="100" y="100" width="300" height="200" as="geometry"/>
</mxCell>
<!-- Child inside container — coordinates relative to parent -->
<mxCell id="api1" value="REST API" style="rounded=1;whiteSpace=wrap;html=1;" vertex="1" parent="svc1">
  <mxGeometry x="20" y="40" width="120" height="60" as="geometry"/>
</mxCell>
<mxCell id="db1" value="Database" style="shape=cylinder3;whiteSpace=wrap;html=1;" vertex="1" parent="svc1">
  <mxGeometry x="160" y="40" width="120" height="60" as="geometry"/>
</mxCell>
```

### コネクター（エッジ）

**重要：** エッジの `mxCell` には必ず子要素 `<mxGeometry relative="1" as="geometry" />` を含めてください。自己閉じのエッジセル（`<mxCell ... edge="1" ... />`）は**不正な形式**で、レンダリングされません。必ず展開形式を使ってください。

```xml
<!-- Directed arrow — always include rounded, orthogonalLoop, jettySize for clean routing -->
<mxCell id="10" value="" style="edgeStyle=orthogonalEdgeStyle;rounded=1;orthogonalLoop=1;jettySize=auto;html=1;" edge="1" parent="1" source="2" target="3">
  <mxGeometry relative="1" as="geometry" />
</mxCell>

<!-- Arrow with label + explicit entry/exit points to control direction -->
<mxCell id="11" value="HTTP/REST" style="edgeStyle=orthogonalEdgeStyle;rounded=1;orthogonalLoop=1;jettySize=auto;html=1;exitX=0.5;exitY=1;exitDx=0;exitDy=0;entryX=0.5;entryY=0;entryDx=0;entryDy=0;" edge="1" parent="1" source="2" target="4">
  <mxGeometry relative="1" as="geometry" />
</mxCell>

<!-- Arrow with waypoints — use when edge must route around other shapes -->
<mxCell id="12" value="" style="edgeStyle=orthogonalEdgeStyle;rounded=1;orthogonalLoop=1;jettySize=auto;html=1;" edge="1" parent="1" source="3" target="5">
  <mxGeometry relative="1" as="geometry">
    <Array as="points">
      <mxPoint x="500" y="50" />
    </Array>
  </mxGeometry>
</mxCell>
```

**エッジスタイルのルール：**
- **アニメーションコネクター：** 矢印に沿って動くドットアニメーションを表示したい場合は、エッジスタイルに `flowAnimation=1;` を追加します。SVG エクスポートと draw.io デスクトップで動作し、データフロー図やパイプライン図に最適です。例：`style="edgeStyle=orthogonalEdgeStyle;flowAnimation=1;rounded=1;..."`。
- **常に** `rounded=1;orthogonalLoop=1;jettySize=auto` を含めること。これらを指定すると、重なりを避けるスマートなルーティングが有効になります。
- ノードに 2 本以上の接続がある場合は、各エッジに `exitX/exitY/entryX/entryY` を明示的に指定して、形状の周囲に線を分散させます。
- エッジが途中の形状を回り込まなければならない場合は、`<Array as="points">` でウェイポイントを追加します。
- **矢印の先端のためのスペースを確保すること：** 最後の曲がりからターゲット形状までの直線部分は 20px 以上必要です。短すぎると矢印の先端が曲がり部分と重なって不自然に見えます。ノード間隔を広げるか、明示的にウェイポイントを追加して対応してください。

### 1 つの形状での接続の分散

複数のエッジが同じ形状に接続するときは、線が重ならないように入出力点を別々に割り当てます：

| 位置 | exitX/entryX | exitY/entryY | 用途 |
|----------|-------------|-------------|----------|
| 上の中央 | 0.5 | 0 | 上のノードに接続するとき |
| 左上寄り | 0.25 | 0 | 上側から 2 本目の接続 |
| 右上寄り | 0.75 | 0 | 上側から 3 本目の接続 |
| 右の中央 | 1 | 0.5 | 右のノードに接続するとき |
| 下の中央 | 0.5 | 1 | 下のノードに接続するとき |
| 左の中央 | 0 | 0.5 | 左のノードに接続するとき |

**ルール：** 形状の 1 辺に N 本の接続がある場合は、均等に並べます（例：下辺に 3 本なら `exitX` を 0.25、0.5、0.75 にする）。

### カラーパレット（fillColor / strokeColor）

*プリセットが有効でないときのみ使用します（上の「Applying a preset」を参照）。*

| 色名 | fillColor | strokeColor | 用途 |
|-----------|-----------|-------------|---------|
| 青 | `#dae8fc` | `#6c8ebf` | サービス、クライアント |
| 緑 | `#d5e8d4` | `#82b366` | 成功、データベース |
| 黄 | `#fff2cc` | `#d6b656` | キュー、判定 |
| オレンジ | `#ffe6cc` | `#d79b00` | ゲートウェイ、API |
| 赤／ピンク | `#f8cecc` | `#b85450` | エラー、アラート |
| グレー | `#f5f5f5` | `#666666` | 外部／中立 |
| 紫 | `#e1d5e7` | `#9673a6` | セキュリティ、認証 |

### レイアウトのコツ

**間隔は複雑度に応じて調整します：**

| 図表の複雑度 | ノード数 | 横方向の間隔 | 縦方向の間隔 |
|-------------------|-------|----------------|--------------|
| シンプル | 5 以下 | 200px | 150px |
| 中 | 6〜10 | 280px | 200px |
| 複雑 | 11 以上 | 350px | 250px |

**ルーティング用の通り道：** 形状の行／列の間に、エッジが形状を横切らずに走らせられる 80px ほどの余白を追加で確保してください。エッジが横切る必要がある隙間には、形状を置かないようにします。

**グリッド整列：** `x`、`y`、`width`、`height` の値はすべて **10 の倍数**にそろえてください。こうしておくと、draw.io のデフォルトグリッド上で形状がきれいに整列し、手動編集も楽になります。

**全般的なルール：**
- x/y 座標を決める前に、まず紙の上か頭の中でノード配置をざっとスケッチしておく。
- 関連するノードは同じ横帯または縦帯にまとめる。
- 枠線が見える論理的なグループ分けには `swimlane` セルを使う。
- 接続数の多いハブノードは中央に置き、エッジが外側に放射するようにする（中央で交差しないようにする）。
- 垂直方向のまっすぐな接続を強制したい場合は、エッジに次のように明示的に入出力点を固定する：
  `exitX=0.5;exitY=1;exitDx=0;exitDy=0;entryX=0.5;entryY=0;entryDx=0;entryDy=0`
- 斜めのルーティングを避けるため、子ノードは親の真下に中央揃え（同じ中心 x）で配置する。
- **イベントバスパターン：** Kafka／バスのノードは下ではなく、**サービス行の中央**に置く。これで両側のサービスは短い水平矢印で到達でき（左側は `exitX=1`、右側は `exitX=0`）、線の交差がなくなる。
- 水平方向の接続（`exitX=1` または `exitX=0`）は、同じ行の縦に並んだノードを横切らない。ピア間の接続やパブリッシュ用の接続にはこれを使う。

**エッジと形状の重なりを避けるには：**
- 座標を確定する前に、各エッジの経路を頭の中でたどってみる。無関係な形状を横切ってしまうなら、形状を動かすかウェイポイントを追加する。
- ツリー／階層レイアウトの場合：ノードをレイヤー（行）に割り当て、隣接するレイヤー同士だけを接続して、交差を最小化する。
- スター／ハブレイアウトの場合：ハブを中央に置き、衛星ノードをその周りに配置する。エッジが短く、放射状になる。
- エッジが複数の行／列をまたがざるを得ない場合は、図表の中央を通さず、外側の通り道に沿って走らせる。

## エクスポート

### コマンド

エクスポートには**2 つのモード**があります：

- **プレビュー／セルフチェック**（ワークフローのステップ 4）— `-e` なし。出力は `diagram.png`。ビジョンによるセルフチェックに必要です。ここで `-e` を付けると、ビジョン API から 400 "Could not process image" エラーが返ってきます（issue #8）。
- **最終／成果物**（ステップ 7）— `-e` を付ける。出力は `diagram.drawio.png`。XML が埋め込まれているので、ファイルは draw.io で編集可能なまま保てます。

```bash
# Preview PNG (use this in step 4, before self-check) — NO -e
draw.io -x -f png -s 2 -o diagram.png input.drawio

# Final PNG (step 7, after user approval) — WITH -e, double extension
draw.io -x -f png -e -s 2 -o diagram.drawio.png input.drawio

# macOS — full path (if not in PATH); preview / final variants
/Applications/draw.io.app/Contents/MacOS/draw.io -x -f png -s 2 -o diagram.png input.drawio
/Applications/draw.io.app/Contents/MacOS/draw.io -x -f png -e -s 2 -o diagram.drawio.png input.drawio

# Windows
"C:\Program Files\draw.io\draw.io.exe" -x -f png -e -s 2 -o diagram.drawio.png input.drawio

# Linux (headless — requires xvfb-run; on servers add HOME and --disable-gpu)
export HOME=${HOME:-/tmp}
xvfb-run -a --server-args="-screen 0 1280x1024x24" \
  draw.io -x -f png -e -s 2 -o diagram.drawio.png input.drawio --disable-gpu
# Running as root (CI / Docker)? Append --no-sandbox AT THE END (placing it earlier makes drawio treat it as the input filename)

# SVG export (final — -e is safe; SVG is text)
draw.io -x -f svg -e -o diagram.svg input.drawio

# PDF export (final)
draw.io -x -f pdf -e -o diagram.pdf input.drawio

# Custom output directory (e.g. CI artifacts dir) — create if missing, then export there
mkdir -p ./artifacts && draw.io -x -f png -e -s 2 -o ./artifacts/diagram.drawio.png input.drawio
```

### エクスポート後の PNG 修復（`-e` 付き PNG エクスポートのたびに必須）

draw.io CLI は `-e` 付きで PNG を出力するときに IEND チャンクを切り詰めてしまいます。ファイルは 4 バイトの IEND 長さフィールドで終わっていますが、続く `IEND` タイプ + CRC（8 バイト）が欠落しています。結果として、ビジョン API は 400 "Could not process image" を返し、厳密な PNG デコーダはエラーを出します。SVG/PDF は影響を受けません。

`-e` 付き PNG エクスポートの直後に、必ず次のコマンドを実行してください：

```bash
python3 <this-skill-dir>/scripts/repair_png.py diagram.drawio.png
```

このスクリプトは `endswith(IEND)` ガードを持っているので、draw.io 側でこのバグが修正された後は何もしない動作になります。無条件に実行しても安全です。

**主なフラグ：**
- `-x` — エクスポートモード（必須）
- `-f` — フォーマット：`png`、`svg`、`pdf`、`jpg`
- `-e` — 出力に図表 XML を埋め込む（PNG、SVG、PDF）。エクスポートされたファイルが draw.io で編集可能なまま保てる。**ステップ 5 のセルフチェック用のプレビュー PNG では付けないこと**。`-e` PNG は IEND チャンクが切り詰められており、ビジョン API が受け付けない（issue #8）。最終 PNG エクスポートでは `-e` を残し、その後 `scripts/repair_png.py` を実行する（上の「エクスポート後の PNG 修復」を参照）。SVG/PDF は影響を受けない。
- `-s` — 倍率：`1`、`2`、`3`（PNG は 2 を推奨）
- `-o` — 出力ファイルのパス。任意のディレクトリを指定できる（例：`./artifacts/diagram.drawio.png`）。対象ディレクトリは先に `mkdir -p` しておく。XML を埋め込む場合は `.drawio.png` の二重拡張子を使う。
- `-b` — 図表の周囲の余白幅（デフォルト：0、推奨：10）
- `-t` — 背景を透明にする（PNG のみ）
- `--page-index 0` — 特定のページのみエクスポート（デフォルトはすべて）

### ブラウザフォールバック（CLI 不要）

draw.io デスクトップ CLI が使えないときは、クライアント側で開けるビューワー URL を生成します：

```bash
python3 <this-skill-dir>/scripts/encode_drawio_url.py input.drawio
```

このコマンドは、deflate 圧縮 + base64 エンコードした図表 XML を URL フラグメントに含む `https://viewer.diagrams.net/...` 形式の URL を出力します。フラグメント（`#` 以降）はサーバーには送信されないので、何もアップロードされません。図表はクライアント側で開かれ、表示・編集ができます。ユーザーがデスクトップアプリをインストールできないときに便利です。

### フォールバックの順序

ツールが使えないときは、段階的に対処を切り替えます：

| シナリオ | 挙動 |
|----------|----------|
| draw.io CLI なし、Python あり | ブラウザフォールバック（diagrams.net URL）を使う。 |
| draw.io CLI も Python も無い | `.drawio` XML のみを生成し、draw.io デスクトップか diagrams.net で開いてもらうよう案内する。 |
| macOS サンドボックス分離環境で draw.io CLI がクラッシュ／出力が空 | CLI をサンドボックス内では使えないものとして扱い、ブラウザフォールバックか XML のみを使う。CLI エクスポートはサンドボックス外のホスト環境で実行するようユーザーに依頼する。 |
| セルフチェック用のビジョンが使えない | セルフチェック（ステップ 5）をスキップし、エクスポートした PNG をそのままユーザーに見せる。 |
| エクスポートが失敗する（Chromium／ディスプレイ関連の問題） | Linux なら `xvfb-run -a` でリトライ。それでも失敗するなら `.drawio` XML を提供し、手動でのエクスポートを提案する。 |
| Linux サーバー（ヘッドレス）でエクスポートが失敗する | 順に試す：(1) `xvfb-run -a`、(2) root の場合は末尾に `--no-sandbox` を追加、(3) `--disable-gpu` を追加、(4) `export HOME=/tmp`、(5) apt の依存関係をインストール（`libgtk-3-0 libnotify4 libnss3 libgbm1 libasound2t64` など）、(6) [tomkludy/drawio-renderer](https://hub.docker.com/r/tomkludy/drawio-renderer) の Docker（ヘッドレスエクスポート用 REST API）にフォールバック。 |

### draw.io が PATH にあるかの確認

```bash
# Try short command first
if command -v draw.io &>/dev/null; then
  DRAWIO="draw.io"
elif [ -f "/Applications/draw.io.app/Contents/MacOS/draw.io" ]; then
  DRAWIO="/Applications/draw.io.app/Contents/MacOS/draw.io"
else
  echo "draw.io not found — install from https://github.com/jgraph/drawio-desktop/releases"
fi
```

## よくある間違い

何かおかしい挙動（エクスポート失敗、ビジョンが PNG を拒否、レイアウトの崩れ、エッジルーティングの不正など）に遭遇したら、`references/troubleshooting.md` を参照してください。間違いと修正方法を 1 行ずつまとめた表があります。

## 図表タイププリセット

ユーザーが特定の図表タイプを依頼したときは、それに対応するプリセット（形状、エッジ、レイアウト方向）について `references/diagram-types.md` を参照します。ユーザーの言い回しから次のように選びます：

| ユーザーの発話 | `references/diagram-types.md` の対応セクション |
|---|---|
| "ER diagram"、"schema diagram"、"data model" | ERD |
| "UML class diagram"、"class diagram" | UML Class |
| "sequence diagram"、"interaction diagram"、"lifeline" | Sequence |
| "architecture"、"system diagram"、"service diagram" | Architecture |
| "neural network"、"model architecture"、"ML diagram"、"deep learning" | ML / Deep Learning Model |
| "flowchart"、"decision tree"、"process flow" | Flowchart |

図表タイププリセットは**構造的**なスタイルキーワードを定義します。ユーザースタイルプリセットも有効な場合（`## Style Presets` を参照）は、構造的なキーワードはそのまま残し、その上に色・フォント・エッジ・追加要素を重ねます。マージのルールは `references/style-presets.md` の "Interaction with diagram-type presets" を参照してください。
