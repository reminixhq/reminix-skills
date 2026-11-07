# Reminix agent skill

Teaches an AI agent how to use Reminix: the workspace, every operation, approvals and errors. It is generated from Reminix's API after each release, so it always matches.

## Install

Any agent (Claude Code, Codex, Cursor, GitHub Copilot and others):

```bash
npx skills add reminixhq/reminix-skills
```

With the GitHub CLI:

```bash
gh skill install reminixhq/reminix-skills reminix
```

Claude Code, as a plugin with Reminix's MCP server:

```text
/plugin marketplace add reminixhq/reminix-skills
/plugin install reminix@reminix-skills
```

In Claude or ChatGPT, download [the skill](https://reminix.com/docs/skill/reminix.zip) and add it in the Skills section of the settings. More: [Build with AI agents](https://reminix.com/docs/agents/).

This repository is written by Reminix's release process; changes made here are replaced. Report a problem in an issue.
