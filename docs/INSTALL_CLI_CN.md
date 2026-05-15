# 前提依存 —— draw.io デスクトップ版

[English](INSTALL_CLI.md)

図表エクスポート用に draw.io デスクトップ版のインストールが必要：

## macOS

```bash
# 推奨方法 — Homebrew
brew install --cask drawio

# インストール検証
drawio --version
```

## Windows

以下のアドレスからインストーラーをダウンロード：https://github.com/jgraph/drawio-desktop/releases

```powershell
# インストール検証
"C:\Program Files\draw.io\draw.io.exe" --version
```

## Linux

以下のアドレスから `.deb` または `.rpm` パッケージをダウンロード：https://github.com/jgraph/drawio-desktop/releases

```bash
# ヘッドレスエクスポート（Linux サーバーでディスプレイ無し時に必須）
sudo apt install xvfb  # Debian/Ubuntu
xvfb-run -a drawio --version
```

| プラットフォーム | 追加ステップ |
|------|----------|
| **macOS** | Homebrew インストール後、追加操作不要 |
| **Windows** | PATH にない場合はフルパスを使用 |
| **Linux** | ヘッドレスエクスポート時はコマンドの前に `xvfb-run -a` を付与 |
