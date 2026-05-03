# AGENTS Instructions

## Documentation policy
- 技術的な詳細や説明は `docs/` 以下に Markdown (`.md`) で記載してください。
- 新しい設計方針・運用手順・仕様説明など、継続的に参照される情報は `docs/` 配下へ追記してください。
- `README.md` を更新・参照した際に詳細説明が必要と判断できる内容は、`docs/` に補足ドキュメントを追加してください。

## Working style
- コードはこまめにコミットしてください。
- ひとかたまりの変更に対して 1 コミットを目安にしてください。
- 新たに与えられた指示で、`AGENTS.md` にない追加情報のうち効果が一時的でないものはこのファイルへ追記してください。

## Service operation policy
- Portainer は、ユーザーが希望する追加サービスを簡単に利用するために導入します。
- Traefik と GitLab は Portainer 管理の対象外とし、CLI から起動・管理する運用を維持してください。
