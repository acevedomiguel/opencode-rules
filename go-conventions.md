# Go conventions

Apply these conventions together with the repository's own architecture and
compatibility requirements. Check `go.mod` before using language or standard
library features.

## Formatting, packages, and naming

- Run `gofmt` on changed Go files. Keep general files consistent with the
  repository's `.editorconfig`.
- Use the Go version declared by `go.mod`; do not duplicate toolchain versions
  across local and CI configuration.
- Organize packages around cohesive behavior and clear ownership. Keep public
  APIs small and use `internal/` for implementation details that consumers must
  not depend on.
- Keep transport, persistence, and user-interface types out of pure domain
  packages unless the project explicitly chooses another architecture.
- Use short, idiomatic package names without underscores or generic stuttering.
  Avoid repeating the package name in exported identifiers.
- Use established initialisms consistently (`ID`, `URL`, `HTTP`), concise
  receiver names, and descriptive names at wider scopes.
- Document exported behavior, including errors, nil semantics, concurrency
  guarantees, and deprecation where relevant.

## Functions, types, and interfaces

- Prefer ordinary functions, structs, slices, and maps before generics or
  design patterns. Use generics for a real shared type relationship.
- Make zero values useful where practical. Use constructors when creation
  requires validation, dependencies, or resource ownership.
- Accept the smallest interface the consumer needs and define it near the
  consumer. Return concrete types unless an interface is part of the contract.
- Do not mirror every method of a concrete type into a speculative interface.
- Keep fields private when callers could violate invariants. Use defensive
  copies at boundaries when mutation or aliasing could corrupt state.
- Distinguish nil, empty, missing, unlimited, and zero when callers observe a
  difference.

## Errors and operational failure

- Return expected operational failures as errors; reserve panic for programmer
  invariants that cannot reasonably be handled.
- Add useful operation context with `%w` when wrapping preserves classification.
  Use `errors.Is` and `errors.As`, not message matching, for sentinel and typed
  errors.
- Do not discard errors without a documented reason. Include completion and
  cleanup errors when they affect the operation's result.
- Keep user-visible errors actionable without exposing credentials, private
  implementation details, or untrusted response bodies.
- Preserve stable sentinel and typed-error contracts when adding context.

## Context and resource ownership

- Pass `context.Context` explicitly, normally as the first parameter after the
  receiver. Do not store it in a struct merely to avoid passing it.
- Propagate cancellation and deadlines across blocking work. Every
  `context.WithCancel` or timeout creation needs its cancellation function
  called on all paths.
- Use context values sparingly for request-scoped data, not optional parameters
  or global dependencies.
- Close resources owned by the current function, placing cleanup beside
  successful acquisition. Do not close borrowed resources.
- Bound network calls, response reads, retries, and queues. Preserve rate-limit
  signals and avoid unbounded background work.

## Concurrency

- Make ownership of mutable state explicit. Prefer message passing or a single
  owner when that is simpler than shared locks.
- Protect every shared access consistently; copy neither a mutex nor a struct
  containing one after first use.
- Every goroutine needs a lifecycle: who starts it, what stops it, and how its
  termination or failure is observed.
- Avoid sending on channels or writing shared results after cancellation unless
  the protocol explicitly permits it.
- Use race-enabled tests for concurrent changes. A race-free run does not prove
  the absence of deadlocks or goroutine leaks.

## CLI and data contracts

- Keep machine-readable stdout clean; send diagnostics and progress to stderr.
  Preserve documented exit codes and non-interactive behavior.
- Treat serialized schemas as compatibility contracts. Coordinate version,
  nullability, cache invalidation, documentation, and fixtures together.
- Keep time-dependent logic deterministic with injected clocks and explicit
  zones and interval boundaries.
- Validate untrusted input at the boundary while preserving meaningful domain
  states through the rest of the program.

## Tests

- Use focused, table-driven tests when cases share behavior, but do not force
  unrelated setup into a large table.
- Mark helpers with `t.Helper()`, use `t.TempDir()` for filesystem isolation,
  and use `httptest` for HTTP behavior.
- Tests should not access real user configuration, credentials, keychains, or
  public networks. Build synthetic fixtures rather than sanitizing real data.
- Inject clocks, sleepers, transport, and other nondeterministic dependencies
  where they affect behavior.
- Use subtests for independent cases and `t.Parallel()` only when shared state
  and environment use are demonstrably safe.
- Add fuzz tests for parsers and complex boundary logic when they provide
  useful coverage; keep deterministic regression cases for discovered inputs.

## Dependencies and vulnerability review

- Keep `go.mod` and `go.sum` synchronized with `go mod tidy` and verify modules
  with `go mod verify` when dependency integrity matters.
- Prefer the standard library or an existing dependency when it already meets
  the requirement. Evaluate maintenance, API fit, transitive dependencies, and
  security before adoption.
- Use `govulncheck` for vulnerability analysis; `go vet` and tests are not
  substitutes. Review dependency updates rather than applying them blindly.

## CI and builds

- Keep local targets and CI aligned. A standard baseline is:
  `gofmt -l .` with no output, `go vet ./...`, `go test -race -shuffle=on ./...`,
  and `go build ./...`.
- Test the operating systems, architectures, and Go versions actually
  supported. Use `go-version-file: go.mod` with `actions/setup-go`.
- Add curated linters and coverage gates deliberately; do not enable every
  available rule or choose a coverage target that rewards meaningless tests.
- Apply `github-actions.md` to workflow design and hardening.

## Reproducible releases

- Use `-trimpath` and avoid wall-clock build stamps. Inject version and commit
  metadata when build information is unavailable; never move published tags.
- Use `CGO_ENABLED=0` for portable pure-Go binaries unless cgo is a documented
  requirement. Build only supported targets.
- Validate GoReleaser with `goreleaser check` and
  `goreleaser release --snapshot --clean`; ensure its hooks leave module files
  unchanged.
- Publish SHA-256 checksums. Add SBOMs, signatures, and provenance attestations
  when the release process supports them, and document verification commands.
- Follow `homebrew.md` for Homebrew distribution.

## Sources

Grounded in [Effective Go](https://go.dev/doc/effective_go),
[Go Code Review Comments](https://go.dev/wiki/CodeReviewComments), and
[`go-agent-skills`](https://github.com/eduardo-sl/go-agent-skills). Reviewed
revisions are recorded in `sources.yaml`.
