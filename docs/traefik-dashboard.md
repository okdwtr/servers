# Traefik ダッシュボードを `traefik.domain.tld` で公開する

このリポジトリでは Traefik Dashboard を **docker-compose のラベル**で公開します。
現時点では認証は**適用せず**、将来有効化できるよう設定をコメントアウトで用意しています。

## 変更済みファイル

- 静的設定: `traefik/traefik.yml`
- compose 設定: `traefik/docker-compose.yml`

## 公開仕様

- ホスト名: `traefik.domain.tld`
- エントリポイント: `websecure`（443）
- ルーティング対象:
  - `/dashboard`
  - `/api`
- サービス: `api@internal`

## 起動方法

```bash
cd traefik
docker compose up -d
```

## 動作確認

```bash
curl -kI https://traefik.domain.tld/dashboard/
```

`HTTP/1.1 200` が返れば到達できています。

## 認証を有効化する手順（任意）

`traefik/docker-compose.yml` の `labels` で以下コメントを外してください。

1. `traefik.http.routers.traefik-dashboard.middlewares=traefik-dashboard-auth`
2. `traefik.http.middlewares.traefik-dashboard-auth.basicauth.users=...`

`users` には `htpasswd` 形式の資格情報を指定します。
