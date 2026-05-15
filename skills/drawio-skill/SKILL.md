---
name: drawio-skill
version: 1.5.2
description: ユーザーが図表、フローチャート、アーキテクチャ図、または可視化を要求するときに使用する。また、3 つ以上のコンポーネントを持つシステム、複雑なデータフロー、または視覚的表現が有益な関係を説明するときにプロアクティブに使用する。draw.io デスクトップのネイティブ CLI を使ってローカルで `.drawio` XML ファイルを生成し、PNG/SVG/PDF にエクスポートする。
license: MIT
homepage: https://github.com/Agents365-ai/drawio-skill
compatibility: draw.io デスクトップアプリの CLI が PATH 上にある必要がある（macOS/Linux/Windows）。セルフチェックステップにはビジョン対応モデル（例：Claude Sonnet/Opus）が必要；利用不可の場合は丁寧にスキップ。
platforms: [macos, linux, windows]
metadata: {"openclaw":{"requires":{"anyBins":["draw.io","drawio"]},"emoji":"📐","os":["darwin","linux","win32"],"install":[{"id":"brew-drawio","kind":"brew","formula":"drawio","bins":["draw.io"],"label":"Install draw.io via Homebrew","os":["darwin"]}]},"hermes":{"tags":["drawio","diagram","flowchart","architecture","visualization","uml"],"category":"design","requires_tools":["draw.io"],"related_skills":["mermaid","excalidraw","plantuml"]},"author":"Agents365-ai","version":"1.5.2"}
---

# Draw.io 図表

## 概要

draw.io デスクトップアプリの CLI を使ってローカルで `.drawio` XML ファイルを生成し、PNG/SVG/PDF/JPG にエクスポートする。

**サポートされているフォーマット：** PNG、SVG、PDF、JPG — ブラウザの自動化は不要。

PNG、SVG、PDF のエクスポートは `--embed-diagram`（`-e`）をサポート — エクスポートしたファイルには完全な図表 XML が含まれるため、draw.io で開くと編集可能な図表を復元できる。埋め込まれた XML を示すには二重拡張子（`name.drawio.png`）を使う。

## 同梱リソース

ワークフローがこれらのいずれかを参照するとき、オンデマンドで読む — 事前にコンテキストに入れる必要はない。

| ファイル | 読むタイミング |
|---|---|
| `references/diagram-types.md` | ユーザーが特定の図表タイプ（ERD、UML クラス、シーケンス、アーキテクチャ、ML/DL、フローチャート）を指名したとき |
| `references/style-presets.md` | ユーザーがスタイルプリセットの学習 / 保存 / 一覧 / デフォルト設定 / 削除を求めたとき、またはアクティブなプリセットを解決して適用ルールが必要なとき |
| `references/style-extraction.md` | Learn フローの中で抽出手順が必要なとき（`style-presets.md` から呼ばれる） |
| `references/troubleshooting.md` | エクスポートが失敗、ビジョンが PNG を拒否、またはレンダリングが間違って見えるとき |
| `scripts/repair_png.py` | すべての `-e` PNG エクスポートの後 — draw.io の切り詰められた IEND チャンクを修正（issue #8） |
| `scripts/encode_drawio_url.py` | CLI が利用不可で、ブラウザフォールバックの diagrams.net URL が必要なとき |

## 前提条件

draw.io デスクトップアプリがインストールされ、CLI にアクセス可能であること：

**macOS sandbox / sandbox isolation メモ（例：codex.app）：** 一部のサンドボックス化された macOS 環境では、draw.io デスクトップ CLI を呼び出すと（`draw.io --version` ですら）draw.io プロセスがクラッシュするか、出力が無い場合がある。その場合、CLI を**このサンドボックス分離下では利用不可**として扱う — サンドボックス内でリトライを続けない。CLI エクスポート作業には**サンドボックス化されていないホスト環境**（サンドボックス分離の外）を優先するか、ブラウザフォールバック / XML 専用出力を使う。

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

無ければ draw.io デスクトップをインストール：
- macOS: `brew install --cask drawio` または https://github.com/jgraph/drawio-desktop/releases からダウンロード
- Windows: https://github.com/jgraph/drawio-desktop/releases からインストーラーをダウンロード
- Linux: https://github.com/jgraph/drawio-desktop/releases から `.deb`/`.rpm` をダウンロード — **snap を使わない**（AppArmor サンドボックスがサーバー上で secrets/keyring を拒否し、クラッシュの原因になる）

## ワークフロー

ワークフローを開始する前に、ユーザーの要求が十分に具体的かを評価する。重要な詳細が欠けている場合、1-3 個の的を絞った質問をする：
- **図表タイプ** — どのプリセット？（ERD、UML、シーケンス、アーキテクチャ、ML/DL、フローチャート、または一般）
- **出力フォーマット** — PNG（デフォルト）、SVG、PDF、JPG？
- **出力場所** — デフォルトはユーザーの作業ディレクトリ；ユーザーが指定した明示的なパスがあればそれを尊重する（例：「`./artifacts/` に置いて」）。言及がなければ尋ねない。
- **スコープ/忠実度** — コンポーネントはいくつ？特定の技術やラベルはあるか？

要求がすでにこれらの詳細を指定している、または明らかに単純（例：「X のフローチャートを描いて」）であれば、明確化はスキップする。

**ステップ 0 — アクティブなプリセットを解決。** この生成に適用される（もしあれば）ユーザー定義のスタイルプリセットを決定する。

- ユーザーのメッセージから、スタイルプリセットを明確に名指しするフレーズをスキャン："use my `<name>` style"、"with my `<name>` style"、"in `<name>` mode"、"in the style of `<name>`"。裸の `with <name>` は**カウントしない** — "draw a diagram with redis" はスタイルではなくコンポーネントを名指ししている。明確な一致があれば → アクティブなプリセット = `<name>`。
- そうでなければ、`~/.drawio-skill/styles/` で `"default": true` のファイルをチェック。あれば → アクティブなプリセット = それ。
- そうでなければ → アクティブなプリセットなし；ワークフローの残りで組み込みの色/形状/エッジ規約にフォールスルー。

プリセット JSON を `~/.drawio-skill/styles/<name>.json` からロード、見つからなければ `<this-skill-dir>/styles/built-in/<name>.json` にフォールバック。指定されたプリセットがどちらの場所にも存在しない場合、ユーザーに名前が不明だと伝え、利用可能なプリセット（ユーザーディレクトリ + 組み込み）を一覧し、停止する — 静かにデフォルトにフォールバック**しない**。

プリセットが正常にロードされたら、返信の最初の行で言及する：*"Using preset `<name>` (confidence: `<level>`)."* プリセットが色/形状/エッジ/フォントの決定をどう変えるかは、下の **Applying a preset** サブセクションを参照。

1. **依存関係をチェック** — `draw.io --version` が成功することを確認；正しい CLI パスのためにプラットフォームをメモ
2. **計画** — 形状、関係、レイアウト（LR または TB）を特定、階層/レイヤーでグループ化
3. **生成** — `.drawio` XML ファイルをディスクに書く。デフォルトの出力ディレクトリはユーザーの作業ディレクトリ；ユーザーが出力パスやディレクトリ（例：`./artifacts/`、`docs/images/`）を指定した場合はそれを使う — まず対象ディレクトリを `mkdir -p`。ステップ 4 と 7 の PNG/SVG/PDF エクスポートにも同じディレクトリ選択を適用。
4. **ドラフトをエクスポート** — CLI を実行してプレビュー PNG を生成。**このステップで `-e` を渡さない** — それが追加する埋め込まれた `zTXt mxGraphModel` チャンクは、ビジョン API（Claude を含む）でステップ 5 の 400 "Could not process image" を返す原因になる。クリーンなプレビューを `<name>.png`（単一拡張子）として保存。埋め込みは最終エクスポートのみ（ステップ 7）。
5. **セルフチェック** — エージェントの組み込みビジョン機能を使ってエクスポートされた PNG を読み、明らかな問題を捕らえ、ユーザーに見せる前に自動修正する（Claude Sonnet/Opus のようなビジョン対応モデルが必要）。PNG の読み取りが 400 / "Could not process image" エラーを返した場合、ほぼ確実に誤って `-e` でエクスポートした — `-e` 無しで再エクスポートし、一度リトライ。それでも失敗したら、セルフチェックをスキップしてステップ 6 へ続行。
6. **レビューループ** — ユーザーに画像を見せ、フィードバックを集め、的を絞った XML 編集を適用し、再エクスポートし、承認されるまで繰り返す
7. **最終エクスポート** — 承認版をすべての要求されたフォーマットに再エクスポート。ここで `-e` を使う（PNG/SVG/PDF）ので、成果物は draw.io で編集可能なまま；埋め込まれた XML を示すために `<name>.drawio.png` として保存。**`-e` 付きの PNG の場合、その直後に `python3 <this-skill-dir>/scripts/repair_png.py <name>.drawio.png` を実行** — draw.io の CLI は `-e` PNG 出力で IEND チャンクを切り詰める（8 バイト欠落）、その結果、ビジョン API と厳密な PNG デコーダが拒否する破損ファイルを生成する（issue #8）。ファイルパスを報告。

**`draw.io --version` がクラッシュするか何も出力しない場合（codex.app のような制限された macOS sandbox isolation で一般的）：**
- サンドボックス内で CLI 呼び出しのリトライを続けない。
- ステップ 4、5、6、7（CLI エクスポート + PNG ベースのレビュー）をスキップし、**ブラウザフォールバック**（`scripts/encode_drawio_url.py`）を使うか `.drawio` XML のみを提供する。
- ユーザーが PNG/SVG/PDF 出力を必要とする場合、**サンドボックス化されていないホスト環境**（サンドボックス分離の外）でエクスポートコマンドを実行し、結果のファイルを共有するよう依頼する。

エスカレーションルール：
- バイナリが PATH（または既知のアプリパス）に存在するが、実行が異常終了、空出力、Electron 起動失敗、ディスプレイ/セッションエラー、またはサンドボックス制限の可能性で失敗する場合、フォールバックの前に 1 回のエスカレーションリトライを優先する。
- バイナリが完全に欠落している場合、より積極的に検索するためにエスカレーションしない；インストールガイダンスまたはフォールバックに進む。

### ステップ 5：セルフチェック

ドラフト PNG をエクスポートした後、エージェントのビジョン機能（例：Claude の画像入力）を使って画像を読み、ユーザーに見せる前にこれらの問題をチェックする。エージェントがビジョンをサポートしない場合、セルフチェックをスキップし、直接 PNG を表示する。

**重要：** ここで読まれるドラフト PNG は `-e` **無し**でエクスポートされていなければならない。Draw.io の `-e` フラグは、Anthropic ビジョン API が 400 "Could not process image" で拒否する切り詰められた IEND チャンク（type+CRC の 8 バイトが欠落）を持つ PNG を出力する（issue #8）。プレビューステップの最も単純な修正は `-e` を完全にスキップすること；ステップ 7 の最終エクスポートは `-e` を保持し、修復スニペットを実行する。ここで 400 エラーを見たら、`-e` 無しで再エクスポートし一度リトライ；それでも失敗（他の理由）なら、セルフチェックをスキップしてステップ 6 に進む。

| チェック | 探すもの | 自動修正アクション |
|-------|-----------------|-----------------|
| 重なり合う形状 | 2 つ以上の形状が積み重なっている | 形状を ≥200px シフトして離す |
| クリップされたラベル | テキストが形状境界で切れている | ラベルに合わせて形状の幅/高さを増やす |
| 欠落した接続 | 視覚的に形状に接続しない矢印 | `source`/`target` の id が既存のセルと一致することを確認 |
| キャンバス外の形状 | 負の座標、またはメイングループから遠い位置の形状 | クラスター近くの正の座標に移動 |
| エッジと形状の重なり | エッジ/矢印が無関係な形状を視覚的に貫通 | ウェイポイント（`<Array as="points">`）を追加して形状を回避、または形状間のスペーシングを増やす |
| エッジのスタッキング | 複数のエッジが同じパス上で互いに重なる | 形状の周辺上にエントリー/エグジット点を分散（異なる exitX/entryX 値を使用） |

- 最大**2 回のセルフチェックラウンド** — 2 回の修正後も問題が残るなら、それでもユーザーに見せる
- 各修正後に再エクスポートして、新しい PNG を読み直す

### ステップ 6：レビューループ

セルフチェックの後、エクスポートされた画像を表示し、ユーザーにフィードバックを求める。

**ターゲット編集ルール** — 各タイプのフィードバックに対して、最小限の XML 変更を適用する：

| ユーザーの要求 | XML 編集アクション |
|-------------|----------------|
| X の色を変更 | `value` が X と一致する `mxCell` を見つけ、`style` の `fillColor`/`strokeColor` を更新 |
| 新しいノードを追加 | 次に利用可能な `id` で新しい `mxCell` 頂点を追加し、関連ノードの近くに配置 |
| ノードを削除 | `mxCell` 頂点と、一致する `source`/`target` を持つエッジを削除 |
| 形状 X を移動 | 一致する `mxCell` の `mxGeometry` の `x`/`y` を更新 |
| 形状 X をリサイズ | 一致する `mxCell` の `mxGeometry` の `width`/`height` を更新 |
| A から B への矢印を追加 | A と B の id に一致する `source`/`target` を持つ新しい `mxCell` エッジを追加 |
| ラベルテキストを変更 | 一致する `mxCell` の `value` 属性を更新 |
| レイアウト方向を変更 | **完全な再生成** — 新しい方向で XML を再構築 |

**ルール：**
- 単一要素の変更：既存の XML をその場で編集 — 以前のイテレーションのレイアウト調整を保持する
- レイアウト全体の変更（例：LR↔TB を入れ替え、「やり直し」）：完全な XML を再生成する
- 各イテレーションで同じ `{name}.png`（`-e` 無し）を上書き — `v1`、`v2`、`v3` ファイルを作らない。`-e` はステップ 7 の最終エクスポート専用。
- 編集を適用したら、再エクスポートして更新された画像を表示
- ユーザーが承認 / 完了 / LGTM と言うまでループは続く
- **安全弁：** 5 イテレーションラウンドの後、ユーザーに draw.io デスクトップで `.drawio` ファイルを開いて細かい調整をすることを提案

### ステップ 7：最終エクスポート

ユーザーが承認したら：
- 要求されたすべてのフォーマット（PNG、SVG、PDF、JPG）にエクスポート — 指定がなければデフォルトで PNG
- `.drawio` ソースファイルとエクスポートされた画像（複数の場合あり）の両方のファイルパスを報告
- **自動起動：** draw.io デスクトップで `.drawio` ファイルを開いて微調整することを提案 — `open diagram.drawio`（macOS）、`xdg-open`（Linux）、`start`（Windows）
- ファイルが保存され使用可能であることを確認

## スタイルプリセット

**スタイルプリセット**は、ユーザーのビジュアル設定（パレット、形状、フォント、エッジ）を捕捉した名前付き JSON ファイル。アクティブな場合、このスキルの組み込みの色/形状規約を完全に置き換える。

SKILL.md のステップ 0.5 がプリセット名を解決するときの**検索順序**：
1. `~/.drawio-skill/styles/<name>.json` — ユーザープリセット（`git pull` で残る）
2. `<this-skill-dir>/styles/built-in/<name>.json` — 同梱の組み込み（`default`、`corporate`、`handdrawn`）

ファイル操作の前に常にユーザー提供の名前を小文字にする — スキーマは小文字を強制する。

**その他のすべて — Learn フロー（ファイルからのプリセット抽出）、管理操作（一覧/デフォルト/削除/名前変更）、適用ルール（色検索、形状キーワード、エッジ、フォント、エクストラ、図表タイププリセットとの相互作用）、検証 — については `references/style-presets.md` を読むこと。** ユーザーがこれらのフローを呼び出したとき、または現在の生成にアクティブなプリセットを適用しなければならないときにのみ必要。

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
- `id="0"` と `id="1"` は必須のルートセル — 絶対に省略しない
- ユーザーの形状は `id="2"` から始まり、順次インクリメント
- すべての形状は `parent="1"` を持つ（コンテナ内の場合を除く — その場合はコンテナの id を使用）
- 適切なレンダリングのためにすべてのテキストはスタイルに `html=1` を使用
- **XML コメント内で `--` を絶対に使わない** — XML 仕様で違法でパースエラーを引き起こす
- 属性値の特殊文字をエスケープ：`&amp;`、`&lt;`、`&gt;`、`&quot;`
- **ラベル内の複数行テキスト：** `value` 属性内の改行には `&#xa;` を使う（リテラル `\n` ではない）。例：`value="Line 1&#xa;Line 2"`

### 形状タイプ（頂点）

| スタイルキーワード | 用途 |
|--------------|---------|
| `rounded=0` | 普通の矩形（デフォルト） |
| `rounded=1` | 角丸矩形 — サービス、モジュール |
| `ellipse;` | 円/楕円 — 開始/終了、データベース |
| `rhombus;` | ひし形 — ディシジョンポイント |
| `shape=mxgraph.aws4.resourceIcon;` | AWS アイコン |
| `shape=cylinder3;` | 円柱 — データベース |
| `swimlane;` | タイトルバー付きグループ/コンテナ |

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

ネストされた要素を持つアーキテクチャ図には、draw.io の親子包含を使う — より大きな形状の上に形状を置くだけでは**ない**。

| タイプ | スタイル | 使うとき |
|------|-------|-------------|
| **グループ**（不可視） | `group;pointerEvents=0;` | 可視のボーダーが不要、コンテナに接続が無い |
| **スイムレーン**（タイトル付き） | `swimlane;startSize=30;` | コンテナに可視のタイトルバーが必要、またはコンテナ自体に接続がある |
| **カスタムコンテナ** | 任意の形状に `container=1;pointerEvents=0;` を追加 | 自身の接続を持たないコンテナとして機能する任意の形状 |

**主要ルール：**
- 子の間の接続を捕らえないコンテナのスタイルに `pointerEvents=0;` を追加
- 子は `parent="containerId"` を設定し、**コンテナに対して相対的な**座標を使う

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

**重要：** 各エッジの `mxCell` は子要素 `<mxGeometry relative="1" as="geometry" />` を含まなければならない。自己閉じのエッジセル（`<mxCell ... edge="1" ... />`）は**不正**でレンダリングされない。常に展開形式を使う。

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

**エッジスタイルルール：**
- **アニメーションコネクター：** 矢印に沿って動くドットのアニメーションを表示するには、任意のエッジスタイルに `flowAnimation=1;` を追加する。SVG エクスポートと draw.io デスクトップで動作 — データフローとパイプライン図に最適。例：`style="edgeStyle=orthogonalEdgeStyle;flowAnimation=1;rounded=1;..."`
- **常に**`rounded=1;orthogonalLoop=1;jettySize=auto` を含める — これらは重なりを回避するスマートルーティングを有効にする
- ノードが 2 つ以上の接続を持つときは、各エッジに `exitX/exitY/entryX/entryY` を固定 — 形状の周辺に線を分散する
- エッジが中間の形状を迂回しなければならないときは `<Array as="points">` ウェイポイントを追加
- **矢印の頭部のスペースを残す：** 最後の曲がりとターゲット形状の間の最終の直線セグメントは ≥20px の長さでなければならない。短すぎると、矢印の頭部が曲がりと重なり、壊れて見える。ノード間のスペーシングを増やすか、明示的なウェイポイントを追加して修正

### 形状上の接続の分散

複数のエッジが同じ形状に接続するとき、スタッキングを防ぐために異なるエントリー/エグジット点を割り当てる：

| 位置 | exitX/entryX | exitY/entryY | 使うとき |
|----------|-------------|-------------|----------|
| 上中央 | 0.5 | 0 | 上のノードに接続するとき |
| 左上 | 0.25 | 0 | 上からの 2 番目の接続 |
| 右上 | 0.75 | 0 | 上からの 3 番目の接続 |
| 右中央 | 1 | 0.5 | 右のノードに接続するとき |
| 下中央 | 0.5 | 1 | 下のノードに接続するとき |
| 左中央 | 0 | 0.5 | 左のノードに接続するとき |

**ルール：** 形状が一辺に N 個の接続を持つ場合、それらを均等に配置する（例：底辺の 3 個の接続 → exitX = 0.25、0.5、0.75）

### カラーパレット（fillColor / strokeColor）

*プリセットがアクティブでないときのみ使用される（上の「Applying a preset」を参照）。*

| 色名 | fillColor | strokeColor | 用途 |
|-----------|-----------|-------------|---------|
| 青 | `#dae8fc` | `#6c8ebf` | サービス、クライアント |
| 緑 | `#d5e8d4` | `#82b366` | 成功、データベース |
| 黄 | `#fff2cc` | `#d6b656` | キュー、ディシジョン |
| オレンジ | `#ffe6cc` | `#d79b00` | ゲートウェイ、API |
| 赤/ピンク | `#f8cecc` | `#b85450` | エラー、アラート |
| グレー | `#f5f5f5` | `#666666` | 外部/中立 |
| 紫 | `#e1d5e7` | `#9673a6` | セキュリティ、認証 |

### レイアウトのヒント

**スペーシング — 複雑さに合わせてスケール：**

| 図表の複雑度 | ノード数 | 水平方向のギャップ | 垂直方向のギャップ |
|-------------------|-------|----------------|--------------|
| シンプル | ≤5 | 200px | 150px |
| 中 | 6–10 | 280px | 200px |
| 複雑 | >10 | 350px | 250px |

**ルーティングコリドー：** 形状の行/列の間に、エッジが形状を横切らずにルーティングできる ~80px の追加の空きコリドーを残す。エッジが横切る必要のあるギャップに形状を置かない。

**グリッド整列：** すべての `x`、`y`、`width`、`height` の値を **10 の倍数**にスナップする — これにより draw.io のデフォルトグリッド上で形状がきれいに整列し、手動編集が容易になる。

**一般ルール：**
- x/y 座標を割り当てる前にグリッドを計画する — まず紙/頭の中でノードの位置をスケッチ
- 関連するノードを同じ水平または垂直のバンドにグループ化する
- 可視ボーダー付きの論理的グルーピングには `swimlane` セルを使う
- 接続が多いハブノードを中央に配置 — エッジが交差する代わりに外側に放射する
- 強制的にまっすぐな垂直接続にするには、エッジに明示的にエントリー/エグジット点を固定：
  `exitX=0.5;exitY=1;exitDx=0;exitDy=0;entryX=0.5;entryY=0;entryDx=0;entryDy=0`
- 斜めルーティングを避けるため、常に親の下に子ノードを中央揃え（同じ中心 x）にする
- **イベントバスパターン**：Kafka/バスノードを下ではなく**サービス行の中央**に置く — 両側のサービスは短い水平矢印で到達できる（左側は `exitX=1`、右側は `exitX=0`）、すべての線交差を排除
- 水平接続（`exitX=1` または `exitX=0`）は同じ行の垂直ノードを横切らない；ピアツーピアおよびパブリッシュ接続にこれらを使う

**エッジと形状の重なりを避ける：**
- 座標を最終化する前に、各エッジパスを頭の中でたどる — 無関係な形状を横切らなければならないなら、形状を移動するかウェイポイントを追加
- ツリー/階層レイアウトの場合：ノードをレイヤー（行）に割り当て、隣接するレイヤー間のみ接続して交差を最小化
- スター/ハブレイアウトの場合：ハブを中央に、衛星をその周りに置く — エッジは短く放射状に
- エッジが複数の行/列にまたがらなければならない場合、図表の中央を通さず、外側のコリドーに沿ってルーティングする

## エクスポート

### コマンド

エクスポートには**2 つ**のモードがある：

- **プレビュー / セルフチェック**（ワークフローのステップ 4）— `-e` 無し。出力 `diagram.png`。ビジョンセルフチェックに必要；ここで `-e` を使うとビジョン API から 400 "Could not process image" エラーが返ってくる（issue #8）。
- **最終 / 成果物**（ステップ 7）— `-e` を渡す。出力 `diagram.drawio.png`。埋め込まれた XML により draw.io でファイルが編集可能なまま。

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

### エクスポート後の PNG 修復（`-e` PNG エクスポート後に必須）

draw.io CLI は `-e` PNG を出すときに IEND チャンクを切り詰める — ファイルは 4 バイトの IEND 長さフィールドで終わるが、`IEND` タイプ + CRC（8 バイト）が欠落している。結果：ビジョン API は 400 "Could not process image" を返し、厳密な PNG デコーダはエラーを出す。SVG/PDF には影響なし。

すべての `-e` PNG エクスポートの直後にこれを実行：

```bash
python3 <this-skill-dir>/scripts/repair_png.py diagram.drawio.png
```

スクリプトの `endswith(IEND)` ガードにより、draw.io が上流でバグを修正したら no-op になる — 無条件で実行しても安全。

**主要フラグ：**
- `-x` — エクスポートモード（必須）
- `-f` — フォーマット：`png`、`svg`、`pdf`、`jpg`
- `-e` — 出力に図表 XML を埋め込む（PNG、SVG、PDF）— エクスポートされたファイルが draw.io で編集可能なまま。**ステップ 5 のセルフチェックで使われるプレビュー PNG ではスキップ** — `-e` PNG は切り詰められた IEND チャンクを持ち、ビジョン API が拒否する（issue #8）。最終 PNG エクスポートでは `-e` を保持し、`scripts/repair_png.py` を実行（エクスポート後 PNG 修復を参照）。SVG/PDF には影響なし。
- `-s` — スケール：`1`、`2`、`3`（PNG は 2 を推奨）
- `-o` — 出力ファイルパス；任意のディレクトリを受け付ける（例：`./artifacts/diagram.drawio.png`）— まず対象ディレクトリを `mkdir -p`。埋め込み時は `.drawio.png` の二重拡張子を使う。
- `-b` — 図表の周りのボーダー幅（デフォルト：0、10 を推奨）
- `-t` — 透明背景（PNG のみ）
- `--page-index 0` — 特定のページをエクスポート（デフォルト：すべて）

### ブラウザフォールバック（CLI 不要）

draw.io デスクトップ CLI が利用不可のとき、クライアント側ビューワー URL を生成する：

```bash
python3 <this-skill-dir>/scripts/encode_drawio_url.py input.drawio
```

URL フラグメントに deflate 圧縮および base64 エンコードされた図表 XML を含む `https://viewer.diagrams.net/...` URL を出力する。フラグメント（`#` の後）はサーバーに送信されないため、何もアップロードされない — 図表はクライアント側で表示および編集のために開かれる。ユーザーがデスクトップアプリをインストールできないときに便利。

### フォールバックチェイン

ツールが利用不可のとき、丁寧に格下げする：

| シナリオ | 振る舞い |
|----------|----------|
| draw.io CLI 欠落、Python 利用可能 | ブラウザフォールバック（diagrams.net URL）を使う |
| draw.io CLI 欠落、Python 欠落 | `.drawio` XML のみ生成；ユーザーに draw.io デスクトップまたは diagrams.net で手動で開くよう指示 |
| macOS sandbox isolation で draw.io CLI がクラッシュ / 出力なし | CLI をサンドボックス内で利用不可として扱う；ブラウザフォールバック / XML 専用を使う；ユーザーにサンドボックス化されていないホスト環境で CLI エクスポートを実行するよう依頼 |
| セルフチェック用のビジョンが利用不可 | セルフチェック（ステップ 5）をスキップ；直接エクスポートされた PNG をユーザーに表示するように進む |
| エクスポートが失敗（Chromium/ディスプレイの問題） | Linux では `xvfb-run -a` でリトライ；それでも失敗するなら `.drawio` XML を提供し、手動エクスポートを提案 |
| Linux サーバー（ヘッドレス）でエクスポート失敗 | 順に試す：(1) `xvfb-run -a`、(2) root の場合、最後に `--no-sandbox` を追加、(3) `--disable-gpu` を追加、(4) `export HOME=/tmp`、(5) apt 依存関係をインストール（`libgtk-3-0 libnotify4 libnss3 libgbm1 libasound2t64` など）、(6) [tomkludy/drawio-renderer](https://hub.docker.com/r/tomkludy/drawio-renderer) Docker（ヘッドレスエクスポート用 REST API）にフォールバック |

### draw.io が PATH にあるかチェック

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

何かおかしいとき（エクスポート失敗、ビジョンが PNG を拒否、レイアウトが壊れている、エッジのルーティングがおかしい）、行ごとの間違い → 修正のテーブルが `references/troubleshooting.md` にある。

## 図表タイププリセット

ユーザーが特定の図表タイプを要求したとき、一致するプリセット（形状、エッジ、レイアウト方向）について `references/diagram-types.md` を読む。ユーザーの言い回しで選ぶ：

| ユーザーが言うこと | `references/diagram-types.md` のセクション |
|---|---|
| "ER diagram"、"schema diagram"、"data model" | ERD |
| "UML class diagram"、"class diagram" | UML Class |
| "sequence diagram"、"interaction diagram"、"lifeline" | Sequence |
| "architecture"、"system diagram"、"service diagram" | Architecture |
| "neural network"、"model architecture"、"ML diagram"、"deep learning" | ML / Deep Learning Model |
| "flowchart"、"decision tree"、"process flow" | Flowchart |

図表タイププリセットは**構造的**なスタイルキーワードを設定する。ユーザースタイルプリセットもアクティブな場合（`## Style Presets` を参照）、構造的キーワードを保持し、その上に色/フォント/エッジ/エクストラを重ねる — マージルールについては `references/style-presets.md` → "Interaction with diagram-type presets" を読むこと。
