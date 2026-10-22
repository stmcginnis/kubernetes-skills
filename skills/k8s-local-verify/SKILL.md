---
name: k8s-local-verify
description: Build, test, and verify a change locally before sending it to a kubernetes or kubernetes-sigs repository, and follow the project's Go coding and testing conventions. Use after editing code in a Kubernetes project and before committing or pushing, when choosing which tests to run, or when a verify, lint, or codegen CI job fails.
license: Apache-2.0
---

# Local verification for Kubernetes projects

The author must review and test a change before reviewers see it (see
`k8s-contributing`). Run the checks below, then tell the author exactly which
ones ran, which passed, and which you skipped.

## Find the repo's checks

Repos differ, so look before running anything:

- `make help` or the `Makefile` for targets such as `test`, `verify`, `lint`,
  `generate`, `manifests`, and `update`.
- `hack/verify-*.sh` and `hack/update-*.sh`. Each `verify` script usually has an
  `update` counterpart that fixes what it checks.
- The repo's presubmit jobs, which are the checks CI will run. They are defined
  in [kubernetes/test-infra](https://github.com/kubernetes/test-infra) under
  `config/jobs/<org>/<repo>/`, and some repos also use `.github/workflows/`.

Run the narrowest check that covers the change first, then the broader ones.

## kubernetes/kubernetes

From the [pull request guide](https://www.kubernetes.dev/docs/guide/pull-requests/)
and the repo's `AGENTS.md`:

```sh
make test WHAT=./pkg/kubelet GOFLAGS=-v               # unit tests, one package
make test-integration WHAT=./test/integration/scheduler
make update                                          # all generators and formatters
make verify                                          # all checks; takes 30-40 minutes
hack/verify-<name>.sh                                # one check
```

- Generated files are read-only. Never hand-edit `zz_generated.*` or
  `generated.pb.go`; run `make update`.
- `go.mod` and `go.work` are generated. Use `hack/pin-dependency.sh` and
  `hack/update-vendor.sh`. Never run `go mod tidy`.
- `staging/src/k8s.io/` is the source of truth for the `k8s.io/*` libraries.
  Code in staging must never import `k8s.io/kubernetes`.
- Every `.go` file needs the license header from
  `hack/boilerplate/boilerplate.go.txt`.
- Lint configuration is in `hack/golangci.yaml`.

## Coding conventions

From the [coding conventions](https://www.kubernetes.dev/docs/guide/coding-convention/):

- Follow [Effective Go](https://go.dev/doc/effective_go),
  [Go Code Review Comments](https://go.dev/wiki/CodeReviewComments), the
  [API conventions](https://github.com/kubernetes/community/blob/main/contributors/devel/sig-architecture/api-conventions.md),
  and the [logging conventions](https://github.com/kubernetes/community/blob/main/contributors/devel/sig-instrumentation/logging.md).
- Package names are lowercase, one word, no underscores or dashes, and match
  the directory. Avoid stutter: `storage.Interface`, not
  `storage.StorageInterface`. Avoid `util` packages.
- Command-line flags use dashes, not underscores.
- Name a mutex `lock` and never embed it: `lock sync.Mutex`.
- All filenames are lowercase. Go source files and directories use
  underscores; documentation files and directories use dashes.
- Third-party code is managed with Go modules. Forked code goes under
  `third_party/forked` with its license.

## Tests

- New packages and most significant new functionality need unit tests. Don't
  add trivial tests only to raise coverage.
- Prefer table-driven tests.
- Significant features also need integration (`test/integration`) or e2e
  (`test/e2e`) tests.
- Unit tests must pass on macOS and Windows as well as Linux.
- Don't depend on Docker Hub images; use the community registry
  (`registry.k8s.io`) or Google Cloud Artifact Registry.
- Poll for a condition with a timeout instead of sleeping a fixed time. Don't
  wait one second and expect a pod to be running.
