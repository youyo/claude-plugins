# CLAUDE.md

このリポジトリで Claude Code が作業する際のガイダンス。

## リポジトリ概要

`heptagon-inc` Claude Code マーケットプレース。社内エージェント運用で使うプラグイン群を集約する。

## ディレクトリ構成

```
.claude-plugin/
  marketplace.json          # マーケットプレース定義
plugins/
  <plugin-name>/
    .claude-plugin/
      plugin.json           # プラグインメタデータ
    skills/                 # エージェント自律起動するスキル
      <skill-name>/
        SKILL.md
    commands/               # 人間が /<command> で呼ぶ
      *.md
    README.md
plans/                       # 実装計画ドキュメント（devflow:plan の出力）
```

## 設計方針

### スキル優先 / コマンド任意

- **エージェント自律起動が前提のものは `skills/`**: `description` に豊富なトリガーワード（日本語・英語・別名・ユースケース）を埋め込み、Claude Code が文脈から自動発火できるようにする
- **人間が明示的に呼ぶ場合のみ `commands/`**: スラッシュコマンドとして提供
- 両者を併設する必要はない。利用形態に合わせて片方を選ぶ

### Copilot CLI 統合プラグイン (`plugins/copilot`)

GitHub Copilot CLI (`@github/copilot`) を Claude Code から呼び出すプラグイン。詳細は `plugins/copilot/README.md`。

#### 重要な実装上の注意

実機検証で判明した CLI 仕様:

| 項目 | 確定内容 |
|---|---|
| 内蔵 MCP 無効化 | `--disable-builtin-mcps`（**`--no-mcp` は実在しない、過去のバグ事例**） |
| 書き込み拒否 | `--deny-tool='write'` |
| シェル拒否 | `--deny-tool='shell'`（bare 形式で動作確認済み、サイレント失敗なし） |
| Web 調査 | `--available-tools='web_fetch'`（`web_search` は実在しない） |
| 非対話モード必須セット | `--allow-all-tools --no-ask-user --no-remote --disallow-temp-dir` |

**フラグの実在性は実機の `copilot --help` と空打ちエラーで必ず確認すること**。ヘルプに書かれている記憶や examples に出ない記法は信用しない。

## 開発フロー

### devflow スキル統合

このリポジトリは `devflow` プラグイン（`/devflow:plan` / `/devflow:implement` / `/devflow:cycle`）でフロー駆動開発する:

1. `/devflow:plan <タスク説明>` — TDD・5観点27項目レビュー統合の計画書を `plans/` に生成
2. `/devflow:implement plans/<plan>.md` — 計画書に従って implementer + code-reviewer + commit-agent で実装
3. 必要に応じて `Phase 3.5 弁証法レビュー`（devils-advocate → advocate）で計画品質を上げる

### コミット規約

Heptagon 組織ルール:

- Conventional Commits 形式（`fix:`、`feat:`、`docs:`、`chore:` 等）
- メッセージは**日本語**
- ブランチ命名規則: 単一文字の前にハイフン禁止（❌ `fix-f-encoding` / ✅ `fix-japanese-filename-encoding`）

### TDD（実行可能コードがある場合）

Red → Green → Refactor を遵守する。本リポジトリは現状 markdown/JSON のみで実行可能コード無しのため自動テスト対象は限定的。プラグイン内に Node.js などのスクリプトを追加する場合は TDD を適用すること。

## 新規プラグインの追加手順

1. `plugins/<name>/.claude-plugin/plugin.json` を作成（`name`, `description`, `version`, `author`, `homepage`, `license`）
2. スキル中心なら `plugins/<name>/skills/<skill-name>/SKILL.md`、コマンド中心なら `plugins/<name>/commands/<cmd>.md`
3. `plugins/<name>/README.md` でユーザー向け説明
4. `.claude-plugin/marketplace.json` の `plugins` 配列に追加
5. `/devflow:plan` でプロジェクト全体の計画書を作るか、シンプルなら直接実装

## 言語

全会話・コミットメッセージ・PR・ドキュメントは**日本語**で記述する。技術用語・コード識別子は原文のまま。

## 関連ドキュメント

- ルートの `README.md` — マーケットプレース全体のユーザー向け案内
- `plugins/copilot/README.md` — copilot プラグインの利用方法
- `plans/copilot-web-research-skill.md` — copilot プラグインの最新実装計画（rev4）
