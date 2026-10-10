# Native Windows Terminal SIXEL detection — verification evidence

Sanitized evidence for a narrowly scoped DA1-based SIXEL discovery fix in `can1357/oh-my-pi`. This branch contains verification artifacts only; it is not the source branch proposed for merging. The private review ZIP, contributor review instructions, complete conversation, credentials, native binaries and personal settings are excluded.

## Environment and source identity

- Native Windows 10 x64 build 19045, PowerShell inside Windows Terminal; **not WSL**.
- Windows Terminal file version `1.24.2610.01001`.
- Installed affected release `omp 18.8.7`.
- Source baseline `db87bd637422aca14ee7744259802296fe92cd1e` on upstream `main`; manifests in that development checkout say `18.8.8`.
- Bun `1.3.14`; released Windows x64 baseline native addon `18.8.7` used for source development. No Rust/native code was changed or rebuilt.

## Screenshots

| File | Meaning |
| --- | --- |
| [Original source fallback](evidence/01-original-svg-source-fallback.png) | Reporter-supplied released-application screenshot of the original larger assistant figure appearing as code, not the minimal smoke figure. |
| [Explicit SIXEL workaround](evidence/02-forced-sixel-workaround.png) | Reporter-supplied screenshot from a new released-application session with SIXEL forced: source in the user input, actual image in the response below. |
| [Patched automatic SIXEL](evidence/03-patched-auto-sixel.png) | AI-assisted real Windows Terminal smoke of the patched ProcessTerminal, TUI and AssistantMessageComponent, with no protocol override. The box and SVG OK are visible as image pixels. |

Images were cropped where needed to remove hostname, working-directory/status labels and unrelated window chrome, then re-encoded as metadata-free RGB PNG. No screenshot pixels were synthesized.

## Protocol evidence

- [terminal-probe.json](evidence/terminal-probe.json): recorded raw query/reply bytes and timings from a separate real native Windows Terminal process. Real TTY stdin/stdout; normal DSR before/after; no XTSMGRAPHICS geometry or palette reply during each two-second window; DA1 includes SIXEL attribute 4 after approximately one millisecond. The session GUID is removed; only its presence is retained.
- [patched-smoke.json](evidence/patched-smoke.json): recorded patched real-terminal smoke. No inherited/active override, protocol transitions from null to SIXEL, and a SIXEL image row is emitted. The screenshot independently confirms visible pixels.

## Original command outputs

These are exports of recorded verification outputs, not newly rerun or reconstructed results. Local account and checkout paths were removed. Synthetic test fixture paths such as `/Users/me/...` remain because they are necessary to explain the existing failures.

- [Regression before the fix](evidence/regression-before.txt): expected SIXEL, received null. The first regression used the existing feed helper; the submitted tests use deterministic fake timers.
- [Related tests after the fix](evidence/related-tests-after.txt): 106 pass, zero fail, 1,135 assertions.
- [Package check](evidence/tui-check.txt): lint, formatting and TypeScript pass; not a root `bun check` claim.
- [Complete full-TUI captured output](evidence/full-tui-suite.txt): 3,553 pass, four skip, five fail, 56,739 assertions. Failures are included, not suppressed.
- [Original-renderer comparison](evidence/baseline-existing-failures.txt): all five failures reproduced using original `terminal.ts` and `tui.ts` loaded from the baseline through a temporary Bun preload. This was a targeted baseline run, not another complete full-suite run.
- [Initial run inheriting WT_SESSION](evidence/initial-ambient-wt-failures.txt): 100 pass, six fail.
- [Baseline ambient timer failure](evidence/baseline-ambient-wt-timer.txt): the first ambient-run timer-count failure reproduced with the original ProcessTerminal source. No claim that all six ambient failures were independently baseline-confirmed.

Automated tests subsequently removed inherited `WT_SESSION` from the test subprocess only. Real-terminal diagnostics and the patched visual smoke retained the Windows Terminal environment. No persistent environment setting or installed executable was changed.

## Scope and disclosure

Full-suite failures comprise three POSIX file-URL expectations on Windows and two locale-dependent timestamp expectations. They reproduced on the baseline renderer and remain out of scope.

The actual visual smoke exercised the real terminal and assistant rendering path without a model request. It is not proof of a complete patched CLI conversation, every resize/history transition, other host terminals, or cross-platform behavior. Root `bun check`, Rust checks/rebuild, Linux/macOS runs and remote CI were not part of these local observations.

Investigation, implementation, automated verification and the patched visual smoke were AI-assisted. The reporter observed the original released-application failure and explicit-SIXEL workaround. The evidence does not manufacture personal verification claims.

[evidence/changes.patch](evidence/changes.patch) is the original reviewed local patch before adding the eventual PR link and contributor attribution to CHANGELOG. The live PR diff is authoritative for final source changes.

[MANIFEST.json](MANIFEST.json) contains SHA-256 digests and byte sizes of the initial and follow-up evidence artifacts, allowing comparison with the reviewed local files. It does not contain the private ZIP.

## Review follow-up

[Follow-up report](evidence/review-followup.md) documents source commit `3917b2518db9adf135117a76c0024083bfb48cc3`, addressing both reviewers' pending-XTSMGRAPHICS lifetime finding and the regex-hoisting nit. The recorded five-file related suite passed 109 tests / 1,144 assertions, the package check passed, and a new real native Windows Terminal smoke displayed SVG OK with the protocol override unset and SIXEL retained beyond the graphics timeout.

The additional [screenshot](evidence/04-review-followup-auto-sixel.png), [smoke JSON](evidence/review-followup-smoke.json), [test output](evidence/review-followup-tests.txt), [package check output](evidence/review-followup-check.txt), and [incremental patch](evidence/review-followup.patch) are separate from the preserved initial observations. The complete suite was not rerun after this follow-up.

