# スタイルプリセット — 学習、適用、管理

**スタイルプリセット**とは、ユーザーのビジュアル設定 — パレット、形状語彙、フォント、エッジスタイル — を捕捉した名前付き JSON ファイルです。プリセットがアクティブな場合、SKILL.md の色/形状/エッジテーブルにある組み込み規約を完全に置き換えます。

このファイルを読むのは：
- ユーザーがファイルからスタイルを「学習」、「保存」、「記憶」、「抽出」するよう求めたとき
- ユーザーが既存のプリセットを管理したいとき（一覧、デフォルト設定、削除、名前変更）
- ステップ 0.5 でアクティブなプリセットが解決され、その適用ルールが必要なとき
- プリセットファイルを読み込む前に検証する必要があるとき

## 場所と検索順序

1. `~/.drawio-skill/styles/<name>.json` — ユーザープリセット（`git pull` で残る）。
2. `<this-skill-dir>/styles/built-in/<name>.json` — スキルに同梱の組み込み（`default`、`corporate`、`handdrawn`）。

ユーザープリセットは同名の組み込みを覆い隠します。

ユーザープリセットのみが `"default": true` を持てます。ユーザーが *"`<built-in-name>` を私のデフォルトに"* と言ったときは、まず組み込み JSON を `~/.drawio-skill/styles/<name>.json` にコピーし、その後コピー側で `default: true` を設定する — 同梱の組み込みには触れないでください。

**名前の正規化：** ファイルを書く、または検索する前に、ユーザー指定の名前を常に小文字にする（プリセットスキーマは小文字を強制；大文字の名前は検証に失敗します）。

## プリセットの適用

SKILL.md のステップ 0.5 がプリセットを特定した場合、この図表に対する組み込みのパレット、形状キーワード、エッジのデフォルト、フォントを完全に置き換えます — 組み込みのカラーテーブルから値を混ぜないでください。

**色の検索。** 形状が果たす各役割（service / database / queue / gateway / error / external / security）について、`preset.roles[role]` をスロット名に解決し、続いて `preset.palette[<slot>]` を `(fillColor, strokeColor)` ペアに解決する。`roles[role]` が未設定または解決されたスロットが `null` の場合、次のフォールバックの梯子に従う：

1. その役割の正準スロットを試す（`service→primary`、`database→success`、`queue→warning`、`gateway→accent`、`error→danger`、`external→neutral`、`security→secondary`）。
2. そのスロットも空であれば、プリセット内で最も埋まっている非 null スロットを選ぶ。
3. 組み込みのカラーテーブルに手を伸ばさないこと — プリセットが権威。

**ディシジョンとコンテナの形状**は `preset.roles` にない — 形状語彙（`preset.shapes.decision`、`preset.shapes.container`）は持つが、役割からスロットへのマッピングはない。色は次のように選ぶ：
- **ディシジョン**（ひし形）→ `preset.palette.warning` を使う（組み込み規約では正準の黄色スロット）。`warning` が空なら、上記のスロットフォールバック梯子を `warning` から開始して適用。
- **コンテナ**（スイムレーン）→ コンテナが表す階層/グループに対応するパレットスロットを使う（例：「Services」階層コンテナは `primary` を、"Data" 階層は `success` を使う）。階層シグナルが無ければ、デフォルトで `primary`。

**形状キーワード。** `preset.shapes[role]` を頂点スタイル文字列の**プレフィックス**として使う（`whiteSpace=wrap;html=1;...` の前）。例：database 役割の場合、`preset.shapes.database = "shape=cylinder3"` なら、頂点スタイルは `shape=cylinder3;whiteSpace=wrap;html=1;fillColor=...` で始まる。6 つの名前付き形状キーは `service`、`database`、`queue`、`decision`、`external`、`container`。`gateway`、`error`、`security` の役割は、プリセットがそれぞれの名前のキーを明示的に埋めない限り `preset.shapes.service` を再利用する。

**エッジ。** `preset.edges.style` を基本エッジスタイル文字列として使う。`preset.edges.arrow` を追加する。エッジごとのルーティングキー（`exitX/exitY/entryX/entryY/...`）は、SKILL.md の通常のルーティングルールにより引き続き追加される。2 つの形状間のフローが `preset.edges.dashedFor` のトークンに一致する場合（ユーザープロンプトがその語を使ったため、またはエッジの一端がその典型的な関係が「optional」である役割を果たすため）、エッジスタイルに `;dashed=1` を追加する。

**フォント。** `fontFamily=<preset.font.fontFamily>;fontSize=<preset.font.fontSize>` をすべての頂点スタイルに追加する。コンテナヘッダーとスイムレーンタイトルには、`preset.font.titleBold` が `true` のとき、さらに `fontSize=<preset.font.titleFontSize>;fontStyle=1` を追加する。

**エクストラ。**
- `preset.extras.sketch === true` → すべての頂点スタイルとすべてのエッジスタイルに `sketch=1` を追加。
- `preset.extras.globalStrokeWidth !== 1`（drawio デフォルトの 1 以外の任意の値、`0.5` を含む）→ すべての頂点スタイルとすべてのエッジスタイルに `strokeWidth=<n>` を追加。

**図表タイププリセットとの相互作用**（ERD / UML / シーケンス / ML / フローチャート）。図表タイププリセットは、ユーザープリセットが保持しなければならない構造的なスタイルキーワードを設定する（例：ERD テーブルは `shape=table;startSize=30;container=1;childLayout=tableLayout;...` に依存する）。ルールは：図表タイププリセットの構造的キーワードを保ち、その上にユーザープリセットの色 / フォント / エッジ / エクストラを重ねる。図表タイププリセットが色（`fillColor=#dae8fc` など）をハードコードしていて、それがユーザープリセットと競合する場合、ユーザープリセットの色が勝つ。例外：`fillColor=none` は構造的 — パレット色で置き換えないこと。

## 学習フロー

**トリガー：** "learn my style from `<path>` as `<name>`"、"save this as `<name>` style"、"remember this style as `<name>`"。

**ファイル拡張子によるディスパッチ：**
- `.drawio`、`.xml` → XML パス
- `.png`、`.jpg`、`.jpeg`、`.svg`（ラスタライズされたフラット画像）→ 画像パス

**ステップ：**

1. **抽出リファレンスをロード。** `references/style-extraction.md` をコンテキストに読み込む。
2. **抽出**は、リファレンスにある XML パスまたは画像パスの手順に従う。
3. **正規化と候補の構築。** ユーザー指定のプリセット名を小文字に変換。このフロー中のすべてのファイルパスにこの正規化された名前を使う。候補プリセット JSON を構築して `/tmp/drawio-preset-<name>.json` に書き込む（ここで `<name>` は既に正規化された名前）。まだ `~/.drawio-skill/styles/<name>.json` には保存**しない**。
4. **サンプルをレンダリング**する。`references/style-extraction.md` のサンプル図表スケルトンを、候補プリセットでパラメータ化して使う。メインワークフローと同じ `draw.io -x -f png -e -s 2 -o ./preset-<name>-sample.png /tmp/drawio-preset-<name>.drawio` コマンドを使って `./preset-<name>-sample.png` に PNG をエクスポート。
5. **ユーザーに表示：**
   - プリセットサマリーテーブル（パレット 16 進数値、役割ごとの形状、フォント、エッジスタイル、エクストラ）。
   - サンプル PNG パス（環境がサポートする場合は画像を埋め込む）。
   - プロベナンス行：`source.type`、`source.path`、`extracted_at`、`confidence`。
6. **承認を待つ：**
   - "save" / "looks good" → 候補を `~/.drawio-skill/styles/<name>.json` に書き込む。`~/.drawio-skill/styles/` が無ければ作成。tempfile とサンプル PNG を削除。
   - "change `<field>` to `<value>`" → メモリ上の候補を編集し、再レンダリングし、再度尋ねる。
   - "cancel" / "abort" / "no" → tempfile とサンプル PNG を削除；何も保存しない。

**エラー時の振る舞い：**

| 失敗 | 振る舞い |
|---|---|
| ソースパスが存在しない | 停止；パスが見つからないと報告。 |
| XML パースが失敗 | 停止；パースエラーを報告；ファイルを drawio デスクトップで開いて修復することを提案。 |
| 画像のビジョンが利用不可 | 停止；ビジョン対応モデルで再実行するか、`.drawio` ファイルを提供するようユーザーに伝える。 |
| 抽出で 0 個の頂点 / 形状 | 停止；保存を拒否。 |
| 抽出で 3 個未満の異なるカラーペア | 続行；`confidence: "low"`（画像）または `"medium"`（XML）でマーク；サマリーで警告。 |
| プリセット名が既存のユーザープリセットと衝突 | 尋ねる：上書きするか、別名を選ぶか。 |
| プリセット名が組み込みプリセットと衝突 | ユーザーディレクトリに保存（組み込みを覆い隠す）；一度警告。 |
| サンプルレンダリングが失敗 | サマリーは表示；「サンプルをレンダリングできなかった — OK で保存する」と注記。ブロックしない。 |

## 管理操作

すべての操作は自然言語 — スラッシュコマンドなし。

*すべての `<name>`、`<a>`、`<b>` 引数に、ファイル操作の前に名前正規化（小文字化）を適用する。*

| ユーザーが言う | エージェントの動作 |
|---|---|
| "list my styles"、"what styles do I have"、"show me my style presets" | `~/.drawio-skill/styles/` と `<this-skill-dir>/styles/built-in/` を読む。テーブルで出力：`name`、`location`（user/built-in）、`source.type`、`confidence`、`default` フラグ。ユーザープリセットに覆い隠された組み込みはその旨マーク。 |
| "show my `<name>` style"、"what's in `<name>`" | プリセット JSON を整形して出力 + 1 行サマリー（ソース、信頼度、デフォルトか否か）。 |
| "make `<name>` the default"、"set `<name>` as default" | `<name>` がユーザープリセットの場合：そのプリセットに `default: true` を設定し、`default` を持っていた他のユーザープリセットからはクリア；両ファイルを保存。`<name>` が組み込みの場合：まず `<this-skill-dir>/styles/built-in/<name>.json` を `~/.drawio-skill/styles/<name>.json` にコピーし、その後コピー側で `default: true` を設定する。同梱の組み込みは決して変更しない。 |
| "remove default"、"unset default" | `default: true` を持っているユーザープリセットからクリアする。 |
| "delete `<name>`"、"remove `<name>`" | まず確認。次に `rm ~/.drawio-skill/styles/<name>.json`。`<this-skill-dir>/styles/built-in/` 配下のファイルの削除は拒否 — 同名のユーザープリセットで覆い隠すことを提案。 |
| "rename `<a>` to `<b>`" | `mv ~/.drawio-skill/styles/<a>.json ~/.drawio-skill/styles/<b>.json`、その後内部の `name` フィールドを更新する。`<a>` が組み込みの場合は失敗（代わりにコピーしてから名前変更することを提案）。 |
| "learn my style from `<path>` as `<name>`" | 上記の Learn フローにディスパッチ。 |

## プリセットファイルの検証

任意のプリセットをロードする際（生成または管理のため）、軽量な構造チェックを行う：
- 必須のトップレベルフィールドが存在すること（`name`、`version`、`palette`、`roles`、`shapes`、`font`、`edges`）。
- `version === 1`。
- すべての埋められたパレットスロットが `fillColor` と `strokeColor` の両方を `#RRGGBB` として持つこと。
- `confidence` が存在する場合は ∈ {`"low"`、`"medium"`、`"high"`}。

検証に失敗した場合：
- **生成中：** ユーザーに警告し、この 1 つの図表については組み込み規約にフォールバック、ファイルは変更しない。
- **学習中：** 候補の保存を拒否し、どのフィールドが失敗したかを報告する。
