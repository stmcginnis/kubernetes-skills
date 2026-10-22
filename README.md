# kubernetes-skills

Agent skills for contributing to repositories in the
[kubernetes](https://github.com/kubernetes) and
[kubernetes-sigs](https://github.com/kubernetes-sigs) GitHub orgs. They follow
the [Agent Skills](https://agentskills.io) format, so they work in Claude Code,
Codex, OpenCode, GitHub Copilot, Gemini CLI, Cursor, and any other tool that
reads `SKILL.md` files.

The content comes from the
[Kubernetes contributor guide](https://www.kubernetes.dev/docs/guide/). The
skills apply the project's
[AI guidance](https://www.kubernetes.dev/docs/guide/pull-requests/#ai-guidance):
the human author stays responsible for the change, writes the commit messages
and review replies, and discloses AI use in the PR. Agents using these skills
will not add AI co-author trailers.

| Skill | Use it for |
|---|---|
| [`k8s-contributing`](skills/k8s-contributing/SKILL.md) | CLA, fork setup, AI-use policy, repo rules, when a KEP is needed |
| [`k8s-pull-request`](skills/k8s-pull-request/SKILL.md) | Commit message rules, squashing, PR template, `/kind`, release notes |
| [`k8s-review-loop`](skills/k8s-review-loop/SKILL.md) | Prow commands, OWNERS, `/lgtm` and `/approve`, blocking labels, flakes |
| [`k8s-local-verify`](skills/k8s-local-verify/SKILL.md) | `make verify` and friends, generated code, Go and test conventions |
| [`k8s-issue-triage`](skills/k8s-issue-triage/SKILL.md) | Filing issues, triage labels and commands, lifecycle |

## Install

### Claude Code

```text
/plugin marketplace add stmcginnis/kubernetes-skills
/plugin install k8s-contributor@kubernetes-skills
```

The skills are then available as `/k8s-contributor:<skill>` and load
automatically when relevant.

### GitHub Copilot CLI

```sh
copilot plugin marketplace add stmcginnis/kubernetes-skills
copilot plugin install k8s-contributor@kubernetes-skills
```

### Codex, OpenCode, Copilot in VS Code, Cursor, and others

Use the [`skills`](https://github.com/vercel-labs/skills) installer and pick
the agent:

```sh
npx skills add stmcginnis/kubernetes-skills -a codex      # or opencode, github-copilot, cursor, gemini-cli
npx skills add stmcginnis/kubernetes-skills -a codex -g   # user-wide instead of the current project
```

Or clone the repo and link the skills into a directory your tool reads:

```sh
git clone https://github.com/stmcginnis/kubernetes-skills.git
mkdir -p ~/.agents/skills
ln -s "$PWD"/kubernetes-skills/skills/* ~/.agents/skills/
```

| Tool | User-level skill directory |
|---|---|
| Codex | `~/.agents/skills` |
| OpenCode | `~/.config/opencode/skills` or `~/.agents/skills` |
| GitHub Copilot | `~/.copilot/skills` or `~/.agents/skills` |
| Cursor | `~/.cursor/skills` or `~/.agents/skills` |
| Gemini CLI | `~/.gemini/skills` or `~/.agents/skills` |
| Claude Code | `~/.claude/skills` |

### Gemini CLI

```sh
gemini skills install https://github.com/stmcginnis/kubernetes-skills --path skills/k8s-contributing
```

Repeat for each skill.

### Claude.ai, ChatGPT, and other upload-based tools

Zip a single skill directory (for example `skills/k8s-pull-request/`) and
upload it as a custom skill.

## Contributing

See [AGENTS.md](AGENTS.md) for how skills in this repo are written and
checked.

## License

[Apache License 2.0](LICENSE)
