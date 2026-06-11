# logvalet

Backlog 向け LLM-first CLI `logvalet` を Claude Code から自律起動するスキル群。情報収集・分析・アクション・レポート機能を提供し、エージェントが文脈に応じて自動実行する。

## 前提条件（重要）

logvalet CLI バイナリは別途インストールが必要です:

```bash
# Homebrew（推奨）
brew install youyo/tap/logvalet

# または Go install
go install github.com/youyo/logvalet/cmd/logvalet@latest
```

インストール後、動作確認:

```bash
logvalet --version
# または
lv --version
```

初期設定:

```bash
logvalet configure --init-profile <プロフィール名> \
  --init-space <Backlogスペースキー> \
  --init-api-key <APIキー>
```

**認証**: Backlog API キーは以下に保管されます:

```
~/.config/logvalet/
```

シークレットをコードに埋め込まないこと。

## 提供スキル

15 個の logvalet スキルを提供（表示名は `logvalet:<skill>` 形式）:

| スキル | 用途 |
|---|---|
| `logvalet:logvalet` | スキル群の案内・ハブ |
| `logvalet:my-week` | 今週のタスク・スケジュール表示 |
| `logvalet:my-next` | 次のアクション（優先度ソート） |
| `logvalet:context` | 指定課題のコンテキスト・履歴・決定情報 |
| `logvalet:decisions` | 過去決定事項の一覧・参照 |
| `logvalet:health` | プロジェクト健全性スコア・リスク検出 |
| `logvalet:risk` | ブロッカー・スタック検出 |
| `logvalet:intelligence` | プロジェクト分析・パターン認識 |
| `logvalet:triage` | 新規課題の優先度診断 |
| `logvalet:draft` | 課題テンプレート・説明文ドラフト |
| `logvalet:issue-create` | 課題作成ワークフロー |
| `logvalet:spec-to-issues` | 仕様書→課題タスク分割 |
| `logvalet:report` | プロジェクト・チーム報告書生成 |
| `logvalet:digest-periodic` | 日次・週次・月次ダイジェスト |
| `logvalet:document-search` | Backlog ドキュメントをキーワードで横断検索 |

これらはエージェントが自律起動する（`/command` で明示呼び出ししない）。各スキルの `description` に豊富なトリガーワード（日本語・英語・別名）が埋め込まれており、Claude Code が自動発火する。

## 出力フォーマット

logvalet CLI は複数の出力形式に対応:

```bash
lv context PROJ-123 --output-format json     # JSON（デフォルト）
lv context PROJ-123 --output-format yaml     # YAML
lv context PROJ-123 --output-format markdown # Markdown
```

スキル内で `--output-format` フラグを使い分けて、Claude Code との連携を最適化する。

## データ送信に関する注意

Backlog 課題の内容（タイトル・説明・コメント等）は LLM（Claude）で分析処理を経由します。機密情報を含む課題のコンテキスト取得・分析には使用しないこと。

## トラブルシューティング

### `command not found: logvalet` / `command not found: lv`

logvalet CLI がインストールされていません。前提条件のインストール手順を実行してください:

```bash
brew install youyo/tap/logvalet
```

または PATH を確認:

```bash
which logvalet
echo $PATH
```

### 旧 logvalet plugin からの移行

以前 `/plugin install logvalet@<旧>` でインストール済みの場合、先に旧プラグインをアンインストールしてください:

```
/plugin uninstall logvalet@<旧>
```

その後、claude-plugins マーケットプレース経由で再インストール:

```
/plugin marketplace add youyo/claude-plugins
/plugin install logvalet@youyo
```

同名プラグインの重複インストールは予測不能な動作につながります。

### 認証エラー（API キー不正）

Backlog API キーが無効または期限切れの場合:

```bash
logvalet configure --init-profile <名前> --init-api-key <新APIキー>
```

認証情報を再設定し、試してください。

## ライセンス

MIT
