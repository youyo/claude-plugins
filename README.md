# claude-plugins

Heptagon の Claude Code マーケットプレース。社内エージェント運用で使うプラグインを集約する。

## マーケットプレース情報

- **名前**: `youyo`
- **オーナー**: youyo
- **バージョン**: 0.1.0

## 提供プラグイン

| プラグイン | 用途 |
|---|---|
| [`copilot`](./plugins/copilot/) | GitHub Copilot CLI を Claude Code から呼び出し、コード・PR・実装計画のレビューと Web 調査をエージェントが自律的に実行できるスキル群 |
| [`logvalet`](./plugins/logvalet/) | Backlog 向け LLM-first CLI logvalet を Claude Code から自律起動するスキル群（情報収集・分析・アクション・レポート） |

## インストール

Claude Code でこのマーケットプレースを追加:

```
/plugin marketplace add youyo/claude-plugins
```

その後、利用したいプラグインをインストール:

```
/plugin install copilot@youyo
/plugin install logvalet@youyo
```

## copilot プラグイン

GitHub Copilot CLI (`@github/copilot`) を読み取り中心のサブエージェントとして起動するスキル群。Claude が文脈から自律発火する想定で、人間が明示的に `/copilot:xxx` を打つことは想定していない。

### 提供スキル

| スキル | 用途 |
|---|---|
| `copilot:code-review` | ローカル git 差分のコードレビュー |
| `copilot:adversarial-review` | 設計選択・前提への批判的レビュー |
| `copilot:pr-review` | GitHub PR のレビュー |
| `copilot:plan-review` | 実装計画ドキュメントのレビュー |
| `copilot:web-research` | Web 調査サブエージェント（一次情報の収集と構造化要約） |

### 前提

- Node.js 18.18+
- `@github/copilot` CLI: `npm install -g @github/copilot`
- 認証: `COPILOT_GITHUB_TOKEN` 環境変数 または `copilot /login`
- PR レビューには `gh` CLI 認証も必要

詳細は [`plugins/copilot/README.md`](./plugins/copilot/README.md)。

## logvalet プラグイン

Backlog 向け LLM-first CLI `logvalet` を Claude Code から自律起動するスキル群。情報収集・分析・PM ワークフロー・課題操作・レポートまで 14 スキル。CLI が PATH に必要。

### 提供スキル

| スキル | 用途 |
|---|---|
| `logvalet:logvalet` | ハブ（全スキルの案内・ワークフロー） |
| `logvalet:my-week` | 今週の担当タスク + ウォッチ課題 |
| `logvalet:my-next` | 直近の担当タスク + ウォッチ課題 |
| `logvalet:context` | 課題の全コンテキスト一括取得 |
| `logvalet:decisions` | 過去の意思決定ログ抽出 |
| `logvalet:health` | プロジェクト健全性スコア |
| `logvalet:risk` | 統合リスク評価 + 推奨アクション |
| `logvalet:intelligence` | アクティビティ異常検知 |
| `logvalet:triage` | 課題トリアージ（優先度・担当提案） |
| `logvalet:draft` | コメント下書き生成 |
| `logvalet:issue-create` | 対話型課題作成 |
| `logvalet:spec-to-issues` | 仕様書 → 課題分解 |
| `logvalet:report` | 月次・週次活動レポート |
| `logvalet:digest-periodic` | 週次・日次ダイジェスト |

### 前提

- `logvalet` CLI を PATH に: `brew install youyo/tap/logvalet` または `go install github.com/youyo/logvalet/cmd/logvalet@latest`
- 初期設定: `logvalet configure --init-profile <名前> --init-space <スペース> --init-api-key <APIKEY>`
- Backlog API キーは `~/.config/logvalet/` に保存される

詳細は [`plugins/logvalet/README.md`](./plugins/logvalet/README.md)。

> **旧 logvalet プラグインを使っていた場合**: `/plugin uninstall logvalet@<旧>` で先にアンインストールしてから claude-plugins 経由で再インストールしてください。

## ライセンス

MIT

## 開発

このリポジトリは `devflow` プラグイン（`/devflow:plan` / `/devflow:implement`）で TDD・品質レビュー統合のフロー駆動開発を行う。詳細は [`CLAUDE.md`](./CLAUDE.md)。

新規プラグイン追加時の手順、Copilot CLI フラグの実機確認結果、コミット規約なども `CLAUDE.md` を参照。
