# Moneylog

Pay-cycle budget tracker. Mobile-first offline PWA, default locale Thailand (THB, Asia/Bangkok). See `SPEC.md` for the full feature spec.

## Glossary

**Pay cycle**
Budgeting period from a payday through the day before the next payday. Not a calendar month. Cycles never overlap or leave gaps: starting a new cycle sets the previous cycle's end date to the day before the new payday. The open cycle has no end date; "days left" uses the *expected payday* (default: last working day of the month, weekends and the selected holiday set excluded; editable per cycle).

**Account**
Anything that holds a balance: bank, savings, pot, credit_card. User-defined, any number; one is flagged the **main spending account**. Balance is always computed from the journal, never stored or edited directly.

**Pot**
A real envelope in a banking app, separate from the main spending account's balance. Each pot is its own account. Funding a pot is a transfer from the main account.

**Credit card**
A liability account. Balance goes negative as you spend; paying it is a transfer from a bank or pot.

**Travel card**
Not an account (money topped up cannot leave it). A top-up is a spending entry from a chosen account, category `transport`, not counted toward the daily cap. Its balance is a number typed into the travel top-up calculator.

**Journal**
List of entries; the source of truth. Entries are never physically removed. Amounts are integer minor units (e.g. satang for THB), displayed as `1,000.99`. A transfer is two equal and opposite entries and never counts as spending. Editing is allowed; deleting marks an entry *voided* (kept, hidden in the UI).

**Reconcile**
Enter the real bank balance, see the gap versus the computed balance, optionally log the gap as an "unlogged / other" journal entry.

**Daily cap**
Optional per-day spending limit, chosen per cycle: on or off, with an adjustable amount (suggested default 400). Only entries flagged *counts toward cap* are included. The flag defaults per category (food, coffee, drinks, taxi ON; others OFF) and can be overridden per transaction. Under/over-spend accumulates across the cycle (cap × days elapsed − spent).

**Late-night toggle**
For entries inside the late-night window (default 00:00–04:59), a toggle (default OFF) moves the entry to the previous day for cap purposes only.

**Scheduled item**
A fixed cost created from a template by the New-cycle wizard, with an expected date and an adjustable amount. Tapping "paid" posts it to the journal, from the template's paying account. Feeds "upcoming in 7 days" and the buffer math.

**Safe to spend (per day)**
(main account − unpaid scheduled items paid from it this cycle) ÷ days left.

**Buffer**
main account − unpaid scheduled items − (daily cap × days left).

**Base salary**
Optional setting. Salary entered above it prompts moving the excess to a chosen account.

**Goal**
Target (amount or months of expenses) linked to an account. Contribution is per pay cycle (not month); defaults to the average moved into the linked account over recent cycles; overridable. Estimate is in cycles-to-goal.

## Conventions

- Dates and times are stored as local strings in the configured time zone (default Asia/Bangkok); no UTC conversion.
- English UI only; notes may contain Thai.
- Public holidays come from a static bundled list per country (Thailand first), updated yearly.
- Backup is a versioned JSON export (`schemaVersion`); import validates and replaces all data.
