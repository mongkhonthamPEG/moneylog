# 0001. Store money as integer satang

Status: accepted

All amounts are stored and computed as integers in satang (THB × 100). Decimals appear only at input and display (`1,000.99`). Floats are never used for money.

Why: reconciliation must be exact to 2 decimals, and float sums drift.
