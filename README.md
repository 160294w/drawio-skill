# drawio-skill — テキストからプロフェッショナルな図表へ

[中文文档](README_CN.md) | [オンラインドキュメント](https://agents365-ai.github.io/drawio-skill/)

<p align="center">
  <img src="assets/workflow.png" width="900" alt="ワークフロー">
</p>

自然言語の記述を `.drawio` XML に変換し、draw.io デスクトップのネイティブ CLI を使って PNG / SVG / PDF / JPG にエクスポートするスキルです。6 種類の図表プリセット（ERD、UML クラス図、シーケンス図、アーキテクチャ図、ML/DL、フローチャート）、セルフチェックと自動修正（2 ラウンド）、繰り返し改善できるフィードバックループ（5 ラウンド）、サンプルファイルや画像から取り込めるスタイルプリセットを備えています。

Claude Code、Cursor、Copilot、OpenClaw、Codex、Hermes など、[Agent Skills](https://agentskills.io) 形式に対応する任意のエージェントで動作します。

## ドキュメント

| ドキュメント | 内容 |
|---|---|
| [docs/COMPARISON.md](docs/COMPARISON.md) | ネイティブエージェントや他の draw.io スキル/ツールとの比較表と、主な利点のまとめ |
| [docs/INSTALL_CLI.md](docs/INSTALL_CLI.md) | macOS / Windows / Linux 向けの draw.io デスクトップ CLI のインストール手順 |
| [docs/INSTALL_SKILL.md](docs/INSTALL_SKILL.md) | プラグインマーケットプレイス、手動クローン、アップデートコマンド |
| [docs/USAGE.md](docs/USAGE.md) | 自然言語プロンプト、マイクロサービス例、トポロジーデモ（スター / レイヤード / リング） |
| [docs/STYLE_PRESETS.md](docs/STYLE_PRESETS.md) | 組み込みプリセット、「ファイルからスタイルを学習させる」フロー、プリセット管理コマンド |
| [skills/drawio-skill/SKILL.md](skills/drawio-skill/SKILL.md) | エージェントが読み込むワークフローガイド |

## 何ができるか

| 機能 | 説明 |
|---|---|
| `.drawio` XML の生成 | 自然言語の記述から生成 |
| 複数フォーマットへのエクスポート | draw.io デスクトップのネイティブ CLI 経由で PNG / SVG / PDF / JPG に出力 |
| 6 種類の図表プリセット | ERD、UML クラス図、シーケンス図、アーキテクチャ図、ML/深層学習、フローチャート |
| 繰り返し改善 | セルフチェックと自動修正（最大 2 ラウンド）の後、承認まで 5 ラウンドのフィードバックループ |
| スタイルプリセット | `.drawio` ファイルや画像からビジュアルスタイルを取り込み、名前を付けて保存、必要に応じて再適用 |
| 自動起動 | 複雑なシステムの説明に図表が役立つ場面で自動的に起動 |

## 対応している図表タイプ

| カテゴリ | 例 | 主な特徴 |
|---|---|---|
| アーキテクチャ | マイクロサービス、クラウド（AWS/GCP/Azure）、ネットワークトポロジ、デプロイ | 階層別スイムレーン、ハブ中央配置戦略 |
| ML / 深層学習 | Transformer、CNN、LSTM、GRU | テンソル形状の注釈、レイヤー種別ごとの色分け |
| フローチャート | 業務プロセス、ワークフロー、決定木、状態遷移 | 意味付きの形状（平行四辺形の I/O、ひし形の判定） |
| UML | クラス図、シーケンス図 | 継承 / コンポジション / 集約の矢印、ライフラインとアクティベーションボックス |
| データ | ER 図、データフロー図（DFD） | テーブルコンテナ、PK/FK 表記 |
| その他 | 組織図、マインドマップ、ワイヤーフレーム | — |

## クイックスタート

2 ステップで使えます。まず draw.io CLI をインストールし（[docs/INSTALL_CLI.md](docs/INSTALL_CLI.md) を参照）、次にスキルをホストに配置します（[docs/INSTALL_SKILL.md](docs/INSTALL_SKILL.md) を参照）。あとは作りたいものを記述するだけです：

```
Create a microservices e-commerce architecture with API Gateway, auth/user/order/product/payment services,
Kafka message queue, notification service, and separate databases for each service
```

スキルがレイアウトを設計し、`.drawio` XML を生成し、選択したフォーマットにエクスポートし、セルフチェックを行ったうえで、繰り返し改善できるようサポートします。

## コミュニティ

ヘルプ、質問、最新情報はこちらから：

- **Discord:** https://discord.gg/79JF5Atuk
- **WeChat:** 下の QR コードをスキャン

<p align="center">
  <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/qrcode/agents365ai_wechat_1.png" width="200" alt="WeChat コミュニティグループ">
</p>

## サポート

このスキルがお役に立ったら、作者へのサポートをご検討ください：

<table>
  <tr>
    <td align="center">
      <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/qrcode/wechat-pay.png" width="180" alt="WeChat Pay">
      <br>
      <b>WeChat Pay</b>
    </td>
    <td align="center">
      <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/qrcode/alipay.png" width="180" alt="Alipay">
      <br>
      <b>Alipay</b>
    </td>
    <td align="center">
      <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/qrcode/buymeacoffee.png" width="180" alt="Buy Me a Coffee">
      <br>
      <b>Buy Me a Coffee</b>
    </td>
    <td align="center">
      <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/awarding/award.gif" width="180" alt="報酬を贈る">
      <br>
      <b>報酬を贈る</b>
    </td>
  </tr>
</table>

## 作者

**Agents365-ai**

- Bilibili: https://space.bilibili.com/441831884
- GitHub: https://github.com/Agents365-ai

## ライセンス

MIT
