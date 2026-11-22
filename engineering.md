# Common engineering conventions

Use these rules for any repository. A local product overlay may add stricter
requirements or override examples that conflict with documented product
contracts.

## Understand before changing

- Establish the requested outcome and read the relevant implementation, tests,
  and project documentation before editing.
- Keep the change within the requested scope. Do not mix in unrelated cleanup,
  formatting, dependency updates, or speculative abstractions.
- Preserve public contracts and existing special cases unless the task
  explicitly changes them. Coordinate intentional contract changes with docs,
  tests, fixtures, and consumers.
- For non-trivial work, write a short plan with verification steps. For
  ambiguous product behavior, resolve the ambiguity before implementation.

## Design for the responsible boundary

- Prefer the simplest correct design using existing project patterns.
- Put invariants, validation, and cleanup in the component that owns the state
  or resource rather than repeating them across callers.
- Keep distinct outcomes distinguishable. Do not collapse missing, unlimited,
  unavailable, empty, and zero into one value.
- Add an abstraction only when it removes real duplication or establishes a
  needed boundary. Avoid layers that merely forward calls.
- Keep data validation, trust boundaries, lifecycle transitions, and failure
  behavior explicit.

## Test first

- For behavior changes, write a focused test that fails for the intended reason
  before implementing the change. Then make the smallest change that passes it.
- Cover boundaries and failure paths as well as the happy path. Use synthetic
  fixtures and deterministic dependencies such as injected clocks.
- Do not weaken assertions, delete tests, or broaden snapshots merely to make a
  change pass.
- Configuration and documentation changes need executable validation too:
  validate schemas, links, generated files, or documented commands as relevant.

## Debug systematically

- Reproduce the failure reliably and capture the exact error before proposing a
  fix.
- Isolate the failing boundary, trace values back to their source, and test one
  evidence-based hypothesis at a time.
- Fix the root cause rather than suppressing its symptom. Add a regression test
  that demonstrates the original failure and the corrected behavior.
- Treat environment, timing, and concurrency failures as real behaviors to
  explain, not as reasons to retry until green.

## Review independently

- Review the final code region and its callers, not only the diff or the
  author's explanation.
- Check correctness, security, compatibility, resource ownership, concurrency,
  error handling, and test coverage in proportion to the change.
- Report actionable findings in severity order with file and line references.
  Distinguish proven defects from questions and optional improvements.
- Do not automatically apply broad refactorings discovered during a focused
  review; present them separately.

## Verify before completion

- Run the narrowest meaningful check after each change, then the relevant full
  project checks before handing work back.
- Inspect actual exit codes and output. A formatter or test command that did
  not run is not a passing check.
- Report checks that were unavailable or skipped. Never claim success based on
  intentions, partial output, or a previous run.
- Leave generated files, dependency locks, documentation, and source changes
  synchronized.

## Work safely with Git

- Use focused branches and concise Conventional Commit subjects. Integrate
  through reviewed pull requests rather than direct pushes to protected
  branches.
- Do not run destructive Git or filesystem operations without explicit
  approval. Do not overwrite or delete unfamiliar work.
- Do not commit, push, publish, or create releases unless requested. Never move
  a published tag to substitute for a corrective release.
- Review the final working-tree diff and keep build output, credentials, local
  configuration, and test artifacts out of version control.

## Apply security everywhere

- Keep secrets out of source, logs, prompts, fixtures, tickets, and generated
  artifacts. Use synthetic examples and redact errors at trust boundaries.
- Validate untrusted input before interpolation into shells, queries, paths,
  URLs, markup, or serialized formats.
- Give tools, automation, and credentials the least authority needed. Pin and
  review executable dependencies; a version tag alone is not immutable.
- Do not bypass security controls or confirmation prompts to obtain a green
  result. Document unavoidable trade-offs plainly.

## Sources

This document is a curated synthesis informed by
[Superpowers](https://github.com/obra/superpowers) and
[Boffin](https://github.com/MicSm/boffin). Exact reviewed revisions are recorded
in `sources.yaml`.
