# CHATGPT REPLY — H23 Preregistration Review
**Date:** 2026-10-12  
**Status:** Review only — implementation and testing not authorised yet

## Conclusion

H23 captures the intended exit-management hypothesis, but the preregistration is not yet unambiguous enough to implement without introducing assumptions. Please resolve the following items before implementation.

### 1. Partial profit at +4%
- Is the 50% partial exit executed at the target price, or at the next available price after slippage?
- How should gaps through the target be handled?

### 2. Breakeven stop
- Does breakeven refer to the theoretical entry price or the actual entry fill price after slippage?
- At exactly what point does the remaining position's stop become active?

### 3. Five-minute red-candle exit
- Are five-minute candles aligned to fixed market-session boundaries?
- Does the rule apply to the first fully completed five-minute candle after entry, or only to candles completed after a specified delay?
- How is a candle classified as red, and what execution price applies?

### 4. Simultaneous exit conditions
- If the target, breakeven stop, hard stop, and red-candle exit are triggered within the same bar, what is the exact priority?
- Please specify deterministic handling for gaps and ambiguous intrabar ordering.

### 5. R-multiple calculations
- Define initial R precisely.
- Specify how R is calculated after a 50% partial exit, including entry and exit slippage, remaining-position exits, and time exits.

### 6. P2 metric
- Confirm whether P2 means the percentage of eligible trades whose observed intrabar high reaches +4%, independent of whether the trade subsequently exits at a loss.
- Specify how incomplete or missing bars are handled.

### Data and validation concern

The currently inspected `data/retest_r2.csv` contains only eight trade rows. That file alone cannot establish entry-identical behaviour across the stated 288 symbols and 19 sessions. The full frozen dataset and a reproducible comparison against `src/retest.py` must be located and verified before claiming entry identity.

## Required next step

Please clarify the points above and publish the completed frozen specification for independent review. Until then:

- Do not implement or run H23.
- Do not modify `src/retest.py` or existing project files.
- Do not change project status or execute trades.
- Preserve the preregistration and all existing safety gates.

This is a request for specification clarification, not a rejection of H23 and not approval to proceed.
