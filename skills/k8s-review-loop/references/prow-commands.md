# Prow commands

Commands are comments on an issue or PR. Each must be on its own line, at the
start of the line. Several can go in one comment. Not every repo enables every
plugin; the live list for a repo is at <https://prow.k8s.io/command-help>
(filter by repo). The table below was taken from that page's data on
2026-10-22.

## Review and merge

| Command | Who can use it | Effect |
|---|---|---|
| `/lgtm`, `/lgtm cancel`, `/remove-lgtm` | Reviewers in `OWNERS`; the author can cancel | Adds or removes `lgtm` |
| `/approve`, `/approve cancel`, `/remove-approve` | Approvers in `OWNERS` | Approves the paths the user owns |
| `/approve no-issue` | Approvers | Approves where the repo requires a linked issue |
| `/hold`, `/hold cancel`, `/unhold` | Anyone | Adds or removes `do-not-merge/hold` |
| `/assign [@user]`, `/unassign [@user]` | Anyone; targets must be org members or repo collaborators | Assigns the PR or issue |
| `/cc @user`, `/uncc @user` | Anyone | Requests a review |
| `/auto-cc` | Anyone | Re-requests reviewers from `OWNERS` |
| `/retitle <title>` | Collaborators | Changes the title |
| `/label tide/merge-method-squash` | Anyone, when configured | Tide squashes on merge |

## Tests

| Command | Who can use it | Effect |
|---|---|---|
| `/ok-to-test`, `/ok-to-test cancel` | Org members | Marks an external PR as trusted and starts tests |
| `/retest` | Anyone, on a trusted PR | Reruns failed jobs |
| `/test ?` | Anyone, on a trusted PR | Lists available jobs |
| `/test <job>`, `/test all` | Anyone, on a trusted PR | Runs a job or all jobs |
| `/skip` | Anyone | Clears stale statuses of non-blocking jobs |
| `/override <context>` | Repo admins | Forces a status to pass |

## Labels

| Command | Who can use it | Effect |
|---|---|---|
| `/kind <kind>`, `/remove-kind <kind>` | Anyone | `kind/*` label |
| `/sig <sig>`, `/remove-sig <sig>` | Anyone | `sig/*` label |
| `/area <area>`, `/wg <wg>`, `/committee <c>` | Anyone | `area/*`, `wg/*`, `committee/*` |
| `/priority <p>` | Anyone | `priority/*` label |
| `/triage accepted` | Org members | `triage/accepted` |
| `/triage needs-information`, `/triage duplicate`, `/triage not-reproducible` | Anyone | `triage/*` label |
| `/help`, `/remove-help` | Anyone | `help wanted` |
| `/good-first-issue`, `/remove-good-first-issue` | Anyone | `good first issue` (also `help wanted`) |
| `/lifecycle frozen`, `/remove-lifecycle stale` | Anyone | Lifecycle labels |
| `/release-note-none` | Author and org members | `release-note-none` (deprecated; write `NONE` in the block) |
| `/release-note-edit` | Org members | Replaces the `release-note` block |
| `/milestone <v>`, `/milestone clear` | Milestone maintainers | Sets the milestone |
| `/stage alpha\|beta\|stable` | Anyone | `stage/*` label |

## Issues and housekeeping

| Command | Who can use it | Effect |
|---|---|---|
| `/close`, `/close not-planned`, `/reopen` | Author and collaborators | Closes or reopens |
| `/transfer <repo>` | Org members | Moves an issue to another repo in the org |
| `/link-issue <issue>` | Org members | Links an issue to the PR |
| `/easycla`, `/check-cla` | Anyone | Rechecks the CLA |
| `/check-required-labels` | Anyone | Rechecks required labels |
| `/verify-owners` | Anyone | Validates changed `OWNERS` files |
