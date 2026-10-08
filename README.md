<p align="center">
  <img src="assets/gray-logo.svg" alt="gray" width="96">
</p>
<h1 align="center">gray-plan</h1>
<p align="center">A read-only planning mode for safer repository exploration.</p>
<p align="center">
  <a href="https://github.com/vstaln/gray-plan/blob/main/LICENSE"><img alt="MIT License" src="https://img.shields.io/badge/license-MIT-blue.svg"></a>
  <img alt="gray plugin" src="https://img.shields.io/badge/gray-plugin-7aa2f7.svg">
  <img alt="rust" src="https://img.shields.io/badge/built%20with-rust-orange.svg">
</p>

`/plan` toggles a read-only exploration mode. State persists at
`~/.gray/plan/enabled` (honoring `$GRAY_HOME`), and `/plan status` reports
the current mode.

While plan mode is on, `tool/before` denies `edit`, `write`, and every tool
outside the read-only set (`read`, `grep`, `find`, `ls`, `view`,
`web_search`, `web_fetch`, `recall`). `bash` is allowed only when every
pipe/chain segment (`|`, `||`, `&&`, `;`, newline) matches a safe pattern
and no segment matches a destructive one.

Denials return: `plan mode is on — read-only; /plan to exit`.

`agent/before_start` injects a `[PLAN MODE ACTIVE]` note so the model knows
why mutations will be denied.

## Safety rules

The safe and destructive command patterns in `src/main.rs` are evaluated per
pipeline segment. This keeps commands such as `pwd && ls` usable while still
blocking writes, deletes, privilege changes, and other mutation-shaped work.

## Scope

This plugin intentionally keeps the read-only gate, `/plan` toggle, and
plan-mode context injection. Step-tracking widgets and an execute-plan
handoff are not included because gray's sidecar wire has no widget API.

## Wire methods

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

---
Part of the [gray](https://github.com/vstaln/gray) plugin ecosystem —
the open-source AI agent harness. <https://gray.alignment.id>
