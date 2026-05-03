# Portainer セットアップガイド

## 概要

Portainer は、ユーザーが希望する追加サービスを Docker の Web UI から簡単にデプロイ・管理するためのツールです。

> **注意**: Traefik および GitLab は Portainer の管理対象外です。これらは引き続き CLI から起動・管理します。

## 前提条件

- Docker / Docker Compose がインストール済みであること
- Traefik が起動しており、外部ネットワーク `proxy` が作成済みであること

## セットアップ手順

### 1. 環境変数の設定

```bash
cd portainer
cp .env.example .env
```

`.env` を編集し、`PORTAINER_DOMAIN` に実際のドメイン名を設定します。

```env
PORTAINER_DOMAIN=portainer.example.com
```

### 2. 起動

```bash
docker compose up -d
```

### 3. 初期設定

ブラウザで `https://<PORTAINER_DOMAIN>` にアクセスし、管理者アカウントを作成します。

## compose.yaml の構成

| 項目 | 内容 |
|------|------|
| イメージ | `portainer/portainer-ce:2.21.4` |
| ボリューム | `portainer_data`（設定データの永続化） |
| Docker ソケット | `/var/run/docker.sock`（コンテナ管理のためマウント） |
| ネットワーク | `proxy`（Traefik と共有する外部ネットワーク） |
| 公開ポート | Traefik 経由で HTTPS（内部ポート 9000） |

## 運用上の注意

- Traefik と GitLab はこの Portainer から管理しません。それぞれのディレクトリで CLI 操作を行ってください。
- Docker ソケットは Portainer がコンテナを管理するために必要です。セキュリティ上の懸念がある場合は [Portainer Agent](https://docs.portainer.io/admin/environments/add/docker/agent) の利用を検討してください。
- バージョンアップ時は `compose.yaml` の `image` タグを確認・更新してください（例: `portainer/portainer-ce:2.21.4` → 新バージョン）。
