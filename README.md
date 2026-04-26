# claude-plugins

Heptagon の Claude Code マーケットプレース。社内エージェント運用で使うプラグインを集約する。

## マーケットプレース情報

- **名前**: `heptagon-inc`
- **オーナー**: youyo
- **バージョン**: 0.1.0

## 提供プラグイン

| プラグイン | 用途 |
|---|---|
| [`copilot`](./plugins/copilot/) | GitHub Copilot CLI を Claude Code から呼び出し、コード・PR・実装計画のレビューと Web 調査をエージェントが自律的に実行できるスキル群 |

## インストール

Claude Code でこのマーケットプレースを追加:

```
/plugin marketplace add youyo/claude-plugins
```

その後、利用したいプラグインをインストール:

```
/plugin install copilot@heptagon-inc
```

## copilot プラグイン

GitHub Copilot CLI (`@github/copilot`) を読み取り中心のサブエージェントとして起動するスキル群。Claude が文脈から自律発火する想定で、人間が明示的に `/copilot:xxx` を打つことは想定していない。

### 提供スキル

| スキル | 用途 |
|---|---|
| `copilot-code-review` | ローカル git 差分のコードレビュー |
| `copilot-adversarial-review` | 設計選択・前提への批判的レビュー |
| `copilot-pr-review` | GitHub PR のレビュー |
| `copilot-plan-review` | 実装計画ドキュメントのレビュー |
| `copilot-web-research` | Web 調査サブエージェント（一次情報の収集と構造化要約） |

### 前提

- Node.js 18.18+
- `@github/copilot` CLI: `npm install -g @github/copilot`
- 認証: `COPILOT_GITHUB_TOKEN` 環境変数 または `copilot /login`
- PR レビューには `gh` CLI 認証も必要

詳細は [`plugins/copilot/README.md`](./plugins/copilot/README.md)。

## ライセンス

MIT

## 開発

このリポジトリは `devflow` プラグイン（`/devflow:plan` / `/devflow:implement`）で TDD・品質レビュー統合のフロー駆動開発を行う。詳細は [`CLAUDE.md`](./CLAUDE.md)。

新規プラグイン追加時の手順、Copilot CLI フラグの実機確認結果、コミット規約なども `CLAUDE.md` を参照。
