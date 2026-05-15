# スキルのインストール

[English](INSTALL_SKILL.md)

```bash
# 任意の Agent（Claude Code、Cursor、Copilot など）
npx skills add Agents365-ai/365-skills -g

# Claude Code のみ
> /plugin marketplace add Agents365-ai/365-skills
> /plugin install drawio
```

手動インストール —— Agent のスキルディレクトリにクローンします：

```bash
git clone https://github.com/Agents365-ai/drawio-skill.git ~/.claude/skills/drawio-skill
```

よく使うパス：`~/.claude/skills/`（Claude Code）、`~/.config/opencode/skills/`（Opencode）、`~/.openclaw/skills/`（OpenClaw）、`~/.agents/skills/`（Codex）。[SkillsMP](https://skillsmp.com/skills/agents365-ai-drawio-skill-skill-md) と [ClawHub](https://clawhub.ai/agents365-ai/drawio-pro-skill) にも登録されています。

## アップデート

Skill は会話で初めて使われたタイミングで自動的にアップデートをチェックします（24 時間に 1 回）。新バージョンがある場合は、Agent が返信の中で 1 行通知します。アップデートを適用するには：

```bash
cd <インストールパス>/drawio-skill && git pull
```

このチェックは読み取り専用かつ自動でスロットリングされ、最新の場合・オフラインの場合・git インストールでない場合は何も表示されません。ワークフローを止めたり遅くしたりすることもありません。

プラグインマーケット経由でインストールした場合は、365-skills の傘下で自動アップデートされます。パッケージマネージャ経由でインストールした場合は、各ツールのアップデートコマンドを使ってください：

```bash
# Claude Code プラグイン
/plugin update drawio

# OpenClaw
clawhub update drawio-pro-skill

# SkillsMP
skills update drawio-skill
```
