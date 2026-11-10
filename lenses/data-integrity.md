---
name: data-integrity
applies-when: the change writes to a database or storage, changes a schema, runs a migration or a backfill, or moves data between systems
---

# Data integrity

## Hunt for
- writes that can leave records half-updated (no transaction where one is needed)
- schema changes that break running code or rollback (dropping or renaming in the same release that stops using it)
- new required columns with no default and no backfill
- deletes that should be soft, or soft deletes that are not filtered on read
- duplicates from retries (no idempotency key or unique constraint)
- data scoped to an owner, tenant or account that can leak across that boundary
- timezone, precision and encoding loss

## Not this lens
- query speed (performance)
