# ChatGPT → Muse — H23 data-source decision request
Date: 2026-10-12

## Verified blocker

I checked the live `/mous-meta` workspace and queried the HF Data Library symbols endpoint.

- The frozen H23 dataset `data/yahoo_1m/*.json` is absent from the workspace.
- `data/retest_r2.csv` contains 8 prior trade rows, not the 288-symbol, 19-session minute-bar dataset.
- The available local `data/universe.txt` contains 314 symbols; it is not evidence of the exact frozen H23 universe.
- HF Data Library advertises 1,253 symbols, but only 59 of the current 314-symbol list matched its returned ticker list. Only 1 of the 8 symbols in `retest_r2.csv` matched. Therefore it cannot presently be treated as a replacement for the frozen Yahoo dataset.
- No H23 script has been created, no test has been run, and no project files have been changed.

## Decision requested from Muse / Anas

Please answer explicitly:

1. **Preferred path:** Should we continue searching for the exact frozen Yahoo 1-minute archive and keep H23 BLOCKED until recovered?
2. **Alternative source:** If the archive cannot be recovered, does Anas authorize a formal pre-registration amendment to use another source (for example HF Data Library), with the changed symbol coverage, IEX-only provenance, missing-symbol policy, session coverage, and entry-identity baseline specified BEFORE any run?
3. **Baseline problem:** Since the existing `retest_r2.csv` has only 8 trades and the original 288-symbol source data are missing, what exact artifact should define the frozen expected entry set if the original dataset is unrecoverable? I will not infer or reconstruct it from the 8 trade rows.

Until a clear decision and an adequate dataset/baseline are available, I will not substitute data, run H23, or report a pass/fail result. This request itself makes no changes to the pre-registration or project state.
