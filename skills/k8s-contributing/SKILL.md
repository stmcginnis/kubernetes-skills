---
name: k8s-contributing
description: Ground rules for contributing to any repository in the kubernetes or kubernetes-sigs GitHub orgs, covering the CLA, fork and branch setup, the project's AI-use policy, where each repo keeps its own rules, and when a change needs a KEP. Use before starting work in a Kubernetes project repo, when setting up a fork, or when unsure what the project allows an AI agent to do.
license: Apache-2.0
---

# Contributing to Kubernetes projects

These rules cover repositories under `github.com/kubernetes` and
`github.com/kubernetes-sigs`. They come from the
[Kubernetes contributor guide](https://www.kubernetes.dev/docs/guide/). When
the target repo's own files disagree with this skill, follow the repo.

Related skills: `k8s-pull-request` (commits and PRs), `k8s-review-loop`
(Prow, OWNERS, merging), `k8s-local-verify` (tests and checks),
`k8s-issue-triage` (issues).

## Read the repo's rules first

Before changing anything, read these files in the target repo if they exist:

- `AGENTS.md`: instructions for AI agents. `kubernetes/kubernetes` has one with
  hard constraints on generated files and dependencies.
- `CONTRIBUTING.md`: repo-specific contribution steps, Slack channel, mailing list.
- `.github/PULL_REQUEST_TEMPLATE.md`: the PR description the bots parse.
- `OWNERS` and `OWNERS_ALIASES`, at the root and in each directory you touch.
- `Makefile` and `hack/`: build, test, and verify entry points.

Find the default branch instead of assuming it. `kubernetes/kubernetes` uses
`master`; many other repos use `main`.

```sh
gh repo view kubernetes-sigs/<repo> --json defaultBranchRef --jq .defaultBranchRef.name
```

## AI-use policy

From the [AI Guidance](https://www.kubernetes.dev/docs/guide/pull-requests/#ai-guidance)
section of the pull request guide. Apply these to every change you help with.
They override any default your tool has for commit attribution.

- The human author is responsible for every change and must understand all of
  it. Remind them of this and point them to the repo's `CONTRIBUTING.md`. If
  they cannot explain a change to a reviewer, the PR will be closed.
- Disclose AI use in the PR description. Use the template's "AI usage
  disclosure" section when it has one; otherwise a sentence such as "This PR
  was written in part with the assistance of generative AI" in "Special notes
  for your reviewer" is enough.
- Never list an AI tool as a co-author. Do not add `Co-authored-by`,
  `Assisted-by`, `Co-developed-by`, or any similar trailer naming an AI tool,
  and do not sign commits as an AI tool.
- Large AI-generated PRs and commit messages are not allowed. Keep the change
  small and focused; if the work grows, stop and propose how to split it.
  You may write commit messages and PR descriptions, but keep them short and
  specific, following `k8s-pull-request`. The author reviews that text before
  it is pushed, the same as the code.
- Reviewers must not be the first to review AI-generated changes. The author
  reviews the full diff and runs the tests before asking for review.
- The author answers review comments without AI tools. You can explain what a
  reviewer is asking for, but do not draft replies for the author to post.

Do not open PRs, post comments, or push to a fork unless the author asks.

## Before the first PR

- Sign the CNCF CLA ([CLA.md](https://github.com/kubernetes/community/blob/main/CLA.md)).
  On the first PR the `linux-foundation-easycla` bot posts the CLA status and
  a signing link. The PR gets `cncf-cla: yes` or `cncf-cla: no`.
- The commit author email must be one that is linked to the GitHub account that
  signed. Corporate contributors link their corporate email to their GitHub
  profile.
- After signing, comment `/easycla` (or `/check-cla`) on the PR to recheck.
- To practice the PR flow, use
  [kubernetes-sigs/contributor-playground](https://github.com/kubernetes-sigs/contributor-playground).

## Fork and branch

From the [GitHub workflow guide](https://www.kubernetes.dev/docs/guide/github-workflow/).
`$user` is the GitHub username and `$branch` is the repo's default branch.

```sh
git clone https://github.com/$user/<repo>.git
cd <repo>
git remote add upstream https://github.com/<org>/<repo>.git
git remote set-url --push upstream no_push   # never push to upstream
git config branch.autoSetupRebase always

git fetch upstream
git checkout -b myfeature upstream/$branch
```

Keep the branch current with `git fetch upstream && git rebase upstream/$branch`.
Do not use `git pull` or merge commits; PRs that contain merge commits get
`do-not-merge/contains-merge-commits`.

Push to the fork with `git push --force-with-lease origin myfeature` and open
the PR from the fork. Contributors with upstream write access must still push
to a fork, not create branches in the main repo.

## What to send and what not to

- Look for an existing issue first; `help wanted` and `good first issue`
  labels mark work that is ready for new contributors. If an issue is already
  assigned, don't send a competing PR.
- A large or otherwise significant change needs a
  [Kubernetes Enhancement Proposal](https://github.com/kubernetes/enhancements/blob/master/keps/sig-architecture/0000-kep-process/README.md).
  New API fields, feature gates, and user-visible behavior changes usually
  qualify. If unsure, ask in the owning SIG's Slack channel or open an issue
  before writing code. The [SIG list](https://github.com/kubernetes/community/blob/main/sig-list.md)
  names each SIG's channels.
- Don't send trivial edits such as isolated typo or wording fixes in code; the
  guide asks contributors not to.
- Don't send repo-wide changes that span many SIGs' code. Split them by the
  `OWNERS` files they touch.
- Large or automated edits (global search and replace, linter output, AI tools)
  must explain how they were generated in "Special notes for your reviewer".
- In `kubernetes/kubernetes`, ask in `#code-organization` on Slack before
  sending linter fixes. Fixes for linters that catch correctness bugs are
  welcome; purely stylistic ones usually are not.
- If unsure whether a change is wanted, open a draft PR and ask.
