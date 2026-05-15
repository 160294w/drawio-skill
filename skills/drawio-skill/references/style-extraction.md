# スタイル抽出 — エージェントリファレンス

ユーザーがスタイルの学習を要求したとき（"learn my style from `<path>` as `<name>`"）、または抽出後にサンプルをレンダリングする必要があるときに、`SKILL.md` によりオンデマンドでロードされます。

## サンプル図表（承認レンダリング用）

候補プリセットを抽出した後、候補のパレット/形状/フォント/エッジを使って、この 7 ノードのサンプルをレンダリングする。各役割はちょうど一度だけ現れ、6 個のエッジ（1 個は破線）が `edges.arrow`、`edges.style`、`edges.dashedFor` を発揮する。

**レイアウト (TB):**
- 行 1 (y=40): `gateway` を x=340 で中央配置
- 行 2 (y=180): `security` (x=80)、`service` (x=340)、`queue` (x=600)
- 行 3 (y=340): `database` (x=80)、`external` (x=340)、`error` (x=600)

**テンプレート — `{{...}}` プレースホルダーを候補プリセットから置換する。**

役割 `R` の頂点スタイルは次のように構築する：
`<shapes[R]>;whiteSpace=wrap;html=1;fillColor=<palette[roles[R]].fillColor>;strokeColor=<palette[roles[R]].strokeColor>;fontFamily=<font.fontFamily>;fontSize=<font.fontSize>`
- `extras.sketch=true` の場合、すべての頂点スタイルおよびすべてのエッジスタイルに `;sketch=1` を追加。
- `extras.globalStrokeWidth !== 1`（drawio デフォルトの 1 以外の任意の値、`0.5` を含む）の場合、すべての頂点スタイルおよびすべてのエッジスタイルに `;strokeWidth=<n>` を追加。

エッジスタイルは次のように構築する：
`<edges.style>;<edges.arrow>`
- エッジごとのルーティングキー（`exitX/entryX/...`）は以下にリテラルとして追加されている。
- エッジ 15 は `edges.dashedFor` を発揮する：
  - `edges.dashedFor` が**空でない**場合、その最初のエントリをエッジの `value`（ラベル）として使用し、エッジスタイルに `;dashed=1` を追加。
  - `edges.dashedFor` が空 (`[]`) の場合、ラベル `cross-call` を使用し、`;dashed=1` を追加**しない** — プリセットに破線規約が無いため、サンプルはそれを偽装してはならない。

**プレースホルダー展開（XML に埋め込むときに適用）：**
- `{{VSTYLE:<role>}}` は `R = <role>` を伴う上記の頂点スタイル式に展開される。結果をリテラル文字列として書く；URL エンコードしない。
- `{{ESTYLE}}` は上記のエッジスタイル式に展開される。
- `{{EDGE15_LABEL}}` と `{{EDGE15_DASH}}` は上記の Edge-15 ルールに従う。

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

### サンプルのレンダリング

1. 埋め込まれた XML を `/tmp/drawio-preset-<name>.drawio` に書き込む。
2. メインワークフローと同じ `draw.io -x -f png -e -s 2 -o <preset-name>-sample.png <tmp>.drawio` コマンドを実行する。
3. PNG を `./preset-<name>-sample.png`（ユーザーの作業ディレクトリ）として保存。
4. ユーザーに表示：プリセットサマリーテーブル + PNG パス + プロベナンス/信頼度の行。

### 承認ループ

- "save" / "looks good" → 候補を `~/.drawio-skill/styles/<name>.json` に書き込む；tempfile とサンプル PNG を削除。
- "change <field> to <value>" → メモリ上の候補を編集；再レンダリング；再度尋ねる。
- "cancel" → tempfile とサンプル PNG を削除；保存しない。

### サンプルレンダリングが失敗した場合（draw.io CLI 欠落 / エクスポートエラー）

それでもサマリーテーブルとプロベナンス行は表示する。注記：*"Could not render sample PNG (CLI unavailable). Save anyway on your OK."* ブロックしない。

## XML 抽出パス

入力：`.drawio` ファイルパス。出力：候補プリセット JSON。決定論的、LLM 推論なし。

### ステップ

1. **ファイルをパースする。** XML を読み、`style=` 属性を持つすべての `<mxCell>` を収集し、頂点（`vertex="1"`）とエッジ（`edge="1"`）に分割する。
2. **各 `style=` 文字列を `;` でトークン化。** 各要素は `key=value` または裸のキーワード（例：`rhombus`、`ellipse`、`rounded=1`）のいずれか。
3. **パレットを抽出。** 各頂点について、`(fillColor, strokeColor)` ペアを取得（どちらも持たない頂点はスキップ）。頻度をカウント。トップ ≤7 ペアを保持。
4. **形状語彙 + 役割マッピングを抽出。** 各頂点について、優先順位で形状クラスを決定する：
   `cylinder3 > ellipse > rhombus > swimlane > rounded=1 > rounded=0`。
   その後、頂点の形状クラスと `value`（ラベル）属性から意味的役割を推測する。**以下のルールを順に評価；最初に一致したものが勝つ。**
   - `cylinder3` → `database`
   - `rhombus` → `decision`
   - `swimlane` → `container`
   - `dashed=1` が存在 + **グレー系の塗りつぶし**（R、G、B チャネルがすべて互いに ±16 以内、つまりアクロマティックに近い 16 進数）→ `external`
   - ラベルが `/queue|bus|kafka|rabbit/i` に一致 → `queue`
   - ラベルが `/gateway|api|lb|load/i` に一致 → `gateway`
   - ラベルが `/auth|login|jwt|oauth/i` に一致 → `security`
   - ラベルが `/error|fail|alert/i` に一致 → `error`
   - その他すべて → `service`

   **正準パレットスロットを持つ各役割** — `service`、`database`、`queue`、`gateway`、`error`、`external`、`security` — について、最も頻度の高い `(role, color-pair)` マッピングが勝つ。そのペアはその役割の正準パレットスロットに入る：
   `service→primary、database→success、queue→warning、gateway→accent、error→danger、external→neutral、security→secondary`。
   `roles[role]` をそのスロット名に設定する。

   **ディシジョンとコンテナの形状は `roles[...]` エントリを取得しない** — `shapes.decision` と `shapes.container` にのみ記録される。ディシジョン/コンテナ頂点で観察されたカラーペアは引き続きパレットに参加する（残りのスロットを埋められる）が、意味的役割には結び付かない。

   残りのカラーペア（役割スロットマッピングで主張されないもの）は、頻度降順で残りの空きパレットスロットを埋める。

   役割ごとに使われた形状クラス文字列を `shapes[role]` に記録する。6 つの名前付き形状キーは `service`、`database`、`queue`、`decision`、`external`、`container` — `gateway`、`error`、`security` の役割は `shapes.service` を継承し、自分の `shapes[...]` エントリは取得しない。例：`shapes.database = "shape=cylinder3"`。

5. **フォントを抽出。** 頂点全体で `fontFamily` と `fontSize` の最頻値を計算する；`font.fontFamily` と `font.fontSize` として出力。各頂点の `fontStyle` も**作業変数**として追跡する（出力フィールドではない — スキーマにはトップレベルの `font.fontStyle` がない）。識別可能な頂点のサブセットがより大きな `fontSize` と `fontStyle=1`（太字）の組み合わせを使う場合、そのサブセットをタイトルとして扱う：`font.titleFontSize` をその最頻サイズに、`font.titleBold: true` に設定。そうでなければ両タイトルフィールドを省略。

6. **エッジのデフォルトを抽出。** 最頻のエッジスタイル文字列を取るが、カウント前に以下のエッジごとの座標キーを除去する：`entryX`、`entryY`、`exitX`、`exitY`、`entryDx`、`entryDy`、`exitDx`、`exitDy`。`endArrow`/`endFill` から矢印スタイルを `edges.arrow` に別途記録。
   `dashed=1` を持つエッジがあれば、その `value`（ラベル）属性を収集する。≥2 個が共通のトークンを共有する場合（例：すべて "async" または "optional" とラベル付けされている）、そのトークンを `edges.dashedFor` に追加。

7. **エクストラを抽出。** 任意の頂点またはエッジに `sketch=1` が見られる → `extras.sketch = true`。頂点全体の最頻 `strokeWidth` → `extras.globalStrokeWidth`（デフォルト `1`）。

8. **プロベナンスを設定。**
   ```json
   {
     "source": { "type": "xml", "path": "<input absolute path>", "extracted_at": "YYYY-MM-DD" },
     "confidence": "high"
   }
   ```

### XML エッジケース

| 状況 | 振る舞い |
|---|---|
| ソースが 3 個未満の異なるカラーペアを持つ | 埋まらないスロットを `null` のままにする。`confidence` を `"medium"` に下げる。サマリーでユーザーに警告。 |
| ソースが 7 個より多いカラーペアを持つ | 頻度のトップ 7 を保持。サマリーでいくつかの色が落とされたと警告。 |
| 非標準の `shape=` キーワード（例：`shape=mxgraph.aws4.*`） | これらはステップ 4 の優先順位ラダーに一致しないため、頂点は形状クラス目的では `rounded=0` にフォールスルーする。アイコノグラフィーは失われる；色、ラベル、エッジスタイルは引き続き捕捉される。役割推測はラベルの正規表現ルール経由で引き続き実行される。サマリーで注記：*"Non-standard shape library detected — iconography not preserved in preset (color and label captured)."* |
| 英語以外のラベル | ステップ 4 の英語キーワード正規表現はほとんど一致しない；ほとんどの頂点が `service` に潰れる。パレット/形状/フォント/エッジは正しく捕捉される（ラベルテキストに依存しない）。`confidence` は `"high"` のまま。サマリーで注記：*"Role labels not in English — `service`/`database`/`decision`/`container`/`external` inferred from shape class; other roles not mapped."* |
| ファイルに `<mxCell vertex="1">` がまったく無い | 停止。保存を拒否。メッセージ：*"Nothing to learn from — source file has no shapes."* |

## 画像抽出パス

入力：PNG/JPG（または任意のビジョン読取り可能画像フォーマット）のパス。出力：候補プリセット JSON。推論ベース；`confidence: "medium"` がベスト。

**前提条件：** エージェントのビジョン機能が利用可能であること（メインワークフローのセルフチェックが使うのと同じメカニズム）。ビジョンが利用不可の場合、停止してユーザーに伝える：
*"Image-based learning needs a vision-enabled model (Claude Sonnet or Opus). Re-run on such a model, or provide the `.drawio` source file instead."*

### ステップ

1. **画像を読む。** エージェントのビジョン入力を使う — メインワークフローのステップ 5 がセルフチェック中にエクスポートされた PNG を読むのと同じパス。

2. **目視で検査してパレットを抽出。** 形状本体上の異なる塗りつぶし色領域を識別する。

   それぞれの異なる塗りつぶしについて：
   - `fillColor` — 各 RGB チャネルを 16 の倍数に最近接で量子化。結果の HSL の明度が 0.75 未満なら、0.85 に上げる（色相と彩度を保持；L=0.85 を設定；HSL→RGB ラウンドトリップ）。`#RRGGBB` として出力。Drawio 標準のパステルは L≈0.85–0.96 を占める；0.75 未満は「塗りつぶし色としては暗すぎる」と読み取られ、このステップはそれをその範囲に持ち上げる。
   - `strokeColor` — 対応するボーダーを読み取る。読み取れない場合、塗りつぶしから ~25% 暗くして導出（HSL を一致させ、L を 0.25 落とす）。

   この決定順序を使って、各 `(fillColor, strokeColor)` ペアを名前付きスロットにマップする：

   1. **最初にグレーをチェック。** 塗りつぶしの R、G、B チャネルがすべて互いに ±16 以内（XML パスのグレー系ルールと同じ定義）、または HSL 彩度 < 0.20 の場合、`neutral` として分類。このチェックは色相角度に関係なく勝つ。
   2. **そうでなければ色相帯。** これらの明示的な HSL 色相範囲を使う：
      - 180°–260° → `primary`（青）
      - 80°–170° → `success`（緑）
      - 45°–65° → `warning`（黄）
      - 20°–44° → `accent`（オレンジ）
      - 0°–19° または 320°–360° → `danger`（赤/ピンク）
      - 260°–320° → `secondary`（紫）
   3. **どの帯にもマッチしない**（65°–80° または 170°–180° のギャップ領域）→ 角度距離で最も近い帯にこぼれる。

   **衝突ルール。** ≥2 個の異なる塗りつぶしが同じスロットに着地した場合、画像内のカバーピクセル面積で並べる（降順）。最大のものが正準スロットを保持。残りの塗りつぶしは、色相帯の角度距離で測った**最も近い空きスロット**にこぼれる — まず両側の隣接帯、その後より遠くへ。すべてのスロットが既に埋まっている場合、余分なものを落とし、サマリーで警告。

3. **形状語彙を抽出。** 可視のすべての形状をシルエットで分類する：
   - 角丸矩形 → `rounded=1`
   - 鋭角矩形 → `rounded=0`
   - 円 / 楕円 → `ellipse`
   - ひし形 → `rhombus`
   - 円柱（上下が湾曲した矩形）→ `shape=cylinder3`
   - タイトル付きコンテナ（ヘッダーバー + ネストした子要素）→ `swimlane;startSize=30`
   - 破線ボーダー矩形 → `rounded=1;dashed=1`

   役割の割り当ては、**XML パスのステップ 4 と同じラベルテキスト + 形状ルール**を使う。可視ラベルはビジョン経由で読まれる。

4. **フォントを抽出。** ベストエフォート。識別可能なカテゴリ：
   - 明らかなセリフ → `fontFamily: "Georgia"`
   - 明らかな等幅 → `fontFamily: "Courier New"`
   - その他 → `fontFamily: "Helvetica"`

   見た目の相対的なサイズで：
   - 小 → `fontSize: 11`
   - 中 → `fontSize: 12`
   - 大 → `fontSize: 14`

   タイトル/コンテナヘッダーが明らかに大きいか太字 → それに応じて `titleFontSize` を設定、`titleBold: true`。

5. **エッジのデフォルトを抽出。**
   - 直角の直交矢印 → `edges.style = "edgeStyle=orthogonalEdgeStyle;rounded=1;orthogonalLoop=1;jettySize=auto;html=1"`。
   - 曲線矢印 → `edges.style` に `;curved=1` を追加。
   - 塗りつぶしの三角形の矢印先端 → `edges.arrow = "endArrow=classic;endFill=1"`。
   - オープン V 字の矢印先端 → `edges.arrow = "endArrow=open;endFill=0"`。
   - "optional"、"async"、"fallback"、"secondary" などのラベルの近くの破線矢印 → それらのラベルトークンを `edges.dashedFor` に追加。

6. **エクストラを抽出。**
   - 目に見えて手描き / 粗い / スケッチ風（波打つストローク、不均一な塗りつぶし）→ `extras.sketch = true`。
   - 太いストローク（明らかに通常の >1.5×）→ `extras.globalStrokeWidth = 2`。
   - そうでなければデフォルト：`extras = { "sketch": false, "globalStrokeWidth": 1 }`。

7. **プロベナンスと信頼度を設定。**
   ```json
   {
     "source": { "type": "image", "path": "<input absolute path>", "extracted_at": "YYYY-MM-DD" },
     "confidence": "medium"
   }
   ```
   調整：
   - 識別可能な形状が 3 個未満 → `confidence: "low"`。
   - 画像パスはデフォルトで `"medium"` のまま。`"high"` への唯一のパスは厳密に検証可能なシグナル：ソース画像が drawio 自体からエクスポートされたもの（認識可能な drawio デフォルトクロム、グリッド、または可視の drawio ウォーターマーク）、**かつ** 7 つすべてのパレットスロットが埋まっている、**かつ** 7 つすべての役割がラベル付けされている。これは推論ベース（画像）とパースベース（XML）のプロベナンスの意味的ギャップを保持する。

### 画像エッジケース

| 状況 | 振る舞い |
|---|---|
| ビジョンが利用不可 | 上記の通り停止 — 推測にフォールバックしない。 |
| 画像に識別可能な形状が 3 個未満 | 続行；`confidence: "low"` でマーク；サマリーでプリセットが緩い近似であることをユーザーに明示的に警告。 |
| 画像に可視ラベルが無い | 役割の割り当ては形状クラスのみに潰れる：円柱 → `database`、ひし形 → `decision`、スイムレーン → `container`、グレー塗りつぶしの破線ボーダー矩形 → `external`、その他すべて → `service`。パレット/フォント/エッジは引き続き捕捉。サマリーで注記：*"No labels readable — semantic roles beyond shape-class not inferred."* |
| 2 つのパレットスロットが同じ色相ファミリーに着地 | より頻度の高いものを正準スロットに保持；他方は隣接する空きスロットにこぼれる（ステップ 2 のルール）。 |
| 画像が 7 個より多い異なる塗りつぶしを持つ | ステップ 2 の衝突ルールにより、面積が最大の 7 つを保持。サマリーでいくつかの色が落とされたと警告。 |
