---
name: security
applies-when: the change handles user input, authentication, authorization, secrets, files, URLs, network calls or permissions
---

# Security

## Hunt for
- missing or bypassable authorization: can user A read or change user B's data by changing an id?
- injection: SQL, shell, template, path traversal, header, log
- secrets or tokens in code, logs, URLs, client bundles or error messages
- unsafe redirects, SSRF through user-supplied URLs
- trusting client-side checks, hidden fields or unsigned tokens
- rate limits and abuse of new endpoints (enumeration, brute force, cost amplification)

## Not this lens
- general bugs with no attacker in the scenario (correctness)
