# 使い方

[中国語](USAGE_CN.md)

欲しいものを記述するだけ：

```
Create a microservices e-commerce architecture with API Gateway, auth/user/order/product/payment services,
Kafka message queue, notification service, and separate databases for each service
```

エージェントが `.drawio` XML ファイルを生成し、自動的に PNG にエクスポートします。

## 例

**プロンプト：**
> Create a microservices e-commerce architecture with Mobile/Web/Admin clients, API Gateway,
> Auth/User/Order/Product/Payment services, Kafka message queue, Notification service,
> and User DB / Order DB / Product DB / Redis Cache / Stripe API

**出力：**

![マイクロサービスアーキテクチャ](../assets/microservices-example.png)

## トポロジーデモ

このスキルは、クリーンなエッジルーティング — 線が形状を貫通しない — でさまざまな図表トポロジーを扱います。

### スタートポロジー（7 ノード）

中央のメッセージブローカーから 6 つのマイクロサービスが外側に放射状に配置。エッジはさまざまな側面から Kafka に入り、ゼロ交差です。

![スタートポロジー](../assets/demo-star.png)

### レイヤードフロー（10 ノード、4 階層）

E コマースアーキテクチャに 2 つのクロス接続：Order→Product（同階層水平）と Auth→Redis（ルーティングコリドー経由の斜め）。すべてのエッジがクリーンにルーティングされます。

![レイヤードフロー](../assets/demo-layered.png)

### リング / サイクル（8 ノード）

クローズドループと 2 つのスパー分岐を持つ CI/CD パイプライン。エッジは内部を横切らずに外周に沿って流れます。

![リングサイクル](../assets/demo-ring.png)
