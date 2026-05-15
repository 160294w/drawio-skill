# 図表タイププリセット

ユーザーが特定の図表タイプを要求した場合、形状、スタイル、レイアウト規約には以下の該当プリセットを適用します。これらのプリセットは**構造的**なスタイルキーワード（例: ERD の `shape=table;childLayout=tableLayout`）を設定します；ユーザースタイルプリセット（`references/style-presets.md` を参照）はその上に色/フォント/エッジ/エクストラを重ねます。

このファイルを読むのは：
- ユーザーがこれらの図表タイプの 1 つを指名したとき（ERD、UML クラス、シーケンス、アーキテクチャ、ML/DL モデル、フローチャート）
- 新しい図表のために形状語彙やレイアウト方向を選択しているとき

## ERD（エンティティ・リレーションシップ図）

| 要素 | スタイル | メモ |
|---------|-------|-------|
| Table | `shape=table;startSize=30;container=1;collapsible=1;childLayout=tableLayout;fixedRows=1;rowLines=0;fontStyle=1;strokeColor=#6c8ebf;fillColor=#dae8fc;` | 各テーブルはコンテナ |
| Row（カラム） | `shape=tableRow;horizontal=0;startSize=0;swimlaneHead=0;swimlaneBody=0;fillColor=none;collapsible=0;dropTarget=0;points=[[0,0.5],[1,0.5]];portConstraint=eastwest;fontSize=12;` | テーブルの子、`parent=tableId` |
| PK カラム | 行に対して太字テキスト：`fontStyle=1` | `PK` プレフィックスや鍵アイコンでマーク |
| FK リレーションシップ | 破線エッジ：`dashed=1;endArrow=ERmandOne;startArrow=ERmandOne;` | ER 表記の矢印を使う |
| レイアウト | TB、テーブル間 300px の間隔 | 関連するテーブルを縦方向にグループ化 |

## UML クラス図

| 要素 | スタイル | メモ |
|---------|-------|-------|
| クラスボックス | `swimlane;fontStyle=1;align=center;startSize=26;html=1;` | 3 セクション：タイトル / 属性 / メソッド |
| セパレータ | `line;strokeWidth=1;fillColor=none;align=left;verticalAlign=middle;spacingTop=-1;spacingLeft=3;spacingRight=10;rotatable=0;labelPosition=left;points=[];portConstraint=eastwest;` | セクション間 |
| 継承 | `endArrow=block;endFill=0;` | 中空の三角形矢印 |
| 実装 | `endArrow=block;endFill=0;dashed=1;` | 破線 + 中空の三角形 |
| コンポジション | `endArrow=diamondThin;endFill=1;` | 塗りつぶしのひし形 |
| 集約 | `endArrow=diamondThin;endFill=0;` | 中空のひし形 |
| レイアウト | TB、クラス間 250px | インターフェイスを実装の上に |

## シーケンス図

| 要素 | スタイル | メモ |
|---------|-------|-------|
| Actor/Object | `shape=umlLifeline;perimeter=lifelinePerimeter;whiteSpace=wrap;html=1;container=1;collapsible=0;recursiveResize=0;outlineConnect=0;portConstraint=eastwest;` | 破線の縦線を持つライフライン |
| 同期メッセージ | `html=1;verticalAlign=bottom;endArrow=block;` | 実線、塗りつぶし矢印 |
| 非同期メッセージ | `html=1;verticalAlign=bottom;endArrow=open;dashed=1;` | 破線、オープン矢印 |
| 戻りメッセージ | `html=1;verticalAlign=bottom;endArrow=open;dashed=1;strokeColor=#999999;` | グレーの破線 |
| アクティベーションボックス | ライフライン上の `shape=umlFrame;whiteSpace=wrap;` | ライフライン上の細長い矩形 |
| レイアウト | LR、ライフラインを 200px 間隔で配置 | 時間は上から下へ流れる |

## アーキテクチャ図

| 要素 | スタイル | メモ |
|---------|-------|-------|
| レイヤー/階層 | `swimlane;startSize=30;` | グルーピングのコンテナ：クライアント / API / サービス / データ |
| サービス | `rounded=1;whiteSpace=wrap;html=1;` + 階層カラー | 階層ごとにカラーパレットを使用 |
| データベース | `shape=cylinder3;whiteSpace=wrap;html=1;` | 緑のパレット |
| キュー/バス | `rounded=1;whiteSpace=wrap;html=1;fillColor=#fff2cc;strokeColor=#d6b656;` | 黄 — ハブパターンのために中央に配置 |
| ゲートウェイ/LB | `shape=mxgraph.aws4.resourceIcon;` または オレンジ付き `rounded=1;` | オレンジパレット |
| 外部 | `rounded=1;dashed=1;fillColor=#f5f5f5;strokeColor=#666666;` | 外部システムには破線境界 |
| レイアウト | 階層数に応じて TB または LR；≥4 階層 → TB | ハブノードは中央に |

## ML / 深層学習モデル図

ニューラルネットワークアーキテクチャ図 — NeurIPS、ICML、ICLR を対象とした論文に最適。

| 要素 | スタイル | メモ |
|---------|-------|-------|
| レイヤーブロック | `rounded=1;whiteSpace=wrap;html=1;` + タイプ別カラー | メインのビルディングブロック |
| 入力/出力 | `fillColor=#d5e8d4;strokeColor=#82b366;` | 緑 |
| Conv / Pooling | `fillColor=#dae8fc;strokeColor=#6c8ebf;` | 青 |
| Attention / Transformer | `fillColor=#e1d5e7;strokeColor=#9673a6;` | 紫 |
| RNN / LSTM / GRU | `fillColor=#fff2cc;strokeColor=#d6b656;` | 黄 |
| FC / Linear | `fillColor=#ffe6cc;strokeColor=#d79b00;` | オレンジ |
| Loss / Activation | `fillColor=#f8cecc;strokeColor=#b85450;` | 赤/ピンク |
| スキップ接続 | `dashed=1;endArrow=block;curved=1;` | 破線の曲線矢印 |
| テンソル形状ラベル | 副次ラベルとして形状アノテーションを追加：`value="Conv2D&#xa;(B, 64, 32, 32)"` | 複数行には `&#xa;` を使う |
| レイアウト | TB（データは上→下に流れる）、レイヤー間 150px | エンコーダ/デコーダはスイムレーンとしてグループ化 |

**テンソル形状の規約：** 各レイヤーに対し、入力/出力テンソル次元を `(B, C, H, W)` または `(B, T, D)` フォーマットで注釈する。次元はラベルの 2 行目に `&#xa;` を使って配置する。

## フローチャート（拡張版）

| 要素 | スタイル | メモ |
|---------|-------|-------|
| 開始/終了 | `ellipse;whiteSpace=wrap;html=1;fillColor=#d5e8d4;strokeColor=#82b366;` | 緑の楕円 |
| プロセス | `rounded=0;whiteSpace=wrap;html=1;fillColor=#dae8fc;strokeColor=#6c8ebf;` | 青の矩形 |
| ディシジョン | `rhombus;whiteSpace=wrap;html=1;fillColor=#fff2cc;strokeColor=#d6b656;` | 黄のひし形 |
| I/O | `shape=parallelogram;perimeter=parallelogramPerimeter;whiteSpace=wrap;html=1;fillColor=#ffe6cc;strokeColor=#d79b00;` | オレンジの平行四辺形 |
| サブプロセス | `rounded=0;whiteSpace=wrap;html=1;fillColor=#e1d5e7;strokeColor=#9673a6;` + 二重境界 | 紫 |
| Yes/No ラベル | ディシジョンエッジ上の `value="Yes"` / `value="No"` | 常にディシジョン分岐をラベル付け |
| レイアウト | TB、200px の垂直ギャップ | ディシジョンは LR に分岐し、中央に戻ってマージ |
