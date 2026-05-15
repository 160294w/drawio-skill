# スタイルプリセット

[中国語](STYLE_PRESETS_CN.md)

スタイルプリセットを使うと、図表全体でビジュアルスタイルを取り込んで再利用できます。プリセットがアクティブなとき、組み込みのカラーパレット、形状語彙、フォント、エッジのデフォルトを置き換えます。

## 組み込みプリセット

| 名前 | 説明 |
|------|-------------|
| `default` | 組み込みの慣例に合わせたクリーンな青/緑/黄のパレット |
| `corporate` | ビジネスプレゼンテーション向けの落ち着いたプロフェッショナルなパレット |
| `handdrawn` | カジュアル、またはホワイトボードスタイルの図表向けのスケッチ風ストローク |

## 図表にプリセットを適用する

```
Draw a microservices architecture using my "corporate" style
```

または、すべての将来の図表で自動的に使うデフォルトを設定する：

```
Make "corporate" my default style
```

## ファイルからあなたのスタイルを学習する

スキルを任意の `.drawio` ファイルやフラット画像に指し示す：

```
Learn my style from ~/diagrams/brand.drawio as "mybrand"
Learn my style from ~/diagrams/screenshot.png as "mybrand"
```

スキルが色、形状、フォント、エッジスタイルを抽出し、サンプル図表をレンダリングしてプレビューを表示し、あなたが承認してから `~/.drawio-skill/styles/mybrand.json` に保存します。

## プリセットの管理

| 言うこと | 起こること |
|---|---|
| "list my styles" | すべてのユーザーおよび組み込みプリセットをテーブルで表示 |
| "show my `<name>` style" | プリセットの JSON を整形して出力 |
| "make `<name>` the default" | すべての図表のアクティブなデフォルトに設定 |
| "remove default" | デフォルトをクリアする（組み込みの慣例に戻る） |
| "delete `<name>`" | ユーザープリセットを削除する（確認を求める） |
| "rename `<a>` to `<b>`" | ユーザープリセットの名前を変更する |
