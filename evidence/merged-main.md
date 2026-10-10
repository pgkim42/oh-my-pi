# PR #15226 — upstream main synchronization

## Source revision

Merge commit `7c7c10f77c8555e227071a6e9f921d8d678b7760` joins the reviewed SIXEL fix at `3917b2518db9adf135117a76c0024083bfb48cc3` with upstream main at `1534f1a33954da73fc3c234cb0d146e6d6942f89`.

The actual local merge succeeded automatically. No renderer code conflict needed manual selection. Both the upstream composer/status-line git-fact change and the SIXEL changelog entry were preserved, with a missing separator blank line restored between the merged changelog sections. No change to SIXEL discovery or draining logic was made during this synchronization.

A scope check against the merged main tree listed only the existing five PR files: the renderer contract documentation, TUI changelog, terminal.ts, tui.ts and sixel-probe.test.ts. Upstream's eight changed files were incorporated through the merge, not proposed as unrelated new PR work.

## Recorded verification

| Check | Result |
| --- | --- |
| Seven-file related run, including upstream composer/status-line coverage | 137 passed, zero failed, 1,255 assertions |
| TUI package lint, formatting and TypeScript check | Passed |
| Real native Windows Terminal SVG smoke, protocol override unset | Image visibly rendered |
| SIXEL after graphics probe timeout | Remained selected |

- [merged-main-tests.txt](merged-main-tests.txt): original captured output and the exact seven-file command.
- [merged-main-check.txt](merged-main-check.txt): original captured package check.
- [merged-main-smoke.json](merged-main-smoke.json): real TTY process, win32, no override, null-to-SIXEL discovery, image painted at 159 ms and success observed at 463 ms.
- [05-merged-main-auto-sixel.png](05-merged-main-auto-sixel.png): actual captured SVG OK pixels, cropped to terminal content and re-encoded without PNG metadata.

The automated tests remove inherited WT_SESSION from their subprocess only, following the previously disclosed isolation. The visual smoke retains the actual Windows Terminal environment. No permanent user setting or installed executable was changed.

## Boundaries

The screenshot and smoke use real ProcessTerminal, TUI and AssistantMessageComponent without a model request. They are not evidence of a full model-backed patched CLI conversation. Root bun check, Rust checks/rebuild and the entire TUI suite were not rerun for this merge. Earlier full-suite failures remain historical results, not a claim of new passes.

Verification was AI-assisted. Commit metadata uses the contributor's GitHub noreply email. The private review ZIP is not part of this evidence branch.
