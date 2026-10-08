# gray-plan

Read-only exploration mode for gray — a sidecar plugin port of pi's
`plan-mode` extension.

`/plan` toggles plan mode (state persists at `~/.gray/plan/enabled`,
honoring `$GRAY_HOME`); `/plan status` reports it. While on, `tool/before`
denies `edit`, `write`, and every tool outside the read-only set
(`read`, `grep`, `find`, `ls`, `view`, `web_search`, `web_fetch`, `recall`).
`bash` is allowed only when **every** pipe/chain segment (`|`, `||`, `&&`,
`;`, newline) matches a SAFE pattern and no segment matches a DESTRUCTIVE
one. Deny reason: `plan mode is on — read-only; /plan to exit`.

`agent/before_start` injects a `[PLAN MODE ACTIVE]` note so the model
knows writes will deny.

## Patterns

`SAFE_PATTERNS` and `DESTRUCTIVE_PATTERNS` in `src/main.rs` are a faithful
port of pi `plan-mode/utils.ts` (the `(?!>)` lookahead is rewritten as
`($|[^>])` for the `regex` crate, and commands are additionally segmented
on pipes/chains before judging).

## Partial port

pi's `[DONE:n]` step tracking, plan-step widgets, and the execute-plan
handoff are dropped: the gray wire has no widget/`sendMessage` API, so
there is nowhere to render them. This port keeps the read-only gate, the
`/plan` toggle, and the plan-mode context injection.

## Wire methods used

- `plugin/manifest`, `plugin/shutdown`
- `tool/before` (hook) — `allow`/`deny` verdicts
- `agent/before_start` (hook) — injects the plan-mode notice
- `command/run` — `/plan`

## Install

```sh
gray plugin install plan
```

## Develop

```sh
cargo test
cargo build --release
gray account check
```
