# OpenCode Rules

Reusable engineering conventions for OpenCode projects. This repository owns
common guidance; each consuming repository keeps only product-specific contracts,
commands, and skills locally.

## Source catalog

[Awesome OpenCode](https://github.com/awesome-opencode/awesome-opencode) is the
discovery source of truth for OpenCode resources. The exact catalog revision used
for this repository is recorded in `sources.yaml`; domain-specific guidance also
points to its authoritative upstream documentation there.

## Use a tagged release

Add the documents relevant to the project in `opencode.jsonc`:

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "instructions": [
    "https://raw.githubusercontent.com/acevedomiguel/opencode-rules/v1.0.2/engineering.md",
    "https://raw.githubusercontent.com/acevedomiguel/opencode-rules/v1.0.2/go-conventions.md",
    "https://raw.githubusercontent.com/acevedomiguel/opencode-rules/v1.0.2/github-actions.md",
    "https://raw.githubusercontent.com/acevedomiguel/opencode-rules/v1.0.2/homebrew.md"
  ]
}
```

Use only the documents that apply. For example, a non-Go project does not need
`go-conventions.md`; a project without Homebrew distribution does not need
`homebrew.md`.

OpenCode loads remote instructions when configuration loads. Tagged URLs make
updates explicit: consumers stay on their selected release until their config
changes.

## Documents

| Document | Scope |
| --- | --- |
| `engineering.md` | Scoped design, TDD, systematic debugging, independent review, verification, safe Git behavior, and general security |
| `go-conventions.md` | Idiomatic Go, errors, contexts, interfaces, concurrency, tests, dependencies, CI, and reproducible releases |
| `github-actions.md` | Workflow trust boundaries, token and secret scope, injection prevention, dependency locking, and validation |
| `homebrew.md` | Formula/cask decisions, package integrity, taps, GoReleaser casks, audits, signing, and notarization |
| `sources.yaml` | Reviewed upstream repositories and immutable revisions used to curate the guidance |

## Keep local overlays specific

A consuming repository may have `AGENTS.md`, additional local instruction files,
and local skills for behavior that is genuinely unique to that product. Examples
include public API contracts, domain invariants, credential classes, concrete
build commands, and supported terminal layouts.

Do not copy the common documents into those overlays. Update the central rule,
publish a new tag, and intentionally upgrade each consumer.

## Version policy

- `v1.0.0` is the initial immutable release.
- `v1.0.1` adds the explicit Awesome OpenCode source-catalog link.
- `v1.0.2` corrects the independent Go documentation source pins.
- Never move or reuse a published tag. Make follow-up changes on `main`, review
  them, and publish a new version.
- Use a patch tag for corrections that do not change expectations, a minor tag
  for additive guidance, and a major tag for materially different requirements.
- Review both the central diff and the consumer impact before changing a
  consumer's tag.

## Contributing

Keep documents standalone, concise, and free of product-specific details.
Preserve attribution and update `sources.yaml` when refreshing upstream
research. Validate Markdown links, final newlines, YAML revisions, and every raw
URL before publishing a tag.

## License

MIT. See `LICENSE`. Upstream sources remain under their respective licenses;
this repository contains curated guidance and attribution rather than vendored
skill trees.
