# トラブルシューティング — よくある間違い

出力（レンダリング、エクスポート、レイアウト、エッジ）に違和感があるときや、CLI の呼び出しが失敗したときに参照してください。ほとんどの項目は 1 行で修正方法を示しています。

| 間違い | 修正方法 |
|---------|-----|
| `id="0"` と `id="1"` のルートセルが欠落 | 常に `<root>` の先頭に両方を含める |
| 形状が接続されていない | エッジの `source` と `target` は既存の形状の `id` と一致させる必要がある |
| 自己閉じのエッジ `mxCell`（`<mxCell ... edge="1" />`） | 子要素 `<mxGeometry relative="1" as="geometry" />` を含む展開形式を使う。自己閉じのエッジはレンダリングされない |
| XML コメント内の `--` | XML 仕様で不正 — 単一のハイフンに変えるか、表現を書き換える |
| `value` 内の特殊文字 | XML エンティティを使う：`&amp;` `&lt;` `&gt;` `&quot;` |
| ラベルテキスト内にリテラルの `\n` | `value` 属性内での改行には `&#xa;` を使う |
| 形状が重なり合う | 複雑度に応じて間隔を調整（200〜350px）。ルーティング用の通り道を確保する |
| エッジが形状を貫通する | ウェイポイントを追加、入出力点を分散、または間隔を広げる |
| 矢印の先端が曲がり部分と重なる | ターゲットの直前のセグメントは 20px 以上必要 — 間隔を広げるかウェイポイントを追加する |
| 改善ループが終わらない | 5 ラウンドを過ぎたら、draw.io デスクトップで .drawio を開いて微調整するようユーザーに提案する |
| macOS でエクスポートコマンドが見つからない | フルパス `/Applications/draw.io.app/Contents/MacOS/draw.io` を試す |
| Linux：ヘッドレスで出力が空またはエラーになる | コマンドの先頭に `xvfb-run -a` を付ける |
| Linux：`--no-sandbox` が入力ファイルより前にある（ファイル名として解釈される） | `--no-sandbox` をコマンドの末尾に移動する（drawio-desktop#249、#1056） |
| Linux：`Failed to get 'appData' path` / `Home directory not accessible` | drawio を呼ぶ前に `export HOME=/tmp` を実行する（drawio-desktop#127） |
| Linux サーバー：segfault / EGL / MESA の `failed to load driver` エラー | `--disable-gpu` を追加する（GPU が使えないときに Chromium の GL 初期化を抑制） |
| PDF エクスポートが失敗する | Chromium が利用可能か確認する（draw.io デスクトップに同梱されている） |
| CLI エクスポート時の背景色がおかしい | 既知の CLI バグ。`--transparent` フラグを追加するか、スタイルで背景を指定する |
| ビジョンがドラフト PNG に対し 400 "Could not process image" を返す | `-e` なしでプレビューを再エクスポートする（issue #8）。根本原因は `-e` PNG の IEND チャンクの切り詰めであり、`zTXt` チャンク自体ではないが、プレビューで `-e` をスキップするのが最もシンプルな解決策 |
| 最終 `-e` PNG が画像ビューアやビジョン API で開けない | `python3 <this-skill-dir>/scripts/repair_png.py <path>` を実行する。draw.io CLI は `-e` PNG の IEND を 8 バイト切り詰めて出力する。SVG/PDF には影響なし |
