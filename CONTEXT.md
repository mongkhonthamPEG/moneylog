# Moneylog

Personal pay-cycle budget tracker. Mobile-first offline PWA, Bangkok, THB. See `SPEC.md` for the full feature spec.

## Glossary

**Pay cycle**
Budgeting period from a payday through the day before the next payday. Not a calendar month. Cycles never overlap or leave gaps: starting a new cycle sets the previous cycle's end date to the day before the new payday. The open cycle has no end date; "days left" uses the *expected payday* (default: last working day of the month, Thai holidays excluded; editable per cycle).

**Account**
Anything that holds a balance: bank, savings, pot, credit_card. Balance is always computed from the journal, never stored or edited directly.

**Pot**
A real envelope in a banking app, separate from the Main Bank balance. Each pot is its own account. Funding a pot is a transfer from Main Bank.

**Credit card**
A liability account. Balance goes negative as you spend; paying it is a transfer from a bank or pot.

**Rabbit card**
Not an account. Money topped up cannot leave it. A top-up is a spending entry from the Pot – BTS (category `transport`, not counted toward the daily cap). Its balance is a number typed into the BTS calculator.

**Journal**
Append-only list of entries; the source of truth. Amounts are integer satang (THB × 100), displayed as `1,000.99`. A transfer is two equal and opposite entries and never counts as spending. Editing is allowed; deleting marks an entry *voided* (kept, hidden in the UI).

**Reconcile**
Enter the real bank balance, see the gap versus the computed balance, optionally log the gap as an "unlogged / other" journal entry.

**Daily cap**
Per-day spending limit (default 400 THB, editable per cycle). Only entries flagged *counts toward cap* are included. The flag defaults per category (food, coffee, drinks, taxi ON; others OFF) and can be overridden per transaction. Under/over-spend accumulates across the cycle (cap × days elapsed − spent).

**Late-night toggle**
For entries 00:00–04:59, a toggle (default OFF) moves the entry to the previous day for cap purposes only.

**Scheduled item**
A fixed cost (rent, mobile, subscriptions) created by the New-cycle wizard with an expected date and an adjustable amount. Tapping "paid" posts it to the journal. Subscriptions post from the Pot – Subscriptions. Feeds "upcoming in 7 days" and the buffer math.

**Safe to spend (per day)**
(Main Bank − unpaid Main Bank-paid scheduled items this cycle) ÷ days left.

**Buffer**
Main Bank − unpaid Main Bank-paid scheduled items − (daily cap × days left).

**Base net salary**
Editable setting, default 30,000. Salary entered above it prompts moving the OT excess to Reserve Bank.

**Goal**
Target amount linked to a pot. Contribution is per pay cycle (not month); defaults to the average moved into the pot over recent cycles; overridable. Estimate is in cycles-to-goal.

## Conventions

- Dates and times are stored as local Bangkok strings; no UTC conversion. App is fixed to Asia/Bangkok.
- English UI only; notes may contain Thai.
- Thai public holidays come from a static bundled list, updated yearly.
- Backup is a versioned JSON export (`schemaVersion`); import validates and replaces all data.
