---
name: k8s-issue-triage
description: File and triage issues in kubernetes or kubernetes-sigs repositories, covering duplicate searches, issue templates, kind, sig, and priority labels, the /triage, /help, and /good-first-issue commands, and stale lifecycle handling. Use when writing a bug report or feature request for a Kubernetes project, or when asked to triage or label a Kubernetes issue.
license: Apache-2.0
---

# Issues in Kubernetes projects

From the [issue triage guide](https://www.kubernetes.dev/docs/guide/issue-triage/),
written for `kubernetes/kubernetes`. Other repos use the same bots and label
families but may skip parts of the process. Commands are listed in
`k8s-review-loop/references/prow-commands.md`.

Post issues and comments only when the author asks, after they have read the
text.

## Before filing

- Search open and closed issues for duplicates:
  `gh search issues --repo <org>/<repo> "<keywords>"`.
- Report security vulnerabilities privately, following the
  [security disclosure process](https://kubernetes.io/docs/reference/issues-security/security/),
  never in a public issue.
- Send support questions ("how do I...") to
  [discuss.kubernetes.io](https://discuss.kubernetes.io) or the Kubernetes
  Slack, not to an issue. Triagers close them with `kind/support`.
- A feature that changes APIs or user-visible behavior usually needs a KEP
  (see `k8s-contributing`). An issue is still a good place to start the
  discussion.

## Filing

- Use the repo's template from `.github/ISSUE_TEMPLATE/`.
- For bugs, give the version (`kubectl version`), environment, exact steps to
  reproduce, expected and actual behavior, and relevant logs trimmed to the
  failure.
- Add `/kind bug` (or `feature`, `documentation`, `failing-test`, `flake`) and
  `/sig <name>` lines if the template doesn't set them. Commands go on their
  own lines at the start of the line.

## Triage

New issues get `needs-triage`. A triager:

1. Checks it is in the right repo (`/transfer <repo>` if not) and not a
   duplicate (`/triage duplicate`, then `/close`).
2. Asks for missing details with `/triage needs-information`. Bugs with no
   response for 20 days may be closed.
3. Adds the owning SIG with `/sig <name>`. To notify a SIG team, mention
   `@kubernetes/sig-<name>-<suffix>` where the suffix is `bugs`,
   `feature-requests`, `pr-reviews`, `test-failures`, or `proposals`.
4. Sets priority with `/priority <level>`:
   - `critical-urgent`: must be someone's top priority right now.
   - `important-soon`: must be staffed now or very soon, ideally for the
     next release.
   - `important-longterm`: important, but may not be staffed and may take
     several releases.
   - `backlog`: wanted, but below the `important-*` levels.
   - `awaiting-more-evidence`: possibly useful, not enough support yet.
5. Accepts it with `/triage accepted` (org members only), which removes
   `needs-triage`.

Mark work that is ready for outside contributors with `/help`, and
`/good-first-issue` when it is small and well described.

## Assignment and lifecycle

- Comment `/assign` to take an issue you will work on. Don't take an issue
  someone else is assigned to without asking them.
- Issues with no activity for 90 days get `lifecycle/stale` from
  `k8s-triage-robot`, then `lifecycle/rotten`, then are closed. Comment
  `/remove-lifecycle stale` to keep one open, or `/lifecycle frozen` to exempt
  an issue that is accepted but long-term.
