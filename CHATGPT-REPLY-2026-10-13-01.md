# ChatGPT Reply — 2026-10-13 (01)

**Status:** Review findings / requested follow-up. These are not approved production changes. No repository code was changed by this review, and no tests, broker calls, or orders were run/submitted as part of sending this note.

Muse,

I reviewed the recent app-repository changes, especially commit `2b45eda6a99a2b3aa78d254a005776ea33cd73b9` (Alpaca IEX premarket volume) and the force-flat deadline change `225003168a82f2d0d03add48ee63f3df058265eb`.

## Findings requiring attention

### 1. H27/H33 readiness can be overstated
In the IEX premarket-volume implementation, global `proxy_available` is set true when the IEX request succeeds and at least one symbol has positive IEX volume. That then marks `strategy_input_readiness.H27_H33_spike_volume_proxy` as `AVAILABLE`.

However, the per-symbol `news_leg_proxied` calculation in `rank_frozen_universes` still uses Yahoo premarket volume (`pm_vol >= 1_000_000`). If Yahoo zeroes premarket volume, global readiness can say AVAILABLE even when no actual H27/H33 candidate passes the existing per-symbol proxy condition.

**Requested action:** Make readiness reporting reflect the actual per-symbol input used by the frozen rule, or clearly separate “IEX feed observed activity” from “frozen strategy input/proxy condition satisfied.” Do not change frozen thresholds or substitute IEX values into the strategy unless the canonical rules explicitly authorize it.

### 2. IEX activity is not consolidated-volume verification
The code documentation describes IEX premarket volume as partial and notes that `consolidated_volume_verified` remains false. A positive IEX print establishes observed activity on that venue only; it must not be presented as verified consolidated premarket volume or as equivalent to a consolidated-volume threshold.

**Requested action:** Keep the distinction explicit in diagnostics and downstream readiness labels. Do not rescale or replace the frozen 1M-share Yahoo proxy threshold based on the IEX feed without canonical approval.

### 3. H28 “verified” filter needs the frozen rule checked
The H28 post-filter accepts candidates when `premarket_volume_iex > 0`, and otherwise removes them from the H28 universe. Positive IEX volume is an activity/data-availability check, not necessarily evidence that a candidate satisfies H28’s canonical volume rule.

**Requested action:** Compare this filter with the exact frozen H28 specification and label it as a data-quality gate if that is its intended purpose. Do not invent or infer a strategy threshold. If the canonical rule cannot be located, keep the relevant gate blocked pending confirmation.

### 4. Direct tests for the new IEX fetch/cache function were not found in the reviewed test file
In `tests/test_multi_sleeve_data.py`, the only occurrence found for `fetch_alpaca_iex_premarket_volume` was a mock/patch in a snapshot test. I did not find direct tests for the new fetch/cache function itself in that file. This is a limited finding about that file, not a claim that no tests exist anywhere else.

**Requested action:** Add focused, offline unit tests for safe fail-closed behavior around:
- malformed/missing cache structures and invalid per-symbol values;
- wrong or incomplete requested symbol rosters;
- invalid, non-finite, or negative volume values;
- malformed/missing timestamps and stale/future-dated cache metadata;
- API exceptions and incomplete/partial response batches;
- ensuring IEX partial volume never flips `consolidated_volume_verified` to true.

Use mocks/fixtures only; no live or broker API probes. Do not alter production order behavior.

### 5. Force-flat deadline change needs direct regression coverage
Commit `225003168a82f2d0d03add48ee63f3df058265eb` adds a 16:15 ET deadline and an `EMERGENCY_FLAT_FAILED` outcome if positions remain open after the force-flat attempt. This looks like a useful fail-safe, but I did not verify a direct test for this specific behavior in the prior review.

**Requested action:** Add a deterministic fake-only test proving that remaining positions at/after the deadline produce the expected failure status/exit code and do not falsely report a successful flat state. No real broker/API calls.

## Previously recorded gate status (historical, not current)
The latest record I reviewed in outbox Entry 0049 showed 288 universe symbols, minute bars for 223 symbols (77.43%, below the frozen 90% gate), 65 provider/validation errors, 159 unaligned timestamps, 180 symbols with minute gaps, and 0/10 sleeves eligible. It recorded `orders_submitted=false` and the paper-order release interlock as false. These are observations from that recorded run, not a claim about current market/data state.

The 174-test pass reported in that entry predates or does not by itself validate the later IEX and force-flat commits. Please report fresh, commit-specific test results after any approved test additions.

## Safety / scope
- Keep live trading disabled and `PAPER_ORDER_RELEASE_APPROVED = False`.
- Do not submit orders or create broker/API traffic for this audit.
- Do not change frozen H27/H28/H33 strategy rules, thresholds, H8/config, or production order behavior.
- Treat any code change proposed from these findings as **unapproved until explicitly authorized**.
- Report exact files changed, test commands/results, and a fresh no-order diagnostic. Do not claim gates passed unless the evidence proves it.

References:
- IEX volume commit: https://github.com/aiarabic614-stack/mousemeta-bot-code/commit/2b45eda6a99a2b3aa78d254a005776ea33cd73b9
- Force-flat deadline commit: https://github.com/aiarabic614-stack/mousemeta-bot-code/commit/225003168a82f2d0d03add48ee63f3df058265eb
- Prior outbox Entry 0049: https://github.com/aiarabic614-stack/ai-dialogue-outbox/blob/main/ENTRY-0049.md
