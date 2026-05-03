# Traefik SSH エントリポイント（ポート 22）

このドキュメントでは、Traefik に追加した `ssh` エントリポイント（`:22`）の目的・前提条件・ホスト側の設定手順を説明します。

## 概要

GitLab の Git-over-SSH を Traefik 経由で外部公開するため、Traefik の静的設定に `ssh` エントリポイントを追加しています。

| 設定項目 | 内容 |
|---|---|
| エントリポイント名 | `ssh` |
| 待受ポート（コンテナ内） | `22` |
| ホスト公開ポート（compose） | `22` |
| プロトコル | TCP |

## 前提条件：ホスト SSH デーモンのポート変更

ホストの SSH デーモン（`sshd`）がデフォルトのポート `22` で動作している場合、Traefik コンテナがポート `22` をバインドしようとして起動に失敗します。  
**Traefik を起動する前に、ホスト側の SSH デーモンを別ポート（例: `2222`）に変更してください。**

### 手順（Ubuntu / Debian 系）

1. SSH デーモン設定を編集する

   ```bash
   sudo vim /etc/ssh/sshd_config
   ```

2. `Port` 行を変更する

   ```
   Port 2222
   ```

3. SSH デーモンを再起動する

   ```bash
   sudo systemctl restart ssh
   ```

4. 新しいポートで接続できることを確認してから、古いセッションを閉じる

   ```bash
   ssh -p 2222 user@hostname
   ```

> **注意**: ホストへの SSH 接続が途切れないよう、新しいポートでの接続確認が完了するまで既存のセッションは閉じないでください。

## ファイアウォール設定

ポートを変更した場合は、ファイアウォールのルールも更新してください。

```bash
# ポート 22 を Traefik（Git SSH）用として許可済みであることを確認
sudo ufw allow 22/tcp

# ホスト SSH の新しいポートを許可
sudo ufw allow 2222/tcp

# 変更を反映
sudo ufw reload
```

## Traefik 側の設定

`traefik/traefik.yml` に以下のエントリポイントが定義されています。

```yaml
entryPoints:
  ssh:
    address: ":22"
```

`traefik/compose.yaml` でポート `22` をホストに公開しています。

```yaml
ports:
  - "22:22"
```

## GitLab との連携

GitLab 側の `compose.yaml` で以下のラベルを設定することで、Traefik が SSH トラフィックを GitLab コンテナへ転送します。

```yaml
labels:
  - traefik.tcp.routers.gitlab-ssh.rule=HostSNI(`*`)
  - traefik.tcp.routers.gitlab-ssh.entrypoints=ssh
  - traefik.tcp.routers.gitlab-ssh.service=gitlab-ssh-svc
  - traefik.tcp.services.gitlab-ssh-svc.loadbalancer.server.port=22
```

> GitLab コンテナ内の SSH ポートは `22` のままです。ホスト公開は行わず、Traefik 経由でのみアクセスします。
