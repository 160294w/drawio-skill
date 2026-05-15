# 前提条件 — draw.io デスクトップ

[中国語](INSTALL_CLI_CN.md)

図表をエクスポートするには、draw.io デスクトップアプリのインストールが必要です：

## macOS

```bash
# 推奨 — Homebrew
brew install --cask drawio

# 動作確認
drawio --version
```

## Windows

以下からダウンロードしてインストール：https://github.com/jgraph/drawio-desktop/releases

```powershell
# 動作確認
"C:\Program Files\draw.io\draw.io.exe" --version
```

## Linux

以下から `.deb` または `.rpm` をダウンロード：https://github.com/jgraph/drawio-desktop/releases

```bash
# ヘッドレスエクスポート（ディスプレイのない Linux サーバーで必要）
sudo apt install xvfb  # Debian/Ubuntu
xvfb-run -a drawio --version
```

| プラットフォーム | 追加ステップ |
|----------|------------|
| **macOS** | Homebrew でのインストール後に追加手順は不要 |
| **Windows** | PATH に含まれない場合はフルパスで指定 |
| **Linux** | ヘッドレスエクスポート時はコマンドを `xvfb-run -a` で包む |
