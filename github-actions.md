# GitHub Actions conventions

Treat workflow files as privileged code. Their triggers, token scopes, action
references, and expression expansion define trust boundaries that ordinary
application linters do not inspect.

## Map trigger trust first

- Classify every trigger before authoring or reviewing a workflow. Document why
  a privileged trigger is necessary.
- Prefer `pull_request` for untrusted contributions. Fork pull requests normally
  receive a read-only token and no repository secrets; do not weaken that
  isolation.
- Avoid `pull_request_target` unless the base-repository context is essential.
  Never use a privileged trigger to check out and execute untrusted fork code.
- With `workflow_run`, treat artifacts, outputs, caches, and metadata from the
  triggering workflow as untrusted. Separate unprivileged builds from
  privileged publication.
- Do not run untrusted code on persistent self-hosted runners, especially for
  public repositories.

## Minimize token and secret authority

- Set a restrictive top-level `permissions` policy, normally `contents: read` or
  `{}`, then grant only required scopes on the individual job.
- Reserve `contents: write`, package publication, pull-request writes, and
  `id-token: write` for jobs that demonstrably need them.
- Prefer OIDC and protected environments with short-lived credentials over
  long-lived cloud keys. Require review for high-impact deployment secrets.
- Keep secrets out of workflow source and logs. Do not use structured JSON,
  YAML, or XML blobs as secrets when exact-match redaction matters.
- Do not use `set -x` or `bash -x` around secret handling. Register generated
  sensitive values for masking and rotate anything exposed.
- Set `persist-credentials: false` on checkout when later steps do not need the
  persisted token, particularly before executing untrusted code.

## Prevent expression and output injection

- Never paste attacker-controllable `${{ ... }}` expressions directly into a
  `run:` script. Pull request titles, issue bodies, comments, branch names, and
  commit messages can all be attacker-controlled.
- Pass expression values through an intermediate `env:` variable and quote the
  shell variable, or pass them as arguments to an action that handles them as
  data.
- Treat multiline values written to `$GITHUB_ENV` and `$GITHUB_OUTPUT` as
  structured output. Use safe random delimiters and do not write raw untrusted
  multiline content.
- Review `actions/github-script` and custom action inputs with the same care as
  shell blocks.
- Use explicit shell failure handling such as `set -euo pipefail` where the
  selected shell supports it.

## Lock the supply chain

- Prefer GitHub workflow dependency lockfiles where available. Otherwise pin
  actions and reusable workflows to full commit SHAs with a readable version
  comment.
- Audit an action's source at the exact revision being adopted. A marketplace
  badge or popular repository does not replace source review.
- Branch references such as `@main` are mutable and unsuitable for executable
  dependencies. Version tags are also movable unless stronger repository
  controls make them immutable.
- Keep the `github-actions` Dependabot ecosystem enabled so pinned dependencies
  receive reviewed updates. Preserve version comments for Dependabot.
- Review action updates, including transitive behavior and requested
  permissions, rather than merging them automatically.

## Protect caches, artifacts, and releases

- Scope caches and artifacts so an unprivileged workflow cannot poison inputs
  consumed by a privileged workflow.
- Verify artifact identity and contents before publication or deployment. Do
  not trust filenames or step outputs alone across trust boundaries.
- Publish releases only from protected source and reviewed automation. Never
  move a tag to repair released assets; use a corrective release.
- Use protected environments, minimal publication tokens, checksums, and
  provenance attestations where supported.

## Keep workflows maintainable

- Set reasonable `timeout-minutes` and concurrency behavior to avoid stuck or
  duplicate work.
- Keep local validation and CI commands aligned so contributors can reproduce
  failures before pushing.
- Use matrices for supported combinations, not every theoretically possible
  platform. Avoid unnecessary third-party actions for simple shell commands.
- Pin toolchains from project source files where possible. Keep dependency
  locks and generated files synchronized.

## Verify workflow changes

- Parse and validate changed workflow files. Run `actionlint` for workflow
  semantics and `zizmor` for security findings when available.
- Review every suppression with a concrete justification. Fix the underlying
  issue rather than hiding it when possible.
- Inspect effective permissions, action revisions, trigger trust, secret use,
  and shell interpolation in the final file.
- Use dry runs, snapshots, or unprivileged test environments for release
  changes. Never test publication against live credentials or production
  targets without explicit authorization.

## Sources

Based on GitHub's
[secure use reference](https://docs.github.com/en/actions/security-for-github-actions/security-guides/security-hardening-for-github-actions)
and GitHub's
[`github-actions-hardening`](https://github.com/github/awesome-copilot/tree/main/skills/github-actions-hardening)
skill. Reviewed revisions are recorded in `sources.yaml`.
