# Moneylog — Spec

Personal pay-cycle budget tracker (mobile-first web app).

## Context
Bangkok, paid monthly in THB. Payday varies (usually the last working day of the month), so budgeting runs on PAY CYCLES (payday → day before next payday), NOT calendar months. The previous app (Moneypod) failed because it was calendar-month based and had a wallet limit.

## Tech
- Single-page PWA (HTML + vanilla JS or lightweight framework), works offline on iPhone, installable to home screen
- Data stored in localStorage/IndexedDB
- JSON export/import for backup
- No backend, no login

## Accounts (no limit; add/rename/archive)
Types: bank, savings, pot (envelope), credit_card

Initial state (cycle starting 2026-10-31):
- Main Bank (bank, main spending): 15,000.00
- Reserve Bank (bank, reserve): 2,000.00
- Emergency Fund A (savings, emergency fund): 10,000.00
- Emergency Fund B (savings, emergency fund): 10,000.00
- Bank 2 (bank): 0
- Pot – Subscriptions (pot): 1,000
- Pot – BTS (pot): 1,000
- Pot – Book fund (pot): 1,000
- Pot – Credit card prep (pot): 7,000.00
- Credit card (credit_card): 0 owed

## Pay cycle
- User taps "New cycle" when salary lands: enters salary amount + date
- Base net salary ≈ 30,000; anything above is OT → prompt to move OT to Reserve Bank
- Cycle setup wizard: fixed costs (rent, mobile), pot allocations, savings transfers, daily cap
- Default daily cap: 400 THB (editable per cycle)

## Fixed costs (recurring templates, editable)
- Rent + utilities (varies monthly, ~9,000–10,000)
- Mobile (~200–220)
- Subscriptions (paid from Pot – Subscriptions):
  YouTube Premium 199 (14th), YouTube Member เล็ก 20 (23rd), Claude Pro 700 (24th), YouTube Member มดงาน 20 (26th), Spotify 149 (~29th)
- BTS: office days Mon + Thu, 130 THB per day round trip. Count office days in the cycle, exclude Thai public holidays + manually added days off, subtract current Rabbit card balance, round UP to nearest 200 (top-up unit)

## Transactions
- Fields: date/time, amount, account, category, note
- Categories (editable): food, coffee, drinks, household, taxi/grab, books, medical, shopping, other
- Flag: "counts toward daily cap" (default ON for food/coffee/drinks/taxi; OFF for one-offs like household, medical, books)
- Transfer type: account → account (doesn't count as spending)
- Credit card spending increases card balance owed; paying the card is a transfer from a bank/pot

## Quick add (most important UX)
- Big "+" button → amount keypad → category chips → save. Under 5 seconds.
- Default account: Main Bank, default date: now
- Late-night entries (00:00–04:59) get a toggle: "count for previous day"

## Dashboard
- Today: spent vs daily cap (green/red)
- Cycle: total daily spending vs (cap × days elapsed), running over/under
- Remaining in Main Bank ÷ days left in cycle = "safe to spend per day"
- Buffer = Main Bank − (cap × days left)
- All account balances; savings total; emergency fund progress toward 90,000 (3-month) and 120,000 (4-month)
- Upcoming subscription charges in the next 7 days

## Reconciliation
- "Reconcile" button per account: enter real bank balance → app shows gap vs computed balance → option to log the gap as "unlogged / other" so they match
- Show last reconciled date per account

## Goals
- Goal list (e.g. IKEA bookshelf): target amount, linked pot, progress bar, months-to-goal estimate from monthly contribution

## Reports
- Per cycle: income, fixed, daily spending, one-offs, savings moved, average daily spend vs cap
- Category breakdown per cycle
- Compare last 3 cycles

## Acceptance criteria
1. Starting a new cycle on any date (e.g. 30th, 31st, 28th) works; cycles never overlap or leave gaps
2. Transfers never count as spending
3. Credit card: spending raises owed balance; paying it via transfer clears it; card spending counts toward daily cap if category is flagged
4. BTS calculator: given a cycle, returns correct Mon/Thu count, excludes holidays, subtracts card balance, rounds up to 200
5. Reconcile: entering real balance shows exact gap to 2 decimal places
6. Late-night toggle moves the transaction to the previous day for cap purposes only
7. Export → clear data → import restores everything exactly
8. Works on iPhone Safari at 375px width, offline after first load

## Testing
Unit tests for: cycle boundaries, BTS calculation (incl. holiday + card balance + rounding), daily cap math (incl. late-night toggle), reconciliation gap, credit card flow, transfer exclusion.

Priority: happy path → negative → edge cases (zero amounts, decimals, cycle starting on the 28th, holiday on an office day, card balance > needed fare).
