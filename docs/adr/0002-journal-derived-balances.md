# 0002. Append-only journal; balances are derived

Status: accepted

The journal is the source of truth. Account balances are computed from the opening balance plus journal entries. Transfers are two equal and opposite entries. Reconcile gaps and deletes are journal entries (adjustment entry; voided flag), not mutations.

Why: matches how bank ledgers work, keeps export/import exact, and keeps history traceable.
