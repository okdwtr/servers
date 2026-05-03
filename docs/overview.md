# サーバー構成ドキュメント

このドキュメントは `README.md` の概要を補足し、運用時に参照しやすい形でサーバー構成の要点を整理したものです。

## 1. 目的

本リポジトリは Docker / Docker Compose を使って、以下のコンポーネントを連携運用するための設定を管理します。

- Traefik（リバースプロキシ）
- GitLab（コード管理）
- GitLab Pages（静的サイトホスティング）
- GitLab Runner（CI/CD 実行環境）
- Portainer（追加サービス管理）

## 2. 通信フロー

1. 外部からの HTTPS リクエストは Traefik が受け付けます。
2. Traefik がホスト名やルールに基づき GitLab / Pages へ HTTP ルーティングします。
3. GitLab の CI/CD ジョブは Runner で実行されます。
4. 実行結果は GitLab に返却されます。
5. Portainer は、ユーザーが希望する追加サービスをネットワーク経由で利用するための管理導線として利用します。

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

### Portainer

- ユーザー希望の追加サービスを簡易にデプロイ/操作するための管理 UI
- 追加サービスのライフサイクル管理（起動・停止・確認）の効率化
- **対象外**: Traefik / GitLab（これらは CLI から起動・管理する運用を維持）

## 4. 前提環境

- Docker
- Docker Compose

## 5. ドキュメント運用ルール

- 新規に技術的な詳細（設計・運用・障害対応・セキュリティ注意点など）を追加する場合は `docs/` 配下に `.md` で追記します。
- 既存の README は概要説明、`docs/` は詳細説明という役割で使い分けます。
