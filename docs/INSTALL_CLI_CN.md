# 前提条件 —— draw.io デスクトップ版

[English](INSTALL_CLI.md)

図表をエクスポートするには、draw.io デスクトップ版のインストールが必要です：

## macOS

```bash
# 推奨 — Homebrew
brew install --cask drawio

# インストールの確認
drawio --version
```

## Windows

以下からインストーラーをダウンロードします：https://github.com/jgraph/drawio-desktop/releases

```powershell
# インストールの確認
"C:\Program Files\draw.io\draw.io.exe" --version
```

## Linux

以下から `.deb` または `.rpm` パッケージをダウンロードします：https://github.com/jgraph/drawio-desktop/releases

```bash
# ヘッドレスエクスポート（ディスプレイがない Linux サーバーでは必須）
sudo apt install xvfb  # Debian/Ubuntu
xvfb-run -a drawio --version
```

| プラットフォーム | 追加手順 |
|------|----------|
| **macOS** | Homebrew でのインストール後に追加手順は不要 |
| **Windows** | PATH に含まれない場合はフルパスで指定 |
| **Linux** | ヘッドレスエクスポート時はコマンドの先頭に `xvfb-run -a` を付ける |
