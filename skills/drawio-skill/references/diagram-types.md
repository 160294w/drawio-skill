# 図表タイププリセット

ユーザーが特定の図表タイプを要求したときは、形状、スタイル、レイアウトの規約として以下の該当プリセットを適用します。これらのプリセットは**構造的**なスタイルキーワード（例：ERD の `shape=table;childLayout=tableLayout`）を設定します。ユーザースタイルプリセット（`references/style-presets.md` を参照）はその上に色・フォント・エッジ・追加要素を重ねます。

このファイルを読むのは次のような場合です：
- ユーザーがこれらの図表タイプ（ERD、UML クラス、シーケンス、アーキテクチャ、ML/DL モデル、フローチャート）のいずれかを指定したとき
- 新しい図表のために形状の語彙やレイアウト方向を選ぼうとしているとき

## ERD（エンティティ・リレーションシップ図）

| 要素 | スタイル | メモ |
|---------|-------|-------|
| Table | `shape=table;startSize=30;container=1;collapsible=1;childLayout=tableLayout;fixedRows=1;rowLines=0;fontStyle=1;strokeColor=#6c8ebf;fillColor=#dae8fc;` | 各テーブルはコンテナ |
| Row（カラム） | `shape=tableRow;horizontal=0;startSize=0;swimlaneHead=0;swimlaneBody=0;fillColor=none;collapsible=0;dropTarget=0;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;fontSize=12;` | テーブルの子、`parent=tableId` |
| PK カラム | 行を太字にする：`fontStyle=1` | `PK` プレフィックスまたは鍵アイコンで識別 |
| FK リレーションシップ | 破線エッジ：`dashed=1;endArrow=ERmandOne;startArrow=ERmandOne;` | ER 表記の矢印を使う |
| レイアウト | TB、テーブル間隔 300px | 関連するテーブルを縦方向にまとめる |

## UML クラス図

| 要素 | スタイル | メモ |
|---------|-------|-------|
| クラスボックス | `swimlane;fontStyle=1;align=center;startSize=26;html=1;` | 3 つのセクション：タイトル / 属性 / メソッド |
| セパレータ | `line;strokeWidth=1;fillColor=none;align=left;verticalAlign=middle;spacingTop=-1;spacingLeft=3;spacingRight=10;rotatable=0;labelPosition=left;points=[];portConstraint=eastwest;` | セクション間の区切り |
| 継承 | `endArrow=block;endFill=0;` | 白抜きの三角形矢印 |
| 実装 | `endArrow=block;endFill=0;dashed=1;` | 破線 + 白抜きの三角形 |
| コンポジション | `endArrow=diamondThin;endFill=1;` | 塗りつぶしのひし形 |
| 集約 | `endArrow=diamondThin;endFill=0;` | 白抜きのひし形 |
| レイアウト | TB、クラス間隔 250px | インターフェイスを実装の上に配置 |

## シーケンス図

| 要素 | スタイル | メモ |
|---------|-------|-------|
| Actor/Object | `shape=umlLifeline;perimeter=lifelinePerimeter;whiteSpace=wrap;html=1;container=1;collapsible=0;recursiveResize=0;outlineConnect=0;portConstraint=eastwest;` | 縦の破線を持つライフライン |
| 同期メッセージ | `html=1;verticalAlign=bottom;endArrow=block;` | 実線、塗りつぶしの矢印 |
| 非同期メッセージ | `html=1;verticalAlign=bottom;endArrow=open;dashed=1;` | 破線、オープン矢印 |
| 戻りメッセージ | `html=1;verticalAlign=bottom;endArrow=open;dashed=1;strokeColor=#999999;` | グレーの破線 |
| アクティベーションボックス | ライフライン上に `shape=umlFrame;whiteSpace=wrap;` | ライフライン上に置く細長い矩形 |
| レイアウト | LR、ライフラインの間隔 200px | 時間は上から下へ流れる |

## アーキテクチャ図

| 要素 | スタイル | メモ |
|---------|-------|-------|
| レイヤー/階層 | `swimlane;startSize=30;` | グルーピング用コンテナ：クライアント / API / サービス / データ |
| サービス | `rounded=1;whiteSpace=wrap;html=1;` + 階層カラー | 階層ごとにカラーパレットを使い分ける |
| データベース | `shape=cylinder3;whiteSpace=wrap;html=1;` | 緑のパレット |
| キュー/バス | `rounded=1;whiteSpace=wrap;html=1;fillColor=#fff2cc;strokeColor=#d6b656;` | 黄 — ハブパターンとして中央に配置 |
| ゲートウェイ/LB | `shape=mxgraph.aws4.resourceIcon;` または `rounded=1;` にオレンジ系 | オレンジのパレット |
| 外部 | `rounded=1;dashed=1;fillColor=#f5f5f5;strokeColor=#666666;` | 外部システムは破線の枠線で表す |
| レイアウト | 階層数に応じて TB か LR を選ぶ。4 階層以上は TB | ハブノードは中央に置く |

## ML / 深層学習モデル図

ニューラルネットワークのアーキテクチャ図。NeurIPS、ICML、ICLR などを対象にした論文に最適です。

| 要素 | スタイル | メモ |
|---------|-------|-------|
| レイヤーブロック | `rounded=1;whiteSpace=wrap;html=1;` + 種別ごとの色 | 主要な構成ブロック |
| 入力/出力 | `fillColor=#d5e8d4;strokeColor=#82b366;` | 緑 |
| Conv / Pooling | `fillColor=#dae8fc;strokeColor=#6c8ebf;` | 青 |
| Attention / Transformer | `fillColor=#e1d5e7;strokeColor=#9673a6;` | 紫 |
| RNN / LSTM / GRU | `fillColor=#fff2cc;strokeColor=#d6b656;` | 黄 |
| FC / Linear | `fillColor=#ffe6cc;strokeColor=#d79b00;` | オレンジ |
| Loss / Activation | `fillColor=#f8cecc;strokeColor=#b85450;` | 赤/ピンク |
| スキップ接続 | `dashed=1;endArrow=block;curved=1;` | 破線の曲線矢印 |
| テンソル形状ラベル | 補足ラベルとして形状の注釈を追加：`value="Conv2D&#xa;(B, 64, 32, 32)"` | 複数行には `&#xa;` を使う |
| レイアウト | TB（データは上から下へ流れる）、レイヤー間隔 150px | エンコーダ/デコーダはスイムレーンでまとめる |

**テンソル形状の規約：** 各レイヤーに、入力/出力テンソルの次元を `(B, C, H, W)` または `(B, T, D)` の形式で注釈します。次元はラベルの 2 行目に `&#xa;` を使って配置します。

## フローチャート（拡張版）

| 要素 | スタイル | メモ |
|---------|-------|-------|
| 開始/終了 | `ellipse;whiteSpace=wrap;html=1;fillColor=#d5e8d4;strokeColor=#82b366;` | 緑の楕円 |
| プロセス | `rounded=0;whiteSpace=wrap;html=1;fillColor=#dae8fc;strokeColor=#6c8ebf;` | 青の矩形 |
| 判定 | `rhombus;whiteSpace=wrap;html=1;fillColor=#fff2cc;strokeColor=#d6b656;` | 黄のひし形 |
| I/O | `shape=parallelogram;perimeter=parallelogramPerimeter;whiteSpace=wrap;html=1;fillColor=#ffe6cc;strokeColor=#d79b00;` | オレンジの平行四辺形 |
| サブプロセス | `rounded=0;whiteSpace=wrap;html=1;fillColor=#e1d5e7;strokeColor=#9673a6;` + 二重枠 | 紫 |
| Yes/No ラベル | 判定のエッジに `value="Yes"` / `value="No"` を設定 | 判定の分岐には必ずラベルを付ける |
| レイアウト | TB、縦方向の間隔 200px | 判定は左右に分岐し、中央に戻して合流させる |
