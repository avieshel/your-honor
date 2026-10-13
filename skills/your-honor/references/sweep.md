# Sweeping for charges

A charge is something the code *did* that could end badly — not a style preference, not a
hypothetical, not a lint warning. Work this list to make sure you have not missed the
category that happens to be quiet today. Not every project will have a charge in each
section, and most will have none in most of them. That is normal and is not a failure to
find something.

## Active or latent?

The single most useful thing you can tell the developer: **is this fire or a landmine?**

- **Active** — there is a reachable path from an input or a scheduler to the bad outcome.
  Grep for callers of the function, and check whether a cron, queue worker, or route
  reaches it.
- **Latent** — the bad code is correct-by-accident or simply never wired up. It is not
  happening today, and it happens the moment someone adds the call.

Both are legitimate charges. Label which one you are looking at, because the defense
changes completely. "Not reachable today" defends *exploitability*, never *danger*, and
the developer must not be allowed to hear the first as though it were the second.

---

## Security

- **Secrets in source** — passwords, API keys, tokens, private keys. Check config files,
  `.env*`, test fixtures, and committed lockfiles. Check whether the value is a real
  production credential or an obvious placeholder: `app_rw` against `db-prod-01` is real.
- **Config that only appears to be safe** — an `.env.example` or secret store that the
  code never reads. A `JWT_SECRET=` line in a template while `jwt.sign` takes a literal is
  worse than having no config at all, because it makes a reviewer stop looking.
- **Injection** — string concatenation or template literals into SQL, shell, LDAP, XPath,
  deserializers, or a URL that gets fetched. Note the *ratio*: when some call sites are
  parameterized and others are not, the author knew the pattern and it was not applied.
  That is evidence of drift, and it is a much better defense than carelessness.
- **Missing or broken auth** — a route with no middleware, an auth check that can never
  fail (`if (!rows)` when `rows` is always an array), a comparison against `undefined`,
  an identity check on the wrong side of a request.
- **Authz, not just auth** — authenticated but not permitted. `user_id` from a query
  parameter rather than the session. Any user able to read any other user's record.
- **Token and session lifetime** — long expiry, no revocation path, no rotation, a secret
  short enough to guess.
- **Untrusted input crossing a trust boundary** — outbound requests to hosts derived from
  input, deserialization of untrusted bytes, path traversal, template rendering of user
  data.
- **Over-broad reads** — `SELECT *` across a table that holds credentials, card data, or
  PII. The shape of the columns is the charge even when the query is parameterized.

## Privacy and data handling

- **PII in logs** — emails, names, card numbers, session tokens, full auth headers. Check
  the log sink and its retention: a log you cannot delete is a breach you cannot undo.
- **PII crossing the boundary** — outbound calls that carry user records. If the payload
  is not currently written, say so precisely. "Collects everything and ships it off-box
  except for one unwritten line" is both accurate and alarming.
- **Missing notice and consent** — personal data stored or exported without the policy
  that authorizes it.
- **Retention** — nothing deletes what it should, or the retention window in code
  contradicts the retention window in the privacy policy.

## Data loss

- **Unguarded mass mutation** — `DELETE`/`UPDATE` with no `WHERE`, no `LIMIT`, no
  transaction, no dry-run flag, inside a loop. Check whether the filter can match every
  row: a unit mismatch (`Date.now()` in milliseconds compared against a `DATETIME`
  coerced to a year number) produces a universal predicate and deletes the whole table.
- **Fetched but unused results** — a query whose result is assigned and never read is a
  safety check that was removed or never written. Look for it near every delete.
- **Non-idempotent operations** — anything that moves money, sends mail, or calls an
  external API, retried without an idempotency key. A retry that succeeds and then fails
  in the follow-up write will charge twice.
- **Referential orphans** — deletion order, missing foreign keys, "or will cascade,
  depending on config". Both outcomes are silent and one of them is losing financial
  records.
- **Backups that were never tested** — code that depends on a restore, with no evidence a
  restore has ever been performed.

## Cost

- **Unbounded work** — a query with no `LIMIT` over a table that grows, a loop that runs
  once per row with a network call inside it, an N+1 in a request path.
- **Unbounded retries** — a retry counter that is incremented and never read, a recursive
  retry with no cap and no backoff. This is both a stack overflow and a bill.
- **Missing timeouts** — any outbound call without a deadline. One slow dependency turns
  into a queue that never drains and a monthly invoice that surprises you.
- **Unbounded memory** — accumulating a response body with no size limit, loading a full
  table into memory to count it.

## Downtime

- **Async rejections on a synchronous framework.** Express 4 does not catch rejected
  promises from handlers. One bad input is a process termination. Check the framework's
  major version before deciding — this was fixed in Express 5.
- **Crash-only recovery** — a job that dies mid-loop leaves the system half-migrated and
  the next run cannot resume.
- **The storm path** — what happens when this thing runs twice, when the network partitions,
  when the database is slow for thirty seconds. Look specifically for missing error
  handlers on the paths that run unattended.
- **Sentinel silence** — `catch (e) { return null }`. Failure that resolves to nothing is
  indistinguishable from success, and it is how jobs report "done" having done nothing.
- **Listening on all interfaces over plaintext** for a service that handles secrets.
- **No health check, no graceful shutdown**, nothing draining in-flight work on deploy.

## Compliance

- **Card data** — any PAN reaching a log, a third party, or an unencrypted channel.
  This is a finding regardless of whether anyone can currently reach it.
- **Password storage** — reversible or raw comparison instead of a slow hash.
- **Retention and deletion** — code behavior that cannot satisfy a documented obligation,
  and data you cannot produce or delete on request.
- **Regionality and licensing** — where the data physically lives versus where it must
  live, and terms you have outgrown.

## Supply chain and posture

- **Stale dependencies** — `express@4.11.1`, `jsonwebtoken@3.2.2`, `lodash@4.17.4` are all
  years past their fixes. Name the version, not the adjective.
- **Unused dependencies** — imported and never referenced. Carries every CVE of a package
  you get no benefit from.
- **Unmaintained packages** — deprecated and no longer receiving patches. Nothing upstream
  is coming.
- **Exact pins with no lockfile** — or a lockfile that has drifted. The difference matters
  for what an attacker can actually reach.
- **Version-shape traps** — code written for one major of a driver and run against
  another. `mysql` returns rows, `mysql2` returns `[rows, fields]`; the same code is
  correct on one and silently wrong on the other. Read what the package actually does.
- **The process gap** — no tests, no CI, no lint gate, no `.gitignore`, no lockfile. This is
  frequently the actual root cause of every other charge, and it is the one charge that
  prevents recurrence. Say so.

## What is not a charge

- Style, naming, formatting, architecture opinions
- Hypotheticals with no reachable path ("if an attacker somehow...")
- Missing best practice with no consequence attached
- Anything you cannot cite
- Anything already handled elsewhere in the codebase — check before charging it