# スタイル抽出 — エージェントリファレンス

ユーザーがスタイルの学習を依頼したとき（"learn my style from `<path>` as `<name>`"）や、抽出後にサンプルをレンダリングする必要があるときに、`SKILL.md` から必要に応じて読み込まれます。

## サンプル図表（承認用のレンダリング）

候補プリセットを抽出したら、その候補のパレット・形状・フォント・エッジを使ってこの 7 ノードのサンプルをレンダリングします。各役割はちょうど 1 回ずつ登場し、6 本のエッジ（うち 1 本は破線）で `edges.arrow`、`edges.style`、`edges.dashedFor` の挙動を確認します。

**レイアウト (TB):**
- 行 1 (y=40)：`gateway` を x=340 に中央配置
- 行 2 (y=180)：`security` (x=80)、`service` (x=340)、`queue` (x=600)
- 行 3 (y=340)：`database` (x=80)、`external` (x=340)、`error` (x=600)

**テンプレート — `{{...}}` プレースホルダーを候補プリセットの値で置き換えます。**

役割 `R` の頂点スタイルは次のように組み立てます：
`<shapes[R]>;whiteSpace=wrap;html=1;fillColor=<palette[roles[R]].fillColor>;strokeColor=<palette[roles[R]].strokeColor>;fontFamily=<font.fontFamily>;fontSize=<font.fontSize>`
- `extras.sketch=true` の場合は、すべての頂点スタイルとエッジスタイルに `;sketch=1` を追加します。
- `extras.globalStrokeWidth !== 1` の場合（drawio のデフォルトである 1 以外の値、`0.5` を含む）は、すべての頂点スタイルとエッジスタイルに `;strokeWidth=<n>` を追加します。

エッジスタイルは次のように組み立てます：
`<edges.style>;<edges.arrow>`
- エッジごとのルーティングキー（`exitX/entryX/...`）は、以下の XML に直接書き込まれています。
- エッジ 15 で `edges.dashedFor` の挙動を確認します：
  - `edges.dashedFor` が**空でない**場合は、その先頭の値をエッジの `value`（ラベル）に使い、エッジスタイルに `;dashed=1` を追加します。
  - `edges.dashedFor` が空 (`[]`) の場合は、ラベルに `cross-call` を使い、`;dashed=1` は**追加しません**。プリセットに破線の規約がないので、サンプルでそれを偽装してはいけません。

**プレースホルダーの展開（XML に埋め込むときに適用）：**
- `{{VSTYLE:<role>}}` は、上記の頂点スタイル式に `R = <role>` を当てはめた結果に展開します。結果はそのままの文字列として書き、URL エンコードはしません。
- `{{ESTYLE}}` は、上記のエッジスタイル式に展開します。
- `{{EDGE15_LABEL}}` と `{{EDGE15_DASH}}` は、上記のエッジ 15 のルールに従います。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<mxfile host="drawio" version="26.0.0">
  <diagram name="Preset Sample">
    <mxGraphModel>
      <root>
        <mxCell id="0" />
        <mxCell id="1" parent="0" />

        <!-- Row 1: gateway -->
        <mxCell id="2" value="Gateway" style="{{VSTYLE:gateway}}" vertex="1" parent="1">
          <mxGeometry x="340" y="40" width="160" height="60" as="geometry" />
        </mxCell>

        <!-- Row 2: security | service | queue -->
        <mxCell id="3" value="Auth" style="{{VSTYLE:security}}" vertex="1" parent="1">
          <mxGeometry x="80" y="180" width="160" height="60" as="geometry" />
        </mxCell>
        <mxCell id="4" value="Service" style="{{VSTYLE:service}}" vertex="1" parent="1">
          <mxGeometry x="340" y="180" width="160" height="60" as="geometry" />
        </mxCell>
        <mxCell id="5" value="Queue" style="{{VSTYLE:queue}}" vertex="1" parent="1">
          <mxGeometry x="600" y="180" width="160" height="60" as="geometry" />
        </mxCell>

        <!-- Row 3: database | external | error -->
        <mxCell id="6" value="Database" style="{{VSTYLE:database}}" vertex="1" parent="1">
          <mxGeometry x="80" y="340" width="160" height="70" as="geometry" />
        </mxCell>
        <mxCell id="7" value="External API" style="{{VSTYLE:external}}" vertex="1" parent="1">
          <mxGeometry x="340" y="340" width="160" height="60" as="geometry" />
        </mxCell>
        <mxCell id="8" value="Error Sink" style="{{VSTYLE:error}}" vertex="1" parent="1">
          <mxGeometry x="600" y="340" width="160" height="60" as="geometry" />
        </mxCell>

        <!-- Edges -->
        <mxCell id="10" value="" style="{{ESTYLE}};exitX=0.25;exitY=1;exitDx=0;exitDy=0;entryX=0.5;entryY=0;entryDx=0;entryDy=0" edge="1" parent="1" source="2" target="3">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>
        <mxCell id="11" value="" style="{{ESTYLE}};exitX=0.5;exitY=1;exitDx=0;exitDy=0;entryX=0.5;entryY=0;entryDx=0;entryDy=0" edge="1" parent="1" source="2" target="4">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>
        <mxCell id="12" value="" style="{{ESTYLE}};exitX=0.75;exitY=1;exitDx=0;exitDy=0;entryX=0.5;entryY=0;entryDx=0;entryDy=0" edge="1" parent="1" source="2" target="5">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>
        <mxCell id="13" value="" style="{{ESTYLE}};exitX=0.5;exitY=1;exitDx=0;exitDy=0;entryX=0.5;entryY=0;entryDx=0;entryDy=0" edge="1" parent="1" source="4" target="7">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>
        <mxCell id="14" value="" style="{{ESTYLE}};exitX=0;exitY=0.5;exitDx=0;exitDy=0;entryX=1;entryY=0.5;entryDx=0;entryDy=0" edge="1" parent="1" source="4" target="6">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>
        <mxCell id="15" value="{{EDGE15_LABEL}}" style="{{ESTYLE}}{{EDGE15_DASH}};exitX=1;exitY=0.5;exitDx=0;exitDy=0;entryX=0;entryY=0.5;entryDx=0;entryDy=0" edge="1" parent="1" source="4" target="8">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>

      </root>
    </mxGraphModel>
  </diagram>
</mxfile>
```

### サンプルのレンダリング手順

1. 埋め込んだ XML を `/tmp/drawio-preset-<name>.drawio` に書き込みます。
2. メインワークフローと同じ `draw.io -x -f png -e -s 2 -o <preset-name>-sample.png <tmp>.drawio` コマンドを実行します。
3. PNG を `./preset-<name>-sample.png`（ユーザーの作業ディレクトリ）として保存します。
4. ユーザーに次の内容を表示します：プリセットのサマリー表、PNG のパス、出所/信頼度の行。

### 承認ループ

- "save" / "looks good" → 候補を `~/.drawio-skill/styles/<name>.json` に書き込み、一時ファイルとサンプル PNG を削除します。
- "change <field> to <value>" → メモリ上の候補を編集し、再レンダリングして、もう一度承認を求めます。
- "cancel" → 一時ファイルとサンプル PNG を削除し、保存しません。

### サンプルのレンダリングに失敗した場合（draw.io CLI が無い / エクスポートエラー）

それでもサマリー表と出所の行は表示します。「サンプル PNG をレンダリングできませんでした（CLI が利用できません）。OK なら保存します」と注記し、フローを止めないでください。

## XML 抽出パス

入力：`.drawio` ファイルのパス。出力：候補プリセットの JSON。決定的な処理で、LLM の推論は使いません。

### 手順

1. **ファイルをパースします。** XML を読み込み、`style=` 属性を持つすべての `<mxCell>` を集めて、頂点（`vertex="1"`）とエッジ（`edge="1"`）に分けます。
2. **各 `style=` 文字列を `;` で分割してトークン化します。** 各要素は `key=value` か、単独のキーワード（例：`rhombus`、`ellipse`、`rounded=1`）のいずれかです。
3. **パレットを抽出します。** 各頂点から `(fillColor, strokeColor)` のペアを取り出します（どちらも持たない頂点はスキップ）。出現頻度をカウントし、上位 7 ペアまでを保持します。
4. **形状の語彙と役割のマッピングを抽出します。** 各頂点について、次の優先順位で形状クラスを決定します：
   `cylinder3 > ellipse > rhombus > swimlane > rounded=1 > rounded=0`。
   続いて、頂点の形状クラスと `value`（ラベル）属性から意味的な役割を推測します。**以下のルールを順に評価し、最初に一致したものを採用します。**
   - `cylinder3` → `database`
   - `rhombus` → `decision`
   - `swimlane` → `container`
   - `dashed=1` あり、かつ**グレー系の塗りつぶし**（R、G、B チャネルがすべて互いに ±16 以内、つまり無彩色に近い色）→ `external`
   - ラベルが `/queue|bus|kafka|rabbit/i` に一致 → `queue`
   - ラベルが `/gateway|api|lb|load/i` に一致 → `gateway`
   - ラベルが `/auth|login|jwt|oauth/i` に一致 → `security`
   - ラベルが `/error|fail|alert/i` に一致 → `error`
   - 上記のいずれにも当てはまらない → `service`

   **標準パレットスロットを持つ役割**（`service`、`database`、`queue`、`gateway`、`error`、`external`、`security`）については、最も頻度の高い `(role, color-pair)` の組み合わせを採用します。そのペアをその役割の標準パレットスロットに入れます：
   `service→primary、database→success、queue→warning、gateway→accent、error→danger、external→neutral、security→secondary`。
   `roles[role]` をそのスロット名に設定します。

   **判定（decision）とコンテナ（container）の形状は `roles[...]` のエントリを持ちません**。これらは `shapes.decision` と `shapes.container` にのみ記録されます。判定／コンテナ頂点で観察された色のペアはパレットに含めることはできます（空きスロットを埋められます）が、意味的な役割には紐付けません。

   役割スロットへのマッピングに割り当てられなかった色ペアは、頻度の高い順に残りの空きパレットスロットを埋めます。

   役割ごとに使われた形状クラス文字列を `shapes[role]` に記録します。形状キーは `service`、`database`、`queue`、`decision`、`external`、`container` の 6 つです。`gateway`、`error`、`security` の役割は `shapes.service` を引き継ぎ、独自の `shapes[...]` エントリは持ちません。例：`shapes.database = "shape=cylinder3"`。

5. **フォントを抽出します。** 全頂点を通した `fontFamily` と `fontSize` の最頻値を計算し、`font.fontFamily` と `font.fontSize` として出力します。各頂点の `fontStyle` も**作業用の変数**として追跡します（出力フィールドではありません。スキーマにはトップレベルの `font.fontStyle` がありません）。識別可能な頂点のサブセットが、より大きな `fontSize` と `fontStyle=1`（太字）を組み合わせて使っている場合、そのサブセットをタイトルとして扱い、`font.titleFontSize` をその最頻サイズに、`font.titleBold: true` に設定します。そうでない場合は、両方のタイトル関連フィールドを省略します。

6. **エッジのデフォルトを抽出します。** 最頻のエッジスタイル文字列を取り出しますが、カウントの前に以下のエッジ固有の座標キーを取り除きます：`entryX`、`entryY`、`exitX`、`exitY`、`entryDx`、`entryDy`、`exitDx`、`exitDy`。`endArrow`/`endFill` から得られる矢印スタイルは、別途 `edges.arrow` に記録します。
   `dashed=1` を持つエッジがあれば、その `value`（ラベル）属性を集めます。2 つ以上のエッジが共通のトークンを持っていれば（例：すべて "async" または "optional" とラベル付けされている）、そのトークンを `edges.dashedFor` に追加します。

7. **追加要素を抽出します。** 頂点またはエッジのいずれかに `sketch=1` が見られれば、`extras.sketch = true` とします。全頂点を通した `strokeWidth` の最頻値を `extras.globalStrokeWidth` に設定します（デフォルトは `1`）。

8. **出所情報を設定します。**
   ```json
   {
     "source": { "type": "xml", "path": "<input absolute path>", "extracted_at": "YYYY-MM-DD" },
     "confidence": "high"
   }
   ```

### XML 抽出のエッジケース

| 状況 | 挙動 |
|---|---|
| ソースに 3 種類未満の色ペアしかない | 埋まらないスロットは `null` のままにする。`confidence` を `"medium"` に下げる。サマリーでユーザーに警告する。 |
| ソースに 7 種類を超える色ペアがある | 頻度の上位 7 つを残し、サマリーで一部の色を採用しなかった旨を警告する。 |
| 非標準の `shape=` キーワード（例：`shape=mxgraph.aws4.*`） | ステップ 4 の優先順位リストに一致しないため、形状クラスとしては `rounded=0` に分類される。アイコンは保持できないが、色・ラベル・エッジスタイルは引き続き取り込める。役割の推測はラベルの正規表現ルールで引き続き実行する。サマリーで *"Non-standard shape library detected — iconography not preserved in preset (color and label captured)."* と注記する。 |
| 英語以外のラベル | ステップ 4 の英語キーワード正規表現はほぼ一致しないため、多くの頂点が `service` に倒れる。パレット・形状・フォント・エッジは正しく取り込める（ラベルのテキストには依存しない）。`confidence` は `"high"` のまま。サマリーで *"Role labels not in English — `service`/`database`/`decision`/`container`/`external` inferred from shape class; other roles not mapped."* と注記する。 |
| ファイルに `<mxCell vertex="1">` が 1 つも無い | 停止し、保存を拒否する。メッセージ：*"Nothing to learn from — source file has no shapes."* |

## 画像抽出パス

入力：PNG/JPG など、ビジョンが読み取れる画像形式のパス。出力：候補プリセットの JSON。推論ベースで、最高でも `confidence: "medium"` です。

**前提条件：** エージェントのビジョン機能が利用できること（メインワークフローのセルフチェックと同じ仕組み）。ビジョンが使えない場合は、処理を止めて次のように伝えます：
*"Image-based learning needs a vision-enabled model (Claude Sonnet or Opus). Re-run on such a model, or provide the `.drawio` source file instead."*

### 手順

1. **画像を読み込みます。** エージェントのビジョン入力を使います。メインワークフローのステップ 5 でエクスポートされた PNG を読むのと同じ仕組みです。

2. **目視でパレットを抽出します。** 形状の本体に現れる、それぞれ異なる塗りつぶし色の領域を識別します。

   各塗りつぶしについて：
   - `fillColor` — 各 RGB チャネルを 16 の倍数に丸めて量子化します。得られた HSL の明度が 0.75 未満であれば、0.85 に引き上げます（色相と彩度はそのまま、L=0.85 を設定し、HSL→RGB に戻す）。最終的に `#RRGGBB` として出力します。drawio 標準のパステルは L≈0.85〜0.96 の範囲に収まり、0.75 未満は「塗りつぶし色としては暗すぎる」とみなされるため、このステップでその範囲に持ち上げます。
   - `strokeColor` — 対応する枠線の色を読み取ります。読み取れない場合は、塗りつぶしから約 25% 暗くして導出します（HSL を一致させ、L を 0.25 下げる）。

   各 `(fillColor, strokeColor)` ペアは、次の判定順で名前付きスロットに割り当てます：

   1. **まずグレー判定。** 塗りつぶしの R、G、B チャネルがすべて互いに ±16 以内（XML パスのグレー系ルールと同じ定義）、または HSL 彩度が 0.20 未満の場合は `neutral` に分類します。この判定は色相に関係なく優先されます。
   2. **そうでなければ色相帯で判定。** 次の HSL 色相範囲を使います：
      - 180°〜260° → `primary`（青）
      - 80°〜170° → `success`（緑）
      - 45°〜65° → `warning`（黄）
      - 20°〜44° → `accent`（オレンジ）
      - 0°〜19° または 320°〜360° → `danger`（赤/ピンク）
      - 260°〜320° → `secondary`（紫）
   3. **どの帯にも当てはまらない**（65°〜80° または 170°〜180° のすき間）場合は、角度距離で最も近い帯に寄せます。

   **衝突ルール。** 2 つ以上の塗りつぶしが同じスロットに割り当てられた場合、画像内で占めるピクセル面積で降順に並べます。最大のものが標準スロットを取得し、残りは色相帯の角度距離で測った**最も近い空きスロット**に寄せます（まず両側の隣接帯、それからより遠くへ）。すべてのスロットが埋まっている場合は、超過分を捨ててサマリーで警告します。

3. **形状の語彙を抽出します。** 見えるすべての形状をシルエットで分類します：
   - 角丸の矩形 → `rounded=1`
   - 角ばった矩形 → `rounded=0`
   - 円 / 楕円 → `ellipse`
   - ひし形 → `rhombus`
   - 円柱（上下が湾曲した矩形）→ `shape=cylinder3`
   - タイトル付きコンテナ（ヘッダーバー + 子要素のネスト）→ `swimlane;startSize=30`
   - 破線枠の矩形 → `rounded=1;dashed=1`

   役割の割り当ては、**XML パスのステップ 4 と同じラベルテキスト + 形状ルール**を使います。表示されたラベルはビジョンで読み取ります。

4. **フォントを抽出します。** ベストエフォートで判定します：
   - 明らかにセリフ体 → `fontFamily: "Georgia"`
   - 明らかに等幅 → `fontFamily: "Courier New"`
   - それ以外 → `fontFamily: "Helvetica"`

   見た目のサイズ感で：
   - 小 → `fontSize: 11`
   - 中 → `fontSize: 12`
   - 大 → `fontSize: 14`

   タイトル／コンテナヘッダーが明らかに大きい、または太字の場合は、それに合わせて `titleFontSize` を設定し、`titleBold: true` にします。

5. **エッジのデフォルトを抽出します。**
   - 直角の直交矢印 → `edges.style = "edgeStyle=orthogonalEdgeStyle;rounded=1;orthogonalLoop=1;jettySize=auto;html=1"` を使います。
   - 曲線の矢印 → `edges.style` に `;curved=1` を追加します。
   - 塗りつぶしの三角形の矢じり → `edges.arrow = "endArrow=classic;endFill=1"` を使います。
   - 開いた V 字の矢じり → `edges.arrow = "endArrow=open;endFill=0"` を使います。
   - "optional"、"async"、"fallback"、"secondary" などのラベル付近にある破線矢印 → そのラベルのトークンを `edges.dashedFor` に追加します。

6. **追加要素を抽出します。**
   - 明らかに手描き／粗いスケッチ風（波打つストローク、ムラのある塗りつぶし）→ `extras.sketch = true`。
   - 線が太い（通常の 1.5 倍以上であることが明らか）→ `extras.globalStrokeWidth = 2`。
   - それ以外はデフォルト：`extras = { "sketch": false, "globalStrokeWidth": 1 }`。

7. **出所情報と信頼度を設定します。**
   ```json
   {
     "source": { "type": "image", "path": "<input absolute path>", "extracted_at": "YYYY-MM-DD" },
     "confidence": "medium"
   }
   ```
   信頼度の調整：
   - 識別可能な形状が 3 つ未満 → `confidence: "low"`。
   - 画像パスは基本的に `"medium"` のままです。`"high"` に上げてよいのは厳密に検証可能なシグナルがある場合だけです：元画像が drawio 自体からエクスポートされた（drawio 標準の装飾、グリッド、または見える drawio のウォーターマーク）、**かつ**パレットの 7 スロットすべてが埋まっている、**かつ** 7 つの役割すべてにラベルが付いていること。こうすることで、推論ベース（画像）とパースベース（XML）の出所の違いをきちんと区別できます。

### 画像抽出のエッジケース

| 状況 | 挙動 |
|---|---|
| ビジョンが利用不可 | 上記のとおり停止する — 推測でのフォールバックはしない。 |
| 画像で識別可能な形状が 3 つ未満 | 続行する。`confidence: "low"` を設定し、サマリーで「プリセットはおおまかな近似である」とユーザーに明示的に警告する。 |
| 画像に表示ラベルが無い | 役割の割り当ては形状クラスのみに頼る：円柱 → `database`、ひし形 → `decision`、スイムレーン → `container`、グレー塗りつぶしの破線枠矩形 → `external`、それ以外 → `service`。パレット・フォント・エッジは引き続き取り込める。サマリーで *"No labels readable — semantic roles beyond shape-class not inferred."* と注記する。 |
| 2 つのパレットスロットが同じ色相ファミリーに入る | 頻度の高い方を標準スロットに残し、もう一方は隣の空きスロットに寄せる（ステップ 2 のルール）。 |
| 画像に 7 種類を超える塗りつぶしがある | ステップ 2 の衝突ルールに従い、面積の大きい 7 つを残す。サマリーで一部の色を採用しなかったことを警告する。 |
