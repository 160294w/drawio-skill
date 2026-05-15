# 使い方

[English](USAGE.md)

作りたい図表をそのまま記述してください：

```
画一个微服务电商架构图，包含 Mobile/Web/Admin 客户端，API Gateway，
Auth/User/Order/Product/Payment 微服务，Kafka 消息队列，Notification 服务，
以及各自独立的数据库
```

エージェントが `.drawio` ファイルを自動的に生成し、PNG にエクスポートします。

## 例

**プロンプト：**
> 画一个微服务电商架构图，包含 Mobile/Web/Admin 客户端，API Gateway（含认证+限流+路由），
> Auth/User/Order/Product/Payment 微服务，Kafka 消息队列，Notification 服务，
> User DB / Order DB / Product DB / Redis Cache / Stripe API

**出力結果：**

![マイクロサービスアーキテクチャ図](../assets/microservices-example.png)

## トポロジー例

本 skill は複数の図表トポロジーに対応し、線のルーティングをきれいに保ちます（無関係な形状を貫通しません）。

### スタートポロジー（7 ノード）

中央のメッセージブローカーを囲むように 6 つのマイクロサービスが放射状に配置されます。接続線はさまざまな方向から Kafka に入り、線の交差はゼロです。

![スタートポロジー](../assets/demo-star-cn.png)

### レイヤード処理フロー（10 ノード、4 階層）

E コマースアーキテクチャ。2 本の交差接続を含みます：注文→商品（同じ階層を水平に）と認証→Redis（対角線、ルーティングの通り道を経由した迂回）。すべての線がきれいにルーティングされています。

![レイヤード処理フロー](../assets/demo-layered-cn.png)

### リング / サイクル（8 ノード）

閉ループと 2 本の分岐をもつ CI/CD パイプライン。線は内部を横切らず、外周に沿って流れています。

![リングトポロジー](../assets/demo-ring-cn.png)
