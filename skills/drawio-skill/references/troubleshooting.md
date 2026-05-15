# トラブルシューティング — よくある間違い

出力（レンダリング、エクスポート、レイアウト、エッジ）に何かおかしいところがあるとき、または CLI の呼び出しが失敗したときに読んでください。ほとんどの行は 1 行の修正方法を持っています。

| 間違い | 修正方法 |
|---------|-----|
| `id="0"` と `id="1"` のルートセルが欠落 | 常に `<root>` の先頭に両方含める |
| 形状が接続されていない | エッジの `source` と `target` は既存の形状の `id` 値と一致しなければならない |
| 自己閉じエッジ `mxCell`（`<mxCell ... edge="1" />`） | 子要素 `<mxGeometry relative="1" as="geometry" />` を含む展開形式を使う — 自己閉じエッジはレンダリングされない |
| XML コメント内の `--` | XML 仕様で違反 — 単一ハイフンを使うか言い回しを変える |
| `value` 内の特殊文字 | XML エンティティを使う：`&amp;` `&lt;` `&gt;` `&quot;` |
| ラベルテキスト内のリテラル `\n` | `value` 属性内の改行には `&#xa;` を使う |
| 重なり合う形状 | 複雑度に応じてスペーシングをスケール（200–350px）；ルーティングコリドーを残す |
| エッジが形状を貫通する | ウェイポイントを追加、エントリー/エグジット点を分散、またはスペーシングを増やす |
| 矢印先端が曲がりと重なる | ターゲット前の最後のエッジセグメントは ≥20px でなければならない — スペーシングを増やすかウェイポイントを追加 |
| イテレーションループが終わらない | 5 ラウンド後、ユーザーに draw.io デスクトップで .drawio を開いて微調整するよう提案 |
| macOS でエクスポートコマンドが見つからない | フルパス `/Applications/draw.io.app/Contents/MacOS/draw.io` を試す |
| Linux: ヘッドレスで出力が空白/エラー | コマンドの前に `xvfb-run -a` を付ける |
| Linux: `--no-sandbox` が入力ファイルの前にある（ファイル名としてパースされる） | `--no-sandbox` をコマンドの最後に移動（drawio-desktop#249、#1056） |
| Linux: `Failed to get 'appData' path` / `Home directory not accessible` | drawio 呼び出し前に `export HOME=/tmp`（drawio-desktop#127） |
| Linux サーバー: segfault / EGL / MESA `failed to load driver` エラー | `--disable-gpu` を追加（GPU が利用できないときに Chromium GL 初期化を抑制） |
| PDF エクスポート失敗 | Chromium が利用可能であることを確認（draw.io デスクトップにバンドルされている） |
| CLI エクスポートで背景色が誤り | 既知の CLI バグ；`--transparent` フラグを追加するかスタイル経由で背景を設定 |
| ビジョンがドラフト PNG に対し 400 "Could not process image" を返す | `-e` 無しでプレビューを再エクスポート（issue #8）。根本原因は `-e` PNG の IEND チャンク切り詰めであって `zTXt` チャンク自体ではない — ただしプレビューでは `-e` をスキップするのが最もシンプルな修正 |
| 最終 `-e` PNG が画像ビューア / ビジョン API で開けない | `python3 <this-skill-dir>/scripts/repair_png.py <path>` を実行。draw.io CLI は `-e` PNG で IEND が 8 バイト切り詰められたものを出す。SVG/PDF は影響なし |
