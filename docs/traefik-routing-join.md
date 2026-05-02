# Traefik の routing にサービスを join する方法

このドキュメントでは、アプリケーションコンテナを Traefik のルーティング対象に参加させるために、
`docker-compose.yml` に何を記載するべきかを説明します。

## 前提

- Traefik 本体は `traefik/docker-compose.yml` で起動済み。
- Traefik と同じ Docker ネットワーク（`proxy`）にアプリケーションを接続できること。
- Traefik 側で Docker provider が有効になっていること。

## 最小構成（compose 例）

以下は `whoami` コンテナを `app.example.com` で公開する例です。

```yaml
services:
  whoami:
    image: traefik/whoami:v1.11
    container_name: whoami
    restart: unless-stopped

    # Traefik がサービスを検出するためのラベル
    labels:
      - traefik.enable=true

      # どのネットワーク経由で到達するか
      - traefik.docker.network=proxy

      # Router 定義
      - traefik.http.routers.whoami.rule=Host(`app.example.com`)
      - traefik.http.routers.whoami.entrypoints=websecure
      - traefik.http.routers.whoami.tls=true

      # Service 定義（コンテナ内の待受ポート）
      - traefik.http.services.whoami.loadbalancer.server.port=80

    networks:
      - proxy

networks:
  proxy:
    external: true
```

## compose に記載すべき設定の要点

1. `labels.traefik.enable=true`
   - デフォルトでは `exposedByDefault=false` のため、明示しないと公開されません。

2. `labels.traefik.docker.network=proxy`
   - Traefik から接続する Docker ネットワークを明示します。
   - コンテナが複数ネットワークに属する場合に特に重要です。

3. `traefik.http.routers.<name>.*`
   - `rule`: ホスト名やパス条件。
   - `entrypoints`: `web` / `websecure` など。
   - `tls`: HTTPS 終端する場合は `true`。

4. `traefik.http.services.<name>.loadbalancer.server.port`
   - アプリケーションコンテナ内部の公開ポート。
   - `ports:` でホスト公開しなくても、同一 Docker ネットワーク内なら Traefik から到達できます。

5. `networks: [proxy]`
   - Traefik と同じネットワークに参加させる必要があります。

## よくあるハマりどころ

- **ラベル名の `<name>` が一致していない**
  - 例: router 名と service 名の参照がズレると 404/502 の原因になります。

- **`proxy` ネットワークに join していない**
  - Traefik がコンテナへ接続できず、502 が発生します。

- **`loadbalancer.server.port` が誤っている**
  - コンテナ内部の実ポートを指定してください（ホスト側ポートではありません）。

## file provider を使う場合

このリポジトリでは `traefik/dynamic/dynamic-example.yml` を用意しています。
Docker ラベルではなくファイル管理したい場合は、サンプルのコメントを外して利用してください。
