# servers

Dockerを使用したサーバー構成管理リポジトリ

## 概要

このリポジトリは、複数のサーバーコンポーネントをDockerコンテナで運用するためのセットアップスクリプトと設定ファイルを管理します。

## アーキテクチャ

```mermaid
graph TB
    subgraph "インターネット"
        INET["External Requests"]
    end
    
    subgraph "ホストOS"
        subgraph "Docker Network"
            TRAEFIK["Traefik<br/>リバースプロキシ<br/>Port 443 TLS"]
            GITLAB["GitLab<br/>コード管理"]
            PAGES["GitLab Pages<br/>静的サイトホスティング"]
            RUNNER["GitLab Runner<br/>CI/CD実行環境"]
        end
    end
    
    INET -->|HTTPS| TRAEFIK
    TRAEFIK -->|HTTP| GITLAB
    TRAEFIK -->|HTTP| PAGES
    GITLAB -.->|CI/CD Jobs| RUNNER
    RUNNER -.->|Results| GITLAB
    
    classDef external fill:#ff9999,stroke:#cc0000,stroke-width:2px,color:#000
    classDef proxy fill:#99ccff,stroke:#0066cc,stroke-width:2px,color:#000
    classDef service fill:#99ff99,stroke:#009900,stroke-width:2px,color:#000
    classDef ci fill:#ffcc99,stroke:#ff6600,stroke-width:2px,color:#000
    
    class INET external
    class TRAEFIK proxy
    class GITLAB,PAGES service
    class RUNNER ci
```

## 構成

### Traefik
リバースプロキシとして機能し、すべてのトラフィックをルーティングします。
- SSL/TLS終端
- ドメイン管理
- リクエストルーティング

### GitLab
コード管理プラットフォーム

#### Runner
GitLab CIパイプラインの実行環境

#### Pages
静的サイトホスティング機能

## 必要な環境

- Docker
- Docker Compose

## セットアップ

各コンポーネントの詳細なセットアップ方法については、該当するディレクトリ内のドキュメントを参照してください。

## ライセンス

詳細は[LICENSE](LICENSE)を参照してください。
