# スキルのインストール

[English](INSTALL_SKILL.md)

```bash
# 任意の Agent（Claude Code、Cursor、Copilot など）
npx skills add Agents365-ai/365-skills -g

# Claude Code のみ
> /plugin marketplace add Agents365-ai/365-skills
> /plugin install drawio
```

手動インストール —— あなたの Agent skills ディレクトリにクローン：

```bash
git clone https://github.com/Agents365-ai/drawio-skill.git ~/.claude/skills/drawio-skill
```

よく使うパス：`~/.claude/skills/`（Claude Code）、`~/.config/opencode/skills/`（Opencode）、`~/.openclaw/skills/`（OpenClaw）、`~/.agents/skills/`（Codex）。同時に [SkillsMP](https://skillsmp.com/skills/agents365-ai-drawio-skill-skill-md) と [ClawHub](https://clawhub.ai/agents365-ai/drawio-pro-skill) にインデックスされています。

## アップデート

Skill は各会話で初めて使われる際に自動でアップデートをチェックします（24 時間スロットル）。新バージョンがある場合、Agent は返信に 1 行の通知を出力します。アップデートを適用するには：

```bash
cd <あなたのインストールパス>/drawio-skill && git pull
```

チェックは読み取り専用、セルフスロットル、すでに最新の場合・オフラインの場合・git インストールでない場合はサイレントに終了し、ワークフローをブロックしたり遅らせたりしません。

プラグインマーケット経由でインストールしたユーザーは 365-skills アンブレラ経由で自動アップデートされます。パッケージマネージャ経由でインストールしたユーザーは、対応するコマンドで直接アップデートします：

```bash
# Claude Code プラグイン
/plugin update drawio

# OpenClaw
clawhub update drawio-pro-skill

# SkillsMP
skills update drawio-skill
```
