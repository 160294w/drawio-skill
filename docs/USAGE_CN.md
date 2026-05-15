# 使い方

[English](USAGE.md)

欲しい図表を直接記述してください：

```
画一个微服务电商架构图，包含 Mobile/Web/Admin 客户端，API Gateway，
Auth/User/Order/Product/Payment 微服务，Kafka 消息队列，Notification 服务，
以及各自独立的数据库
```

エージェントが自動的に `.drawio` ファイルを生成し PNG にエクスポートします。

## 例

**プロンプト：**
> 画一个微服务电商架构图，包含 Mobile/Web/Admin 客户端，API Gateway（含认证+限流+路由），
> Auth/User/Order/Product/Payment 微服务，Kafka 消息队列，Notification 服务，
> User DB / Order DB / Product DB / Redis Cache / Stripe API

**出力結果：**

![マイクロサービスアーキテクチャ図](../assets/microservices-example.png)

## トポロジー例

本 skill は複数の図表トポロジーをサポートし、線のルーティングが明瞭 —— 無関係な形状を貫通しません。

### スタートポロジー（7 ノード）

中央メッセージブローカー + 6 つのマイクロサービスを放射状に配置。接続線はさまざまな方向から Kafka に入り、ゼロ交差。

![スタートポロジー](../assets/demo-star-cn.png)

### レイヤード処理フロー（10 ノード、4 階層）

E コマースアーキテクチャ。2 本の交差接続を含む：注文→商品（同階層水平）と認証→Redis（対角線、ルーティングコリドー経由で迂回）。すべての線のルーティングは明瞭。

![レイヤード処理フロー](../assets/demo-layered-cn.png)

### リング / サイクル（8 ノード）

CI/CD パイプライン、閉じたループと 2 つの分岐を含む。線は矩形外周に沿って流れ、内部領域を貫通しない。

![リングトポロジー](../assets/demo-ring-cn.png)
