# drawio-skill — テキストからプロフェッショナルな図表へ

[中文文档](README_CN.md) | [オンラインドキュメント](https://agents365-ai.github.io/drawio-skill/)

<p align="center">
  <img src="assets/workflow.png" width="900" alt="ワークフロー">
</p>

自然言語による記述を `.drawio` XML に変換し、draw.io デスクトップのネイティブ CLI 経由で PNG / SVG / PDF / JPG にエクスポートするスキル。6 種類の図表プリセット（ERD、UML クラス図、シーケンス図、アーキテクチャ図、ML/DL、フローチャート）、セルフチェック + 自動修正（2 ラウンド）、イテレーティブなフィードバックループ（5 ラウンド）、サンプルファイルや画像から取り込めるスタイルプリセットを備えています。

Claude Code、Cursor、Copilot、OpenClaw、Codex、Hermes、そして [Agent Skills](https://agentskills.io) フォーマットに対応する任意のエージェントで動作します。

## ドキュメント

| ドキュメント | 内容 |
|---|---|
| [docs/COMPARISON.md](docs/COMPARISON.md) | ネイティブエージェントや他の draw.io スキル/ツールとの並列比較表、主要アドバンテージのサマリー |
| [docs/INSTALL_CLI.md](docs/INSTALL_CLI.md) | macOS / Windows / Linux 向けの draw.io デスクトップ CLI インストールレシピ |
| [docs/INSTALL_SKILL.md](docs/INSTALL_SKILL.md) | プラグインマーケットプレイス、手動クローン、アップデートコマンド |
| [docs/USAGE.md](docs/USAGE.md) | 自然言語プロンプト、マイクロサービスのウォークスルー、トポロジーデモ（スター / レイヤード / リング） |
| [docs/STYLE_PRESETS.md](docs/STYLE_PRESETS.md) | 組み込みプリセット、「ファイルから自分のスタイルを学習させる」ワークフロー、プリセット管理コマンド |
| [skills/drawio-skill/SKILL.md](skills/drawio-skill/SKILL.md) | エージェントが読み込むワークフローガイド |

## 何ができるか

| 機能 | 説明 |
|---|---|
| `.drawio` XML 生成 | 自然言語の記述から |
| マルチフォーマットエクスポート | draw.io デスクトップのネイティブ CLI 経由で PNG / SVG / PDF / JPG |
| 6 種類の図表タイププリセット | ERD、UML クラス図、シーケンス図、アーキテクチャ図、ML/深層学習、フローチャート |
| イテレーティブレビュー | セルフチェック + 自動修正（最大 2 ラウンド）、その後あなたが承認するまで 5 ラウンドのフィードバックループ |
| スタイルプリセット | `.drawio` ファイルや画像からあなたのビジュアルスタイルを取り込み、名前を付けて保存し、必要時に再適用 |
| 自動トリガー | 複雑なシステムを説明する際に図表が役立つときに自動起動 |

## 対応している図表タイプ

| カテゴリ | 例 | 主な特徴 |
|---|---|---|
| アーキテクチャ | マイクロサービス、クラウド（AWS/GCP/Azure）、ネットワークトポロジ、デプロイ | 階層別スイムレーン、ハブセンター戦略 |
| ML / 深層学習 | Transformer、CNN、LSTM、GRU | テンソル形状アノテーション、レイヤータイプの色分け |
| フローチャート | ビジネスプロセス、ワークフロー、ディシジョンツリー、ステートマシン | 意味付きの形状（平行四辺形 I/O、ひし形ディシジョン） |
| UML | クラス図、シーケンス図 | 継承 / コンポジション / 集約矢印；ライフライン + アクティベーションボックス |
| データ | ER 図、データフロー図 (DFD) | テーブルコンテナ、PK/FK 表記 |
| その他 | 組織図、マインドマップ、ワイヤーフレーム | — |

## クイックスタート

2 ステップ — まず draw.io CLI をインストール（[docs/INSTALL_CLI.md](docs/INSTALL_CLI.md) を参照）し、次にスキルをホストに配置（[docs/INSTALL_SKILL.md](docs/INSTALL_SKILL.md) を参照）します。その後は欲しいものを記述するだけです：

```
Create a microservices e-commerce architecture with API Gateway, auth/user/order/product/payment services,
Kafka message queue, notification service, and separate databases for each service
```

スキルがレイアウトを計画し、`.drawio` XML を生成し、選択したフォーマットにエクスポートし、セルフチェックを行い、あなたがイテレーションできるようにします。

## コミュニティ

ヘルプ、Q&A、アップデートのために参加しよう：

- **Discord:** https://discord.gg/79JF5Atuk
- **WeChat:** 下記の QR コードをスキャン

<p align="center">
  <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/qrcode/agents365ai_wechat_1.png" width="200" alt="WeChat コミュニティグループ">
</p>

## サポート

このスキルが役に立ったら、作者へのサポートをご検討ください：

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
