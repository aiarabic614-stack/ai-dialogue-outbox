# Entry 0037 — 2026-10-11 — User decision and Alpaca Paper preparation

## User's explicit strategy-testing policy
- Anas clarified that previously explored strategies must **not be finally classified as failed solely from historical/backtest results**. Each strategy should first be tested practically on the Alpaca Paper account, then judged from the paper-execution evidence. Existing negative backtest results must remain documented as warnings/evidence; they are not to be erased or misrepresented.
- Current priority is **intraday/day trading only**. Defer long-horizon strategies whose success cannot be evaluated without waiting months or years.
- Virtual paper-account losses are acceptable as part of learning. The objective is to discover, test, and ultimately validate a genuinely effective strategy, not to protect virtual capital at all costs.
- Live-money trading remains disabled. All broker execution must remain strictly on Alpaca Paper. No real-money order authorization was given.

## Environment and broker checks completed on 2026-10-11
- Added/verified PyYAML in the Python environment (PyYAML 6.0.3). The first `--check` attempt encountered a Windows console encoding error while printing a checkmark; rerunning with `PYTHONUTF8=1` fixed the display issue.
- `python src/main.py --check` succeeded with UTF-8 mode: Alpaca Paper API connection succeeded; account status had previously been verified as ACTIVE, USD, and not trading-blocked. The API returned equity of **$99,995.96** on this check.
- Market clock reported **closed** at the time of the check. No orders were submitted.
- `python src/main.py --build-universe` completed using Alpaca/IEX data: 8,271 initially eligible symbols were screened and a universe of **314 stocks** was saved to `data/universe.txt`.
- `python src/test_engine.py` passed all **13** built-in engine tests using a fake broker (entry, exits, stop loss, time exit, max positions, kill switch, daily loss halt, no same-day re-entry, fill-price logging, missing VWAP, and no entry before open). These are code-behavior tests, not evidence of profitability.
- `python src/main.py --report` showed **0 recorded trades** at this point. No paper trades have yet been executed.
- Running `python -m unittest discover -s src -p 'test*.py'` found zero tests because the current test file is run directly as `python src/test_engine.py`; do not report discovery as a passing suite.

## Strategy status and immediate guardrails
- The existing small-cap opening-range-breakout/VWAP implementation is still the code currently in `src/strategy.py` and `src/engine.py`. Prior research on this family had negative results, so do not treat it as validated or launch it unchanged as the chosen winning strategy. Under Anas's updated policy, those negative research results are warnings and a reason for careful paper evaluation, not an automatic final rejection.
- Before any automated paper run, review and fix execution risks in the current engine and define the exact intraday strategy/test protocol. Keep every test auditable: rules frozen before the run, record candidate/signal, order and fill, spread/slippage where measurable, exit reason, realized P/L, and a per-session report. Do not tune rules after seeing outcomes without documenting a new test.
- The 314-stock universe is prepared, but the opening scanner and automated trading loop have **not** been run for paper execution. The current market was closed during preparation.
- The project status file `STRATEGY_STATUS.md` was updated locally to reflect this decision: `INTRADAY_PAPER_VALIDATION_PRIORITY`; Opening Momentum, Opening Fade, and Catalyst are marked `NOT_DEMO_VALIDATED` rather than finally rejected; long-horizon strategies are deferred; live trading is disabled; Alpaca Paper validation is authorized but no orders have been sent.

## Next action
Prepare an auditable, short-horizon paper-trading test, validate its operational safeguards, then test during a regular US market session. Assess results from the actual simulated fills and a sufficient sample; do not promise profitability or declare success from a few winning trades. Preserve H8's blocked status and keep its historical-price audit separate from this intraday paper-testing work.

## Security
API credentials are stored in the local `.env` file and excluded by `.gitignore`. **Never publish, quote, or commit API keys or secret values** to this outbox.
