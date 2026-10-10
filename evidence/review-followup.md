# PR #15226 — review follow-up

## Findings addressed

- [roboomp should-fix](https://github.com/can1357/oh-my-pi/pull/15226#discussion_r4238142495): selecting SIXEL from DA1 prematurely removed the consumer for an already-issued XTSMGRAPHICS reply.
- [Codex P2](https://github.com/can1357/oh-my-pi/pull/15226#discussion_r4238150812): the same reply-lifetime issue, not a separate defect.
- [roboomp nit](https://github.com/can1357/oh-my-pi/pull/15226#discussion_r4238142497): hoist the SIXEL attribute regex beside the other response patterns.

## Implementation

The DA1 callback now calls `#enableSixelProtocol()` instead of completing the graphics probe. Protocol activation and probe cleanup are separate. The existing XTSMGRAPHICS reply or 250 ms timeout still cleans up the pending listener; an already-selected protocol avoids redundant setup. Native Windows Terminal therefore enables graphics promptly through DA1 while terminals that answer both queries continue to consume the outstanding reply.

`da1SixelAttributePattern` is declared beside `da1ResponsePattern` in the existing stdin-parser setup rather than creating its regex literal in each reply callback.

## New regression coverage

Three cases were added to the existing ProcessTerminal DA1 suite:

1. DA1 advertises attribute 4, followed by a positive XTSMGRAPHICS response. Only ordinary input `x` reaches the application listener.
2. The graphics response following DA1 is split across the stdin flush deadline. No prefix or completed report leaks; ordinary input is preserved.
3. A negative graphics reply after a positive DA1 advertisement is consumed without revoking established SIXEL support.

The existing native-Windows/no-graphics-reply case also advances the fake clock beyond the graphics timeout and confirms the selected protocol is retained. No wall-clock sleeps were added to the regression tests.

## Recorded checks

- [review-followup-tests.txt](review-followup-tests.txt): five-file related suite, 109 passed, zero failed, 1,144 assertions.
- [review-followup-check.txt](review-followup-check.txt): package oxlint, formatting and TypeScript checks passed.
- [review-followup-smoke.json](review-followup-smoke.json): real native Windows Terminal, real TTY input/output, override unset, protocol changes from null to SIXEL and remains selected after the graphics timeout.
- [04-review-followup-auto-sixel.png](04-review-followup-auto-sixel.png): captured image pixels show the outlined SVG OK figure after the lifetime fix. Cropped terminal content only; PNG metadata removed.
- [review-followup.patch](review-followup.patch): exact incremental local patch on top of the originally submitted head `9fea982828efaabc9804fda603b8b2d9836d5b7a`.

The visual smoke painted the image at 161 ms and recorded success at 464 ms. It used the actual ProcessTerminal, TUI and AssistantMessageComponent without a model call. It is not a claim of a complete patched CLI conversation or a visual run on foot/xterm/WezTerm; those reply-order conditions are exercised through the real input pipeline in the regression harness.

The complete TUI suite was not rerun for this follow-up. Previously reported full-suite failures and their original-source comparison remain initial evidence, not newly passing results. No root `bun check`, Rust rebuild or cross-platform verification is claimed. Investigation, implementation, checks and smoke were AI-assisted.
