# drawio-skill —— テキストからプロフェッショナルな図表へ

[English](README.md) | [オンラインドキュメント](https://agents365-ai.github.io/drawio-skill/)

<p align="center">
  <img src="assets/workflow-cn.png" width="900" alt="ワークフロー">
</p>

自然言語の記述を `.drawio` XML に変換し、draw.io デスクトップ版のネイティブ CLI 経由で PNG / SVG / PDF / JPG にエクスポートするスキル。6 種類の図表プリセット（ER 図、UML クラス図、シーケンス図、アーキテクチャ図、ML/DL、フローチャート）、セルフチェック + 自動修正（2 ラウンド）、イテレーティブフィードバックループ（5 ラウンド）、サンプルファイルや画像から学習できるスタイルプリセットを内蔵しています。

Claude Code、Cursor、Copilot、OpenClaw、Codex、Hermes など、[Agent Skills](https://agentskills.io) 仕様に準拠する任意のエージェントをサポートします。

## ドキュメントナビゲーション

| ドキュメント | 内容 |
|---|---|
| [docs/COMPARISON_CN.md](docs/COMPARISON_CN.md) | ネイティブエージェントや他の draw.io スキル/ツールとの比較表とコアアドバンテージ |
| [docs/INSTALL_CLI_CN.md](docs/INSTALL_CLI_CN.md) | macOS / Windows / Linux 各プラットフォームの draw.io デスクトップ CLI インストールレシピ |
| [docs/INSTALL_SKILL_CN.md](docs/INSTALL_SKILL_CN.md) | プラグインマーケット、手動クローンとアップデートコマンド |
| [docs/USAGE_CN.md](docs/USAGE_CN.md) | 自然言語プロンプト、マイクロサービス例、複数のトポロジーデモ（スター / レイヤード / リング） |
| [docs/STYLE_PRESETS_CN.md](docs/STYLE_PRESETS_CN.md) | 組み込みプリセット、「ファイルからスタイルを学習」フロー、プリセットの完全な管理コマンド |
| [skills/drawio-skill/SKILL.md](skills/drawio-skill/SKILL.md) | エージェントが読み込むワークフローガイド |

## 機能説明

| 能力 | 説明 |
|---|---|
| `.drawio` XML 生成 | 自然言語の記述から生成 |
| マルチフォーマットエクスポート | PNG / SVG / PDF / JPG、draw.io デスクトップ版のネイティブ CLI 使用 |
| 6 種類の図表タイププリセット | ERD、UML クラス図、シーケンス図、アーキテクチャ図、ML/深層学習、フローチャート |
| イテレーティブレビュー | セルフチェック + 自動修正（最大 2 ラウンド）、その後あなたが満足するまで 5 ラウンドのフィードバックループ |
| スタイルプリセット | `.drawio` ファイルや画像を使ってあなたのスタイルを Skill に「教え込み」、命名して保存していつでも再利用 |
| 自動トリガー | 図表が複雑なシステムの説明に役立つ際に自動呼び出し |

## 対応している図表タイプ

| カテゴリ | 例 | 特色 |
|---|---|---|
| アーキテクチャ図 | マイクロサービス、クラウド（AWS/GCP/Azure）、ネットワークトポロジ、デプロイ | 階層スイムレーン、hub 中央配置戦略 |
| ML / 深層学習 | Transformer、CNN、LSTM、GRU | テンソル形状アノテーション、レイヤータイプ別配色 |
| フローチャート | ビジネスプロセス、ワークフロー、ディシジョンツリー、ステートマシン | 意味付き形状（平行四辺形 I/O、ひし形ディシジョン） |
| UML | クラス図、シーケンス図 | 継承 / コンポジション / 集約矢印；ライフライン + アクティベーションフレーム |
| データ図 | ER 図、データフロー図 (DFD) | テーブルコンテナ、PK/FK 表記 |
| その他 | 組織図、マインドマップ、ワイヤーフレーム | — |

## クイックスタート

2 ステップ —— まず draw.io CLI をインストール（[docs/INSTALL_CLI_CN.md](docs/INSTALL_CLI_CN.md) を参照）し、次にスキルを host にロード（[docs/INSTALL_SKILL_CN.md](docs/INSTALL_SKILL_CN.md) を参照）します。インストール後は、欲しい内容を直接記述するだけ：

```
画一个微服务电商架构图,包含 API Gateway、用户/订单/商品/支付服务、
Kafka 消息队列、通知服务,以及各自独立的数据库
```

スキルがレイアウトを計画し、`.drawio` XML を生成し、選択したフォーマットにエクスポートし、セルフチェックを行い、その後イテレーションできるようにします。

## コミュニティ

ヘルプ、質問、最新の動向のため、交流グループに参加してください：

- **Discord:** https://discord.gg/79JF5Atuk
- **WeChat:** 下記の QR コードをスキャン

<p align="center">
  <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/qrcode/agents365ai_wechat_1.png" width="200" alt="WeChat 交流グループ">
</p>

## 作者をサポート

この skill があなたの役に立ったら、ぜひ作者をサポートしてください：

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
      <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/awarding/award.gif" width="180" alt="投げ銭">
      <br>
      <b>投げ銭</b>
    </td>
  </tr>
</table>

## 作者

**Agents365-ai**

- Bilibili: https://space.bilibili.com/441831884
- GitHub: https://github.com/Agents365-ai

## License

MIT
