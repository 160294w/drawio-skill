# drawio-skill —— テキストからプロフェッショナルな図表へ

[English](README.md) | [オンラインドキュメント](https://agents365-ai.github.io/drawio-skill/)

<p align="center">
  <img src="assets/workflow-cn.png" width="900" alt="ワークフロー">
</p>

自然言語の記述を `.drawio` XML に変換し、draw.io デスクトップ版のネイティブ CLI を使って PNG / SVG / PDF / JPG にエクスポートするスキルです。6 種類の図表プリセット（ER 図、UML クラス図、シーケンス図、アーキテクチャ図、ML/DL、フローチャート）、セルフチェックと自動修正（2 ラウンド）、繰り返し改善できるフィードバックループ（5 ラウンド）、サンプルファイルや画像から学習できるスタイルプリセットを内蔵しています。

Claude Code、Cursor、Copilot、OpenClaw、Codex、Hermes など、[Agent Skills](https://agentskills.io) 仕様に準拠する任意のエージェントに対応しています。

## ドキュメント一覧

| ドキュメント | 内容 |
|---|---|
| [docs/COMPARISON_CN.md](docs/COMPARISON_CN.md) | ネイティブエージェントや他の draw.io スキル/ツールとの比較表と主な利点 |
| [docs/INSTALL_CLI_CN.md](docs/INSTALL_CLI_CN.md) | macOS / Windows / Linux 各プラットフォーム向け draw.io デスクトップ CLI のインストール手順 |
| [docs/INSTALL_SKILL_CN.md](docs/INSTALL_SKILL_CN.md) | プラグインマーケット、手動クローン、アップデートコマンド |
| [docs/USAGE_CN.md](docs/USAGE_CN.md) | 自然言語プロンプト、マイクロサービス例、複数のトポロジーデモ（スター / レイヤード / リング） |
| [docs/STYLE_PRESETS_CN.md](docs/STYLE_PRESETS_CN.md) | 組み込みプリセット、「ファイルからスタイルを学習」フロー、プリセットの管理コマンド一式 |
| [skills/drawio-skill/SKILL.md](skills/drawio-skill/SKILL.md) | エージェントが読み込むワークフローガイド |

## 機能

| 機能 | 説明 |
|---|---|
| `.drawio` XML の生成 | 自然言語の記述から生成 |
| 複数フォーマットへのエクスポート | draw.io デスクトップ版のネイティブ CLI を使って PNG / SVG / PDF / JPG に出力 |
| 6 種類の図表プリセット | ERD、UML クラス図、シーケンス図、アーキテクチャ図、ML/深層学習、フローチャート |
| 繰り返し改善 | セルフチェックと自動修正（最大 2 ラウンド）の後、納得いくまで 5 ラウンドのフィードバックループ |
| スタイルプリセット | `.drawio` ファイルや画像から自分のスタイルを Skill に学習させ、名前を付けて保存していつでも再利用 |
| 自動起動 | 複雑なシステムの説明に図表が役立つ場面で自動的に呼び出される |

## 対応している図表タイプ

| カテゴリ | 例 | 特徴 |
|---|---|---|
| アーキテクチャ図 | マイクロサービス、クラウド（AWS/GCP/Azure）、ネットワークトポロジ、デプロイ | 階層別スイムレーン、ハブ中央配置戦略 |
| ML / 深層学習 | Transformer、CNN、LSTM、GRU | テンソル形状の注釈、レイヤー種別ごとの配色 |
| フローチャート | 業務プロセス、ワークフロー、決定木、状態遷移 | 意味付きの形状（平行四辺形の I/O、ひし形の判定） |
| UML | クラス図、シーケンス図 | 継承 / コンポジション / 集約の矢印、ライフラインとアクティベーションフレーム |
| データ図 | ER 図、データフロー図（DFD） | テーブルコンテナ、PK/FK 表記 |
| その他 | 組織図、マインドマップ、ワイヤーフレーム | — |

## クイックスタート

2 ステップで使えます。まず draw.io CLI をインストールし（[docs/INSTALL_CLI_CN.md](docs/INSTALL_CLI_CN.md) を参照）、次にスキルをホストに読み込ませます（[docs/INSTALL_SKILL_CN.md](docs/INSTALL_SKILL_CN.md) を参照）。インストール後は、作りたい内容を記述するだけです：

```
画一个微服务电商架构图,包含 API Gateway、用户/订单/商品/支付服务、
Kafka 消息队列、通知服务,以及各自独立的数据库
```

スキルがレイアウトを設計し、`.drawio` XML を生成し、選択したフォーマットにエクスポートし、セルフチェックを行ったうえで、繰り返し改善できるようサポートします。

## コミュニティ

ヘルプ、質問、最新情報のために、交流グループにぜひ参加してください：

- **Discord:** https://discord.gg/79JF5Atuk
- **WeChat:** 下の QR コードをスキャン

<p align="center">
  <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/qrcode/agents365ai_wechat_1.png" width="200" alt="WeChat 交流グループ">
</p>

## 作者をサポート

この skill がお役に立ったら、ぜひ作者へのサポートをご検討ください：

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
