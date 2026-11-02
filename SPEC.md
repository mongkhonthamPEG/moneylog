# Moneylog — Spec

Pay-cycle budget tracker (mobile-first web app). Everything personal (accounts, salary, fixed costs, goals, commute) is user configuration, not hardcoded.

## Context
Built for people paid monthly on a varying payday, so budgeting runs on PAY CYCLES (payday → day before next payday), NOT calendar months. Default locale: Thailand (THB, Asia/Bangkok, Thai public holidays). The previous app (Moneypod) failed because it was calendar-month based and had a wallet limit.

## Tech
- Single-page PWA (Vite + TypeScript + Preact), works offline on iPhone, installable to home screen
- Data in IndexedDB; JSON export/import for backup
- No backend, no login

## Accounts (no limit; add/rename/archive)
- Fields: name, type, opening balance, archived flag
- Types: bank, savings, pot (envelope), credit_card
- Exactly one account is flagged **main spending account**. Daily-cap math, safe-to-spend and buffer use it.
- First run: setup screen where the user adds any number of accounts with opening balances and picks the main one. An account list can also be loaded from a JSON seed file via import.
- Balances are always computed from the journal (opening balance + entries), never edited directly.

## Pay cycle
- User taps "New cycle" when salary lands: enters salary amount + date
- Optional **base salary** setting: when set, any salary above it prompts "move the excess to <account>?" (e.g. overtime to a reserve account)
- Cycle setup wizard: scheduled fixed costs, pot allocations, savings transfers, daily cap
- Default daily cap: 400 (editable per cycle)
- Expected next payday defaults to the last working day of the month (public holidays excluded), editable per cycle; used for "days left"

## Fixed costs (recurring templates, editable)
- Template: name, amount, day of month, paying account, optional category
- The wizard turns templates into **scheduled items** for the cycle. Tap "paid" to post one to the journal (amount adjustable first, e.g. rent that varies).
- Subscriptions are just templates paid from whichever account the user picks (often a pot).
- **Travel top-up calculator** (optional, per user): pick commute weekdays, fare per day, and top-up unit. It counts those weekdays in the cycle, excludes public holidays and manually added days off, subtracts the current card balance (typed in, the card is not an account), and rounds UP to the top-up unit. A top-up is logged as a spending entry from a chosen account, category "transport", not counted toward the daily cap.

## Transactions
- Fields: date/time, amount, account, category, note
- Categories (editable): food, coffee, drinks, household, taxi/grab, books, medical, shopping, transport, other
- Flag: "counts toward daily cap" (category default ON for food/coffee/drinks/taxi; OFF for the rest), overridable per transaction
- Transfer type: account → account (doesn't count as spending)
- Credit card spending increases balance owed; paying the card is a transfer from a bank/pot
- Edit anytime; delete marks the entry voided (kept, hidden)
- Dates/times stored as local strings (no UTC conversion), app fixed to the configured time zone
- Amounts are integer minor units (satang), displayed with 2 decimals, e.g. 1,000.99

## Quick add (most important UX)
- Big "+" button → amount keypad → category chips → save. Under 5 seconds.
- Default account: the main spending account, default date: now
- Late-night entries (00:00–04:59) get a toggle, default OFF: "count for previous day"

## Dashboard
- Today: spent vs daily cap (green/red)
- Cycle: total daily spending vs (cap × days elapsed), running over/under
- Safe to spend per day = (main account − unpaid scheduled items paid from it this cycle) ÷ days left
- Buffer = main account − unpaid scheduled items − (cap × days left)
- All account balances; savings total; progress for each goal
- Upcoming scheduled items in the next 7 days
- Warning when the last backup is older than 7 days

## Reconciliation
- "Reconcile" button per account: enter real balance → app shows gap vs computed balance → option to log the gap as an "unlogged / other" entry so they match
- Show last reconciled date per account

## Goals
- Goal: name, target (an amount, or a number of months of expenses), linked account, progress bar
- Estimate in cycles-to-goal, from a per-cycle contribution (defaults to the average moved in over recent cycles, overridable)
- Example: emergency fund goals at 3 and 4 months of expenses, or a one-off purchase

## Reports
- Per cycle: income, fixed, daily spending, one-offs, savings moved, average daily spend vs cap
- Category breakdown per cycle
- Compare last 3 cycles

## Backup
- Versioned JSON export (`schemaVersion`) via the share sheet; prompt after each New cycle; warn after 7 days without a backup
- Import validates, then replaces all data after confirmation

## Acceptance criteria
1. Starting a new cycle on any date (e.g. 30th, 31st, 28th) works; cycles never overlap or leave gaps
2. Transfers never count as spending
3. Credit card: spending raises owed balance; paying it via transfer clears it; card spending counts toward daily cap if flagged
4. Travel calculator: given a cycle, returns the correct weekday count, excludes holidays and days off, subtracts the entered card balance, rounds up to the top-up unit
5. Reconcile: entering real balance shows exact gap to 2 decimal places
6. Late-night toggle moves the transaction to the previous day for cap purposes only
7. Export → clear data → import restores everything exactly
8. Works on iPhone Safari at 375px width, offline after first load
9. A user can create any number of accounts, archive them, and change the main spending account; dashboard math follows the flag

## Testing
Unit tests for: cycle boundaries, travel calculation (incl. holiday + card balance + rounding), daily cap math (incl. late-night toggle), reconciliation gap, credit card flow, transfer exclusion, safe-to-spend and buffer with scheduled items.

Priority: happy path → negative → edge cases (zero amounts, decimals, cycle starting on the 28th, holiday on a commute day, card balance > needed fare, changing the main account).
