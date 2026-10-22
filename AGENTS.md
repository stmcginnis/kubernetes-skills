# Working on kubernetes-skills

This repo holds [Agent Skills](https://agentskills.io/specification) for
contributing to Kubernetes projects. Each skill is `skills/<name>/SKILL.md`,
with long reference material in `skills/<name>/references/`.

## Rules for skills

- Frontmatter uses only fields from the Agent Skills spec: `name`,
  `description`, `license`, `compatibility`, `metadata`. Tool-specific fields
  such as `allowed-tools` or `when_to_use` are ignored by other tools and
  rejected by claude.ai uploads.
- `name` is lowercase letters, digits, and single hyphens, at most 64
  characters, and matches the directory name. Names start with `k8s-`.
- `description` is at most 1024 characters and says what the skill covers and
  when to use it. Agents choose skills from this text alone.
- Keep `SKILL.md` under 500 lines. Move tables and long lists to
  `references/` and link them with a relative path.
- Each skill must make sense when installed alone. Refer to other skills by
  name, not by path.
- Every rule comes from an upstream source: the
  [contributor guide](https://www.kubernetes.dev/docs/guide/), a repo's
  `AGENTS.md`, `CONTRIBUTING.md`, or PR template, or the Prow plugin help.
  Link the source. Don't add rules from memory or general practice.
- Cover all of `kubernetes` and `kubernetes-sigs`. When a rule only applies to
  `kubernetes/kubernetes`, say so. Tell the agent to check the target repo
  rather than assuming its default branch, make targets, or labels.
- Keep the AI-use policy in `k8s-contributing` intact, and keep the other
  skills consistent with it.
- Nothing specific to one company or employer.

Prose follows the Kubernetes docs style: plain, imperative, specific.

## Checks

```sh
for d in skills/*/; do uvx --from skills-ref agentskills validate "$d"; done   # spec validation
claude plugin validate .                                                   # Claude plugin manifests
```

CI runs both on every pull request.

## Adding a skill

1. Create `skills/k8s-<topic>/SKILL.md`.
2. Add a row to the skill table in `README.md`.
3. Run the checks.

The plugin manifests in `.claude-plugin/` pick up every directory under
`skills/` automatically.
