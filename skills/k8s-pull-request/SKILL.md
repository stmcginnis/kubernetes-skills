---
name: k8s-pull-request
description: Prepare commits and a pull request for a kubernetes or kubernetes-sigs repository, covering commit message rules, splitting and squashing commits, the PR template, /kind and /sig labels, release notes, and the AI usage disclosure. Use when committing in a Kubernetes project, checking a commit message, or writing or opening a PR.
license: Apache-2.0
---

# Commits and pull requests for Kubernetes projects

Rules from the [pull request guide](https://www.kubernetes.dev/docs/guide/pull-requests/)
and the [release notes guide](https://www.kubernetes.dev/docs/guide/release-notes/).
Read `k8s-contributing` first for the AI-use policy; it limits what you may
write on the author's behalf.

## Commit messages

Follow these rules when writing a commit message, and when checking one the
author wrote, report each violation:

- Subject of 50 characters or less, never more than 72.
- Subject capitalized, unless it starts with a lowercase identifier such as a
  function or component name.
- Subject in the imperative mood ("Fix x error in y", "Add foo to bar"), with no
  trailing period. Not "Fixed", "Added", or "Fixing".
- Optional area prefix: `kube-proxy: add a test case for HostnameOverride`,
  `etcd: update default server to 3.4.7`, `cleanup: remove unused script foo`.
- One blank line between subject and body. Body wrapped at 72 characters and
  explains what changed and why.
- No GitHub closing keywords with an issue reference: `close`, `closes`,
  `closed`, `fix`, `fixes`, `fixed`, `resolve`, `resolves`, `resolved`. The bot
  adds `do-not-merge/invalid-commit-message`. Put issue links in the PR
  description instead.
- No `#123` references to PRs or issues and no `@mentions`.
- No AI attribution trailers (`Co-authored-by`, `Assisted-by`, and similar).

## Keep commit messages and PR descriptions short

The AI guidance does not allow large AI-generated commit messages. Reviewers
read every word, so write only what they need:

- State what changed and why in as few sentences as that takes. A small fix
  needs a subject line and one or two sentences of body.
- Don't walk through the diff file by file, list every function touched, or
  describe tests the reviewer can read in the diff.
- No headings, bold text, or bulleted summaries in a commit message.
- Cut filler: "This commit...", "comprehensive", "robust", "ensures",
  "enhances", a body that repeats the subject, and closing summaries.
- Use the author's own description of the problem when they gave one.
- In a PR description, answer each template section in a sentence or a few.
  Leave "Special notes for your reviewer" to things a reviewer needs, such as
  how generated edits were made or how the change was tested by hand.

```text
kubelet: skip image GC when disk stats are missing

The image garbage collector divided by capacity without checking that
cadvisor returned disk stats, which panics on nodes where stats are
not yet available. Return early and retry on the next GC cycle.
```

## Splitting and squashing

- Split work into commits at logical break points. Several small PRs are
  usually better than one PR with many commits.
- Put unrelated fixes in their own PR. Don't touch code owned by several SIGs
  in one PR; split by `OWNERS` files.
- Remove merge commits and WIP, typo, and "address review" commits before
  merge. Separate commits that each stand on their own can stay.
- `kubernetes/kubernetes` `AGENTS.md`: when responding to review, squash
  fixes back into the commits they fix. Never push a PR containing
  known-incorrect commits followed by fixups. Other repos may prefer fixup
  commits during review and a squash at the end; follow the repo's guidance
  or the reviewer's request.

Squash without an interactive editor:

```sh
git commit --fixup=<sha-of-commit-being-fixed>
GIT_SEQUENCE_EDITOR=: git rebase -i --autosquash upstream/<default-branch>
git push --force-with-lease origin <branch>
```

A reviewer or author can instead comment `/label tide/merge-method-squash` to
have Tide squash all commits at merge. Reviewers may still ask for a manual
squash.

## PR title

Same rules as a commit subject. For unfinished work, open a draft PR or
prefix the title with `WIP` (adds `do-not-merge/work-in-progress`).

## PR description

Use the repo's `.github/PULL_REQUEST_TEMPLATE.md` when present. Keep its
headings; the bots parse them. Fill each section and delete the instructional
HTML comments once handled. The `kubernetes/kubernetes` template has these
sections:

1. "What type of PR is this?" Add a `/kind` command on its own line. Pick one
   of `bug`, `cleanup`, `dependency`, `documentation`, `feature`. Add any of
   `api-change`, `deprecation`, `failing-test`, `flake`, `regression` that
   apply. A PR without a kind gets `do-not-merge/needs-kind`.
2. "What this PR does / why we need it." The problem, then the approach.
3. "Which issue(s) this PR is related to." `Fixes #<n>` closes the issue on
   merge; don't use `Fixes` for `failing-test` or `flake` PRs. Link issues in
   other repos by URL. Link the KEP issue when there is one:
   `KEP: https://github.com/kubernetes/enhancements/issues/<n>`. Write `N/A`
   when there is no issue.
4. "Special notes for your reviewer." How large or automated edits were
   generated, anything unusual about testing, and the AI disclosure when the
   template has no section for it.
5. "Does this PR introduce a user-facing change?" The `release-note` block.
   See below.
6. "AI usage disclosure." `YES` or `NO`; if yes, a short description of how
   AI was used.

Add `/sig <name>` (for example `/sig node`, `/sig cluster-lifecycle`) when the
`OWNERS` files of the touched paths don't apply a SIG label; a PR with no SIG
gets `do-not-merge/needs-sig`. Prow commands must be on their own line at the
start of the line.

When the repo has no template, write the same content under plain headings:
what and why, related issues, reviewer notes, and the AI disclosure.

## Release notes

Required in `kubernetes/kubernetes` for user-visible changes. Other repos use
them only when their PR template has a `release-note` block. Without a note the
PR gets `do-not-merge/release-note-label-needed`.

````markdown
```release-note
Fixed a bug that prevents dry-run from being honored for the pod/eviction sub-resource.
```
````

- Write `NONE` in the block for changes with no user-facing effect: tests,
  build and CI changes, and fixes for bugs that never shipped in a release.
  `/release-note-none` also works but is deprecated.
- Use past tense and say what kind of change it is: Added, Changed, Fixed,
  Removed, or Deprecated. Name the affected component, flag, field, or API,
  and link docs when they help.
- If users must do something when upgrading, include the string
  `action required` (any case); the PR gets `release-note-action-required`.
- Cherry-pick PRs inherit the note from the original PR.
- An org member can replace the note later with `/release-note-edit`.

## Opening the PR

Open the PR only when the author asks, after they have reviewed the full diff
and the checks in `k8s-local-verify` pass. Show them the title and description
first.

```sh
gh pr create --repo <org>/<repo> --base <default-branch> \
  --head <user>:<branch> --title "<title>" --body-file <file> [--draft]
```

Then see `k8s-review-loop` for what happens next.
