---
name: product
applies-when: the change is visible to users (UI, copy, emails, messages, API responses) or changes a user flow
---

# Product

## Hunt for
- the change does not actually solve the problem in the spec or ticket
- states nobody designed: loading, empty, error, partial success, no permission
- copy that is wrong, inconsistent, or exposes internals (ids, stack traces, internal names)
- a flow that strands the user (no way back, no next step, lost input)
- accessibility basics: keyboard reach, labels, contrast, focus after actions
- behavior that silently changes for existing users

## Not this lens
- visual taste, unless it breaks usability
