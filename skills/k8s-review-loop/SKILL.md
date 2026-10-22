---
name: k8s-review-loop
description: Move a kubernetes or kubernetes-sigs pull request through Prow review and merge, covering OWNERS reviewers and approvers, /lgtm and /approve, /ok-to-test, /retest, /hold, blocking labels, and updating the PR after review. Use when a Kubernetes PR is open and the author asks what a bot comment or label means, why the PR is not merging, which command to use, or how to update it after review.
license: Apache-2.0
---

# Review and merge for Kubernetes projects

Kubernetes repos merge through Prow and Tide, not the GitHub merge button.
Rules from the [pull request guide](https://www.kubernetes.dev/docs/guide/pull-requests/)
and the [OWNERS guide](https://www.kubernetes.dev/docs/guide/owners/). The
full command list is in [references/prow-commands.md](references/prow-commands.md).

The author writes their own replies to reviewers, without AI tools (see
`k8s-contributing`). You can explain review comments, Prow output, and
failing tests, and make the code changes the author decides on.

## The path to merge

1. The PR is opened. EasyCLA checks the CLA (`cncf-cla: yes` / `cncf-cla: no`).
   Blunderbuss requests reviewers from the `OWNERS` files of the changed paths.
2. For authors who are not org members, the PR gets `needs-ok-to-test`. An org
   member comments `/ok-to-test` to start CI. Org members skip this step.
3. Presubmit tests run. Anyone can `/retest` failed jobs on a trusted PR.
4. A reviewer from `OWNERS` comments `/lgtm` (or approves in the GitHub review
   UI). The author cannot `/lgtm` their own PR.
5. The author asks an approver for the changed paths to review:
   `/assign @approver`. The approver comments `/approve`. When every changed
   path is covered, the bot adds `approved`.
6. Tide merges once the PR has `lgtm` and `approved`, required tests pass, and
   no blocking label is present. Tests run again against the latest base
   before merge.

In some repos, including `kubernetes/kubernetes`, an approver's `/lgtm` also
counts as approval, and an author who is an approver for the changed paths
approves implicitly. Use explicit `/lgtm` and `/approve` anyway.

## Finding reviewers and approvers

For each changed file, walk up from its directory to the repo root. The
nearest `OWNERS` file applies, and so do its parents unless it sets
`options: no_parent_owners: true`.

```yaml
approvers:      # can /approve
  - alice
reviewers:      # can /lgtm
  - carol
  - sig-foo     # alias defined in OWNERS_ALIASES
labels:         # applied to PRs touching this directory
  - sig/foo
emeritus_approvers:   # former approvers; do not assign
  - dave
```

A `filters:` map applies different owners by filename regex. Aliases are
defined in the root `OWNERS_ALIASES`. Pick an approver who appears in the
`OWNERS` files of most of the changed paths, so one approval covers them.

## Blocking labels

| Label | Cause | Fix |
|---|---|---|
| `cncf-cla: no` | CLA not signed or commit email not linked | Sign, link the email, comment `/easycla` |
| `needs-ok-to-test` | Author is not an org member | Wait for a member's `/ok-to-test` |
| `needs-rebase` | Conflicts with the base branch | Rebase on `upstream/<default-branch>` and force-push |
| `do-not-merge/hold` | Someone commented `/hold` | Resolve their concern; `/hold cancel` |
| `do-not-merge/work-in-progress` | Draft PR or `WIP` title | Mark ready, drop `WIP` |
| `do-not-merge/needs-kind` | No `/kind` | Add `/kind <kind>` |
| `do-not-merge/needs-sig` | No SIG label | Add `/sig <name>` |
| `do-not-merge/release-note-label-needed` | Empty `release-note` block | Fill the block or write `NONE` |
| `do-not-merge/invalid-commit-message` | Closing keyword or `@mention` in a commit | Reword the commit |
| `do-not-merge/contains-merge-commits` | Merge commit in history | Rebase to drop it |
| `do-not-merge/invalid-owners-file` | Bad `OWNERS` change | Fix the YAML; `/verify-owners` |
| `do-not-merge/blocked-paths` | Touches a path the repo blocks | Ask a maintainer |
| `do-not-merge/cherry-pick-not-approved` | Release-branch PR without release team approval | Follow the repo's cherry-pick process |

Labels and their exact names vary by repo; check the repo's labels with
`gh label list --repo <org>/<repo>`.

## Updating the PR after review

- Rebase on the latest base rather than merging it in:
  `git fetch upstream && git rebase upstream/<default-branch>`.
- Follow `k8s-pull-request` for squashing. `kubernetes/kubernetes` wants fixes
  squashed into the commits they fix before each push.
- Push with `git push --force-with-lease origin <branch>`.
- Pushing new commits usually removes `lgtm`; the reviewer re-applies it.
- Answer every reasonable review comment. The author may push back with
  reasons and does not have to fix every point raised; reviewers may ask for
  a follow-up PR instead.

## Failing tests

- Read the job output from the Prow status link on the PR. `/test ?` lists the
  jobs for the PR; `/test <job>` runs one.
- If a failure is unrelated to the change, search for an existing
  `kind/flake` or `kind/failing-test` issue before retrying. `/retest` reruns
  failed jobs.
- Don't retest repeatedly to get a flaky job to pass. Report the flake.

## Getting a review

- Reviews take time, particularly near a release freeze. Comment `PTAL` and
  mention the assigned reviewer if a PR has sat for a while, or ask in the
  owning SIG's Slack channel or `#pr-reviews`.
- PRs with no activity are marked `lifecycle/stale`, then
  `lifecycle/rotten`, then closed by `k8s-triage-robot`. Comment
  `/remove-lifecycle stale` while the PR is still active.
