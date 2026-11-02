# 0001. Store money as integer minor units

Status: accepted

All amounts are stored and computed as integers in the currency's minor unit (satang for THB, i.e. THB × 100). Decimals appear only at input and display (`1,000.99`). Floats are never used for money.

Why: reconciliation must be exact to 2 decimals, and float sums drift.
