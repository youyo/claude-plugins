# copilot

GitHub Copilot CLI (`@github/copilot`) を Claude Code から呼び出し、エージェントが自律的にセカンドオピニオンを取得するためのスキル群。

## 提供スキル

| スキル | 用途 |
|---|---|
| `copilot:code-review` | ローカル git 差分のコードレビュー |
| `copilot:adversarial-review` | 設計選択・前提への批判的レビュー |
| `copilot:pr-review` | GitHub PR のレビュー |
| `copilot:plan-review` | 実装計画ドキュメントのレビュー |
| `copilot:web-research` | Web 調査サブエージェント（一次情報の収集と構造化要約） |

これらは人間が `/<command>` で呼ぶことを想定しておらず、エージェントが文脈に応じて自律起動する。各 SKILL.md の `description` にトリガーワードを多数埋め込んである。

## 前提

- Node.js 18.18+
- `@github/copilot` CLI: `npm install -g @github/copilot`
- 認証: `COPILOT_GITHUB_TOKEN` 環境変数 または `copilot /login`
- PR レビューには `gh` CLI 認証も必要

## デフォルト設定

### レビュー系（code/adversarial/pr/plan）

- モデル: `gpt-5.4`、effort: `high`
- 権限: `--allow-all-tools --deny-tool='write' --deny-tool='shell' --disable-builtin-mcps --disallow-temp-dir --no-ask-user --no-remote`

### 調査系（web-research）

- モデル: `gpt-5.4-mini`、effort: `xhigh`
- 権限: `--available-tools='web_fetch' --allow-all-urls --disable-builtin-mcps --disallow-temp-dir --no-ask-user --no-remote`

引数で `--model` / `--effort` を上書き可能。

## Claude WebSearch との使い分け

- **WebSearch**: シンプルな lookup、Claude メインコンテキスト内で完結したい場合
- **copilot:web-research**: 深い調査（xhigh effort）、別モデル視点、コンテキスト分離が有効な場合

## コスト管理 / クォータ

- `--effort xhigh` は重い。簡単な lookup には `--effort medium` を推奨
- GitHub Copilot サブスクリプションのクォータ消費に注意
- クォータ枯渇時は Claude WebSearch にフォールバック

## セキュリティ

- レビュー系: `--allow-all-tools --deny-tool='write' --deny-tool='shell' --disable-builtin-mcps --disallow-temp-dir --no-ask-user --no-remote` を必須付与
- 調査系: `--available-tools='web_fetch'` で read 系・bash 系を物理遮断（D03 情報漏洩経路を遮断）
- 引数値は allowlist 検証（`/^[\w.-]+$/`）でコマンドインジェクション防止
- レビュー/調査対象は `<untrusted_*>` タグで境界化しプロンプトインジェクション軽減
- パス指定は `path.relative` ベースで作業ディレクトリ配下に限定

## データ送信に関する注意

レビュー対象（コード差分・PR 情報・計画ドキュメント）は Copilot CLI を介して GitHub/OpenAI に送信される。機密情報を含むコードのレビューには使用しないこと。
