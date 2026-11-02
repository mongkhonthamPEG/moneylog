# 0002. Journal as source of truth; balances are derived

Status: accepted

The journal is the source of truth. Account balances are computed from the opening balance plus journal entries. Transfers are two equal and opposite entries. Entries are never physically removed: deleting sets a voided flag, and reconcile gaps are posted as adjustment entries.

Why: matches how bank ledgers work, keeps export/import exact, and keeps history traceable.
