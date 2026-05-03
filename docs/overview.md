# サーバー構成ドキュメント

このドキュメントは `README.md` の概要を補足し、運用時に参照しやすい形でサーバー構成の要点を整理したものです。

## 1. 目的

本リポジトリは Docker / Docker Compose を使って、以下のコンポーネントを連携運用するための設定を管理します。

- Traefik（リバースプロキシ）
- GitLab（コード管理）
- GitLab Pages（静的サイトホスティング）
- GitLab Runner（CI/CD 実行環境）

## 2. 通信フロー

1. 外部からの HTTPS リクエストは Traefik が受け付けます。
2. Traefik がホスト名やルールに基づき GitLab / Pages へ HTTP ルーティングします。
3. GitLab の CI/CD ジョブは Runner で実行されます。
4. 実行結果は GitLab に返却されます。

## 3. コンポーネント責務

### Traefik

- SSL/TLS 終端
- ドメイン管理
- バックエンドサービスへのルーティング

### GitLab

- リポジトリ管理
- CI/CD パイプライン管理
- Runner と連携したジョブ制御

### GitLab Pages

- 静的コンテンツの公開
- Traefik 経由の外部公開

### GitLab Runner

- CI/CD ジョブ実行
- GitLab への実行結果返却

## 4. 前提環境

- Docker
- Docker Compose

## 5. ドキュメント運用ルール

- 新規に技術的な詳細（設計・運用・障害対応・セキュリティ注意点など）を追加する場合は `docs/` 配下に `.md` で追記します。
- 既存の README は概要説明、`docs/` は詳細説明という役割で使い分けます。
