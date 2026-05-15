# スタイルプリセット — 学習・適用・管理

**スタイルプリセット**とは、ユーザーのビジュアル設定（パレット、形状の語彙、フォント、エッジスタイル）をまとめた、名前付きの JSON ファイルです。プリセットが有効なときは、SKILL.md の色・形状・エッジの組み込み規約をすべてプリセットの内容で置き換えます。

このファイルを読むのは次のような場合です：
- ユーザーがファイルからスタイルを「学習」「保存」「記憶」「抽出」してほしいと依頼したとき
- ユーザーが既存のプリセットを管理したいとき（一覧、デフォルト設定、削除、名前変更）
- ステップ 0.5 で有効なプリセットが解決され、その適用ルールが必要になったとき
- プリセットファイルを読み込む前に検証が必要なとき

## 配置場所と検索順序

1. `~/.drawio-skill/styles/<name>.json` — ユーザープリセット（`git pull` でも残る）。
2. `<this-skill-dir>/styles/built-in/<name>.json` — スキルに同梱の組み込みプリセット（`default`、`corporate`、`handdrawn`）。

ユーザープリセットは、同名の組み込みプリセットを覆い隠します。

`"default": true` を持てるのはユーザープリセットだけです。ユーザーが *"`<built-in-name>` をデフォルトにして"* と言ったときは、まず組み込みの JSON を `~/.drawio-skill/styles/<name>.json` にコピーし、コピー側で `default: true` を設定してください。同梱の組み込みプリセットには触れないでください。

**名前の正規化：** ファイルを書き込む前、または検索する前に、ユーザー指定の名前を必ず小文字に変換してください（プリセットスキーマは小文字を強制するため、大文字を含む名前は検証に失敗します）。

## プリセットの適用

SKILL.md のステップ 0.5 でプリセットが特定された場合は、そのプリセットによってこの図表の組み込みパレット、形状キーワード、エッジのデフォルト、フォントをすべて置き換えます。組み込みのカラーテーブルから値を混ぜないでください。

**色の解決。** 形状が担う各役割（service / database / queue / gateway / error / external / security）について、`preset.roles[role]` をスロット名に解決し、続いて `preset.palette[<slot>]` を `(fillColor, strokeColor)` ペアに解決します。`roles[role]` が未設定、または解決されたスロットが `null` の場合は、以下のフォールバック手順を順に試します：

1. その役割の標準スロットを試す（`service→primary`、`database→success`、`queue→warning`、`gateway→accent`、`error→danger`、`external→neutral`、`security→secondary`）。
2. そのスロットも空であれば、プリセット内で最も埋まっている非 null スロットを選ぶ。
3. 組み込みのカラーテーブルには戻らない — プリセットの値が優先される。

**判定とコンテナの形状**は `preset.roles` には含まれません。形状の語彙（`preset.shapes.decision`、`preset.shapes.container`）は持ちますが、役割からスロットへのマッピングはありません。色は次のように決めます：
- **判定**（ひし形）→ `preset.palette.warning` を使う（組み込み規約では標準の黄色スロット）。`warning` が空の場合は、上記のフォールバック手順を `warning` から始めて適用する。
- **コンテナ**（スイムレーン）→ コンテナが表す階層/グループに対応するパレットスロットを使う（例：「Services」階層のコンテナは `primary`、"Data" 階層は `success` を使う）。階層の手がかりがなければ、デフォルトで `primary` を使う。

**形状キーワード。** `preset.shapes[role]` を頂点スタイル文字列の**先頭**に置きます（`whiteSpace=wrap;html=1;...` より前）。例：database 役割の場合、`preset.shapes.database = "shape=cylinder3"` なら、頂点スタイルは `shape=cylinder3;whiteSpace=wrap;html=1;fillColor=...` で始まります。6 つの形状キーは `service`、`database`、`queue`、`decision`、`external`、`container` です。`gateway`、`error`、`security` の役割は、プリセットが明示的にキーを定義していない限り `preset.shapes.service` を使い回します。

**エッジ。** `preset.edges.style` を基本のエッジスタイル文字列として使い、`preset.edges.arrow` を後ろに連結します。エッジごとのルーティングキー（`exitX/exitY/entryX/entryY/...`）は、SKILL.md の通常のルーティングルールに従ってこれまで通り追加されます。2 つの形状間のフローが `preset.edges.dashedFor` のトークンに一致する場合（ユーザープロンプトがその語を使った、またはエッジの片端の役割が「optional」な関係を典型的に持つ場合）は、エッジスタイルに `;dashed=1` を追加してください。

**フォント。** すべての頂点スタイルに `fontFamily=<preset.font.fontFamily>;fontSize=<preset.font.fontSize>` を追加します。コンテナのヘッダーやスイムレーンのタイトルには、`preset.font.titleBold` が `true` のとき、さらに `fontSize=<preset.font.titleFontSize>;fontStyle=1` を追加します。

**追加要素。**
- `preset.extras.sketch === true` のとき → すべての頂点スタイルとエッジスタイルに `sketch=1` を追加する。
- `preset.extras.globalStrokeWidth !== 1` のとき（drawio のデフォルトである 1 以外の値、`0.5` を含む）→ すべての頂点スタイルとエッジスタイルに `strokeWidth=<n>` を追加する。

**図表タイププリセットとの組み合わせ**（ERD / UML / シーケンス / ML / フローチャート）。図表タイププリセットは、ユーザープリセットが保持しなければならない構造的なスタイルキーワードを設定します（例：ERD テーブルは `shape=table;startSize=30;container=1;childLayout=tableLayout;...` に依存します）。ルールは次のとおりです：図表タイププリセットの構造的なキーワードはそのまま残し、その上にユーザープリセットの色・フォント・エッジ・追加要素を重ねます。図表タイププリセットが色（`fillColor=#dae8fc` など）をハードコードしていてユーザープリセットと競合する場合は、ユーザープリセットの色を優先します。例外：`fillColor=none` は構造的な指定なので、パレットの色で置き換えてはいけません。

## 学習フロー

**トリガー：** "learn my style from `<path>` as `<name>`"、"save this as `<name>` style"、"remember this style as `<name>`" など。

**ファイル拡張子による振り分け：**
- `.drawio`、`.xml` → XML パス
- `.png`、`.jpg`、`.jpeg`、`.svg`（ラスタライズされた画像）→ 画像パス

**手順：**

1. **抽出用リファレンスを読み込む。** `references/style-extraction.md` をコンテキストに読み込みます。
2. **抽出**は、リファレンスにある XML パスまたは画像パスの手順に従って行います。
3. **正規化と候補の構築。** ユーザー指定のプリセット名を小文字に変換します。このフロー中のすべてのファイルパスで正規化後の名前を使ってください。候補となるプリセット JSON を構築して `/tmp/drawio-preset-<name>.json` に書き込みます（ここでの `<name>` はすでに正規化済み）。この段階では `~/.drawio-skill/styles/<name>.json` には**まだ保存しません**。
4. **サンプルをレンダリング**します。`references/style-extraction.md` にあるサンプル図表のひな型に、候補プリセットの値を当てはめます。メインワークフローと同じ `draw.io -x -f png -e -s 2 -o ./preset-<name>-sample.png /tmp/drawio-preset-<name>.drawio` コマンドで、PNG を `./preset-<name>-sample.png` に書き出します。
5. **ユーザーに表示する内容：**
   - プリセットのサマリー表（パレットの 16 進値、役割ごとの形状、フォント、エッジスタイル、追加要素）。
   - サンプル PNG のパス（環境が対応していれば画像を埋め込む）。
   - 出所情報の行：`source.type`、`source.path`、`extracted_at`、`confidence`。
6. **承認を待つ：**
   - "save" / "looks good" → 候補を `~/.drawio-skill/styles/<name>.json` に書き込む。`~/.drawio-skill/styles/` が無ければ作成する。一時ファイルとサンプル PNG を削除する。
   - "change `<field>` to `<value>`" → メモリ上の候補を編集し、再レンダリングして、もう一度承認を求める。
   - "cancel" / "abort" / "no" → 一時ファイルとサンプル PNG を削除し、何も保存しない。

**エラー時の挙動：**

| 失敗の種類 | 挙動 |
|---|---|
| ソースパスが存在しない | 停止し、パスが見つからない旨を報告する。 |
| XML のパースに失敗 | 停止し、パースエラーを報告する。drawio デスクトップでファイルを開いて修復するよう提案する。 |
| 画像用のビジョンが利用不可 | 停止し、ビジョン対応のモデルで再実行するか、`.drawio` ファイルを渡すようユーザーに伝える。 |
| 抽出された頂点/形状が 0 個 | 停止し、保存を拒否する。 |
| 抽出されたカラーペアが 3 種類未満 | 続行する。`confidence: "low"`（画像）または `"medium"`（XML）でマークし、サマリーで警告する。 |
| プリセット名が既存のユーザープリセットと衝突 | 上書きするか別名にするかをユーザーに尋ねる。 |
| プリセット名が組み込みプリセットと衝突 | ユーザーディレクトリに保存し（組み込みを覆い隠す）、一度だけ警告する。 |
| サンプルのレンダリングに失敗 | サマリーは表示する。「サンプルをレンダリングできませんでした — OK なら保存します」と注記し、フローは止めない。 |

## 管理操作

すべての操作は自然言語で受け付けます — スラッシュコマンドはありません。

*すべての `<name>`、`<a>`、`<b>` 引数に対し、ファイル操作の前に名前の正規化（小文字化）を適用してください。*

| ユーザーの発話 | エージェントの動作 |
|---|---|
| "list my styles"、"what styles do I have"、"show me my style presets" | `~/.drawio-skill/styles/` と `<this-skill-dir>/styles/built-in/` を読み込み、`name`、`location`（user/built-in）、`source.type`、`confidence`、`default` フラグを表で出力する。ユーザープリセットに覆い隠されている組み込みはその旨を示す。 |
| "show my `<name>` style"、"what's in `<name>`" | プリセット JSON を整形して出力し、1 行のサマリー（ソース、信頼度、デフォルトかどうか）を添える。 |
| "make `<name>` the default"、"set `<name>` as default" | `<name>` がユーザープリセットなら：そのプリセットに `default: true` を設定し、他のユーザープリセットの `default` をクリアして、両方のファイルを保存する。`<name>` が組み込みなら：まず `<this-skill-dir>/styles/built-in/<name>.json` を `~/.drawio-skill/styles/<name>.json` にコピーし、コピー側で `default: true` を設定する。同梱の組み込みは決して変更しない。 |
| "remove default"、"unset default" | `default: true` が設定されているユーザープリセットからフラグをクリアする。 |
| "delete `<name>`"、"remove `<name>`" | まず確認を取り、その後 `rm ~/.drawio-skill/styles/<name>.json` を実行する。`<this-skill-dir>/styles/built-in/` 配下のファイルの削除は拒否し、同名のユーザープリセットで覆い隠すよう提案する。 |
| "rename `<a>` to `<b>`" | `mv ~/.drawio-skill/styles/<a>.json ~/.drawio-skill/styles/<b>.json` を実行し、ファイル内の `name` フィールドを更新する。`<a>` が組み込みの場合は失敗とし、代わりにコピーしてから名前変更するよう提案する。 |
| "learn my style from `<path>` as `<name>`" | 上記の学習フローに振り分ける。 |

## プリセットファイルの検証

プリセットを読み込むとき（生成・管理いずれの場合も）、軽量な構造チェックを行います：
- 必須のトップレベルフィールドがすべて存在すること（`name`、`version`、`palette`、`roles`、`shapes`、`font`、`edges`）。
- `version === 1`。
- 値が入っているパレットスロットには、`fillColor` と `strokeColor` の両方が `#RRGGBB` 形式で含まれていること。
- `confidence` が存在する場合は、その値が {`"low"`、`"medium"`、`"high"`} のいずれかであること。

検証に失敗した場合：
- **生成中：** ユーザーに警告し、その図表については組み込み規約にフォールバックする。ファイル自体は変更しない。
- **学習中：** 候補の保存を拒否し、どのフィールドが検証に失敗したかを報告する。
