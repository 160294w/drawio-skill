# 使い方

[中国語](USAGE_CN.md)

作りたいものを記述するだけです：

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

このスキルは、エッジルーティングをきれいに保ち（線が形状を貫通しないように）、さまざまな図表トポロジーに対応します。

### スタートポロジー（7 ノード）

中央のメッセージブローカーを囲むように 6 つのマイクロサービスが放射状に配置されます。エッジはさまざまな側から Kafka に入り、線の交差はゼロです。

![スタートポロジー](../assets/demo-star.png)

### レイヤードフロー（10 ノード、4 階層）

E コマースアーキテクチャに 2 つのクロス接続があります：Order→Product（同じ階層を水平に）と Auth→Redis（ルーティングの通り道を経由した斜めの接続）。すべてのエッジがきれいにルーティングされています。

![レイヤードフロー](../assets/demo-layered.png)

### リング / サイクル（8 ノード）

閉ループと 2 本の分岐をもつ CI/CD パイプライン。エッジは内部を横切らず、外周に沿って流れています。

![リングサイクル](../assets/demo-ring.png)
