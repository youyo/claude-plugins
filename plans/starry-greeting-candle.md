# スキル picker の表示に plugin prefix を付ける

## Context

ユーザーが `/copilot` や `/logvalet` と入力したときの slash autocomplete に、現状は `/code-review` `/risk` のようにスキル単体名しか出ない。同名のスキルが他プラグインにあると区別できず、どのプラグインに属するか一目でわからないため、`/copilot:code-review` `/logvalet:risk` のように **plugin 名を prefix として表示させたい**。

picker の表示名は各 `SKILL.md` の frontmatter `name:` フィールドから来ているため、ここを `<plugin>:<skill>` の形式に書き換えれば実現できる（loaded skills 一覧に表示されている `copilot:code-review` 等の表現と一致させる）。

## 対象ファイル（19 件）

### copilot プラグイン（5件）
- `plugins/copilot/skills/code-review/SKILL.md` — `name: code-review` → `name: copilot:code-review`
- `plugins/copilot/skills/plan-review/SKILL.md` — `name: plan-review` → `name: copilot:plan-review`
- `plugins/copilot/skills/adversarial-review/SKILL.md` — `name: adversarial-review` → `name: copilot:adversarial-review`
- `plugins/copilot/skills/pr-review/SKILL.md` — `name: pr-review` → `name: copilot:pr-review`
- `plugins/copilot/skills/web-research/SKILL.md` — `name: web-research` → `name: copilot:web-research`

### logvalet プラグイン（14件）
- `plugins/logvalet/skills/context/SKILL.md` — `name: context` → `name: logvalet:context`
- `plugins/logvalet/skills/health/SKILL.md` — `name: health` → `name: logvalet:health`
- `plugins/logvalet/skills/decisions/SKILL.md` — `name: decisions` → `name: logvalet:decisions`
- `plugins/logvalet/skills/digest-periodic/SKILL.md` — `name: digest-periodic` → `name: logvalet:digest-periodic`
- `plugins/logvalet/skills/my-next/SKILL.md` — `name: my-next` → `name: logvalet:my-next`
- `plugins/logvalet/skills/intelligence/SKILL.md` — `name: intelligence` → `name: logvalet:intelligence`
- `plugins/logvalet/skills/issue-create/SKILL.md` — `name: issue-create` → `name: logvalet:issue-create`
- `plugins/logvalet/skills/draft/SKILL.md` — `name: draft` → `name: logvalet:draft`
- `plugins/logvalet/skills/spec-to-issues/SKILL.md` — `name: spec-to-issues` → `name: logvalet:spec-to-issues`
- `plugins/logvalet/skills/logvalet/SKILL.md` — `name: logvalet` → `name: logvalet:logvalet`
- `plugins/logvalet/skills/report/SKILL.md` — `name: report` → `name: logvalet:report`
- `plugins/logvalet/skills/my-week/SKILL.md` — `name: my-week` → `name: logvalet:my-week`
- `plugins/logvalet/skills/triage/SKILL.md` — `name: triage` → `name: logvalet:triage`
- `plugins/logvalet/skills/risk/SKILL.md` — `name: risk` → `name: logvalet:risk`

## 手順

1. **先行検証**: まず `plugins/copilot/skills/code-review/SKILL.md` の 1 ファイルだけを書き換える
2. `/reload-plugins` で再読み込みし、`/copilot` 入力時の picker に `/copilot:code-review` と表示されることを確認
3. **問題なければ残り 18 件を一括書き換え**（colon が schema で受け入れられない場合は中断してハイフン代替案 `copilot-code-review` 等に切り替え）
4. 再度 `/reload-plugins` で全件確認

## 触らない箇所

- 各 SKILL.md 本文の `# copilot-code-review` といった見出しは触らない（picker 表示には影響しない）
- `description:` も触らない（trigger word の発火動作は維持）
- `.claude-plugin/plugin.json` は変更不要（auto-prefix の発生源ではないため）
- `.claude-plugin/marketplace.json` も変更不要

## 検証

- `/copilot` と入力して picker の各行が `/copilot:<name>` 形式で表示される
- `/logvalet` と入力して picker の各行が `/logvalet:<name>` 形式で表示される
- skills 一覧（system-reminder の available skills）でも従来どおり認識される
- 自然言語 trigger（例: 「コードレビューして」）からの自律発火が以前と同じく動く

## リスク

- Claude Code の skill name schema が `:` を許容しない可能性がある。先行検証ステップで早期に判定し、不可ならハイフン区切り（例: `copilot-code-review`）にフォールバック方針を選び直す。
