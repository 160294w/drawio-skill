# スタイルプリセット

[中国語](STYLE_PRESETS_CN.md)

スタイルプリセットを使うと、ビジュアルスタイルを取り込んで複数の図表で再利用できます。プリセットが有効なときは、組み込みのカラーパレット、形状の語彙、フォント、エッジのデフォルトをすべてプリセットの値で置き換えます。

## 組み込みプリセット

| 名前 | 説明 |
|------|-------------|
| `default` | 組み込みの規約に沿った、すっきりとした青/緑/黄のパレット |
| `corporate` | ビジネスプレゼンテーション向けの、落ち着いたプロフェッショナルなパレット |
| `handdrawn` | カジュアルな図表やホワイトボード風の図表に合うスケッチ調のストローク |

## 図表にプリセットを適用する

```
Draw a microservices architecture using my "corporate" style
```

または、これ以降のすべての図表で自動的に使われるデフォルトを設定します：

```
Make "corporate" my default style
```

## ファイルからスタイルを学習させる

任意の `.drawio` ファイルや画像をスキルに指定します：

```
Learn my style from ~/diagrams/brand.drawio as "mybrand"
Learn my style from ~/diagrams/screenshot.png as "mybrand"
```

スキルが色、形状、フォント、エッジスタイルを抽出し、サンプル図表をレンダリングしてプレビューを表示します。承認するとはじめて `~/.drawio-skill/styles/mybrand.json` に保存されます。

## プリセットの管理

| 入力例 | 動作 |
|---|---|
| "list my styles" | ユーザー作成と組み込みのプリセットをすべて表で表示 |
| "show my `<name>` style" | プリセットの JSON を整形して出力 |
| "make `<name>` the default" | すべての図表で使われるデフォルトに設定 |
| "remove default" | デフォルト指定を解除（組み込みの規約に戻る） |
| "delete `<name>`" | ユーザープリセットを削除（確認あり） |
| "rename `<a>` to `<b>`" | ユーザープリセットの名前を変更 |
