# オブザーバビリティ構成

## 概要

Prometheus + Grafana + Grafana Loki によるオブザーバビリティスタックです。
各サービスは独立したディレクトリに `compose.yaml` を持ち、`monitoring` という共有 Docker ネットワークで連携します。

## コンポーネント構成

| サービス | ポート | 役割 |
|---------|--------|------|
| Prometheus | 9090 | メトリクス収集・保存 |
| Grafana | 3000 | 可視化・ダッシュボード |
| Loki | 3100 | ログ集約・保存 |

## ディレクトリ構成

```
prometheus/
  compose.yaml       # Prometheus コンテナ定義
  prometheus.yml     # スクレイプ設定

grafana/
  compose.yaml       # Grafana コンテナ定義
  .env.example       # 環境変数テンプレート
  provisioning/
    datasources/
      datasources.yaml  # データソース自動プロビジョニング

loki/
  compose.yaml       # Loki コンテナ定義
  loki-config.yaml   # Loki 設定ファイル
```

## セキュリティ考慮事項

> ⚠️ **本番環境では必ず以下を実施してください**

- `grafana/.env.example` を `grafana/.env` としてコピーし、`GF_ADMIN_PASSWORD` を強固なパスワードに変更してください。
- `.env` ファイルはリポジトリにコミットしないでください（`.gitignore` に追加）。

```bash
cp grafana/.env.example grafana/.env
# .env を編集してパスワードを変更
```

## セットアップ

### 1. 共有ネットワークの作成

各サービスが通信するための外部ネットワークを事前に作成します。

```bash
docker network create monitoring
```

### 2. 環境変数の設定（Grafana）

```bash
cp grafana/.env.example grafana/.env
# grafana/.env を編集し GF_ADMIN_PASSWORD を変更してください
```

### 3. 各サービスの起動

それぞれのディレクトリで `docker compose up -d` を実行します。
各サービスは起動後に他サービスへの接続を自動的にリトライするため、起動順序の制約はありません。

```bash
cd loki && docker compose up -d

cd ../prometheus && docker compose up -d

cd ../grafana && docker compose up -d
```

### 4. Grafana へのアクセス

ブラウザで `http://localhost:3000` を開きます。

- ユーザー名: `.env` の `GF_ADMIN_USER`（デフォルト: `admin`）
- パスワード: `.env` の `GF_ADMIN_PASSWORD` に設定した値

Prometheus と Loki のデータソースはプロビジョニングにより自動登録されます。

## Promtail（ログシッパー）

アプリケーションのログを Loki へ転送するには Promtail を使用します。
Promtail は対象アプリケーションの `compose.yaml` に追加するか、別途専用の compose ファイルを作成してください。

設定例:

```yaml
services:
  promtail:
    image: grafana/promtail:3.5.0
    container_name: promtail
    volumes:
      - /var/log:/var/log:ro
      - ./promtail-config.yaml:/etc/promtail/config.yaml:ro
    command: -config.file=/etc/promtail/config.yaml
    networks:
      - monitoring

networks:
  monitoring:
    external: true
```

## 運用メモ

- Prometheus のスクレイプ設定を追加する場合は `prometheus/prometheus.yml` を編集し、`docker compose -f prometheus/compose.yaml restart` で反映します。
- データは名前付き Docker ボリューム（`prometheus_data`, `grafana_data`, `loki_data`）に永続化されます。
