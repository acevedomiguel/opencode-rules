# Homebrew packaging conventions

Use the current [Formula Cookbook](https://docs.brew.sh/Formula-Cookbook),
[Cask Cookbook](https://docs.brew.sh/Cask-Cookbook), and
[GoReleaser documentation](https://goreleaser.com/customization/publish/homebrew_casks/)
as authoritative references when syntax or policy differs from an example.

## Choose formula or cask deliberately

- Use a formula for supported source-built packages and other package types the
  current Formula Cookbook describes. Include build dependencies, a real test
  block, and an SPDX license identifier.
- Use a cask for supported binary/application distributions, macOS apps, fonts,
  and artifacts described by the Cask Cookbook.
- When GoReleaser generates a prebuilt Go binary package, use its current
  `homebrew_casks` support. Its Homebrew formula integration is deprecated;
  do not start new work with deprecated formula fields.
- Preserve an existing distribution choice unless a migration is explicitly
  requested. Do not silently change user installation commands.

## Make identity and downloads verifiable

- Use stable, versioned HTTPS URLs tied to immutable release artifacts. Avoid
  unversioned `latest` downloads for versioned packages.
- Compute SHA-256 from the exact downloaded artifact and verify it against the
  publisher's checksums when available. Never carry a checksum across versions
  without recomputing it.
- Keep package token, filename, version, URL, homepage, description, and
  architecture conditions consistent.
- Use concise descriptions that follow Homebrew's guidelines. Formulas require
  an appropriate license; casks require the documented identity and artifact
  stanzas.
- Preserve architecture-specific URLs and checksums independently for ARM and
  Intel builds. Declare operating-system or architecture requirements only
  when the artifacts require them.

## Formula requirements

- Use Homebrew's build helpers and standard install paths. For Go source builds,
  use the supported Go dependency and `std_go_args` where appropriate.
- Keep build-time and runtime dependencies distinct and minimal.
- Add a `test do` block that exercises installed behavior, such as version or a
  meaningful command, rather than merely checking that a file exists.
- Ensure the test verifies the artifact described by the formula so a URL or
  checksum mismatch cannot silently install unrelated content.

## Cask requirements

- Include the required version, SHA-256, URL, name, description, homepage, and
  at least one appropriate artifact stanza.
- Use `binary` for an executable contained in an archive, `app` for an app
  bundle, and the documented artifact types for other content.
- Follow current stanza ordering and declarative install-step syntax. Prefer
  structured `*_steps` operations over legacy arbitrary Ruby flight blocks.
- Provide appropriate `uninstall` and `zap` behavior when the installed
  artifacts or user data require them. Do not delete broad user-data paths.
- Avoid arbitrary Ruby and unnecessary install scripts when a specific
  declarative artifact or step expresses the operation.

## Manage taps and updates safely

- Keep tap structure conventional: `Formula/` for formulas and `Casks/` for
  casks in a repository named `homebrew-<tap>`.
- Update package definitions through reviewed automation or pull requests.
  Verify the release exists before changing the package definition.
- Never move a published release tag to repair a package. Publish a corrective
  release and update URL/checksum metadata together.
- Keep tap publication credentials limited to the target repository. A source
  repository's default workflow token normally cannot write a separate tap.
- When GoReleaser publishes cross-repository, use a dedicated minimal-scope
  token and suppress uploads during snapshot builds. Prefer pull-request
  publication when direct tap updates are not required.

## Validate before publication

For a formula, use the applicable commands in an approved environment:

```sh
brew style ./Formula/example.rb
brew audit --strict --online ./Formula/example.rb
brew install --build-from-source ./Formula/example.rb
brew test example
```

For a cask, use:

```sh
brew style --cask ./Casks/example.rb
brew audit --strict --online --cask ./Casks/example.rb
brew install --cask ./Casks/example.rb
example --version
brew uninstall --cask example
```

Use `--new` for new packages when required by the target tap. Validate generated
files as well as source configuration:

```sh
goreleaser check
goreleaser release --snapshot --clean
```

Do not alter a developer's installed packages or publish a tap update merely to
validate a documentation change. Use isolated or CI environments for install
and uninstall checks.

## Treat signing and quarantine as security decisions

- Prefer signed and notarized Apple distributions. Record checksums and signing
  identities as part of the release supply chain.
- Removing quarantine with `xattr` bypasses Gatekeeper verification and weakens
  platform security. Do not introduce it as a routine packaging shortcut.
- If an unsigned package intentionally uses a quarantine bypass, make the
  trade-off explicit to reviewers and users, scope it to the exact staged
  artifact, and treat signing/notarization as the preferred replacement.
- Never disable unrelated security controls or broaden an install script's
  authority to hide a signing problem.

## Sources

Curated from the Homebrew Formula and Cask Cookbooks, GoReleaser's current
Homebrew cask documentation, and the
[`homebrew-dev`](https://github.com/trtmn/agent-skills/tree/main/homebrew-dev)
skill. Reviewed revisions are recorded in `sources.yaml`.
