---
name: dsh-secret-leakage-scan
description: Use when any content is about to leave the trusted workspace — a commit, PR, post, doc, chat message, published artifact, or log — to scan it for secrets, credentials, PII, and internal paths before they escape the trust boundary; catches API keys, tokens, private keys, cloud credentials, .env secrets, session cookies, and absolute local paths.
---

# Scanning Secret / Credential Leakage Before Egress

Secret leakage is when auth material, PII, or internal references end up in a surface outside the trusted workspace — a public repo, a forum post, a chat, a published doc, or an append-only log. The fix is prevention at the boundary: once a live secret is external, it is irreversible; rotation is the only remedy, and you cannot un-see it.

This skill is guidance, not a script. It tells you what to look for and what to do when you find it. It pairs with the standing rule **never put a secret into conversation or logs in cleartext** — if you find one, flag rotation and stop; do not echo the value back.

## The one test

For every byte about to egress, ask: **would a stranger who reads this surface gain access, recon, or PII they should not have?** If a single string answers yes, the content does not ship until that string is removed or proven non-live.

## Taxonomy — what counts as a leak

1. **API keys & bearer tokens** — `AKIA[0-9A-Z]{16}`, `sk-` / `sk-ant-`, `ghp_` / `gho_` / `ghu_` / `ghs_` / `ghr_` / `github_pat_`, `glpat-`, `xox[baprs]-`, `AIza[0-9A-Za-z_-]{35}`, `ya29.`, `eyJ…` (JWT), `Bearer `, `OPAQUE`, app passwords.
2. **Private keys** — `-----BEGIN … PRIVATE KEY-----`, `-----BEGIN RSA PRIVATE KEY-----`, PuTTY `.ppk` bodies.
3. **Cloud credentials** — AWS `AKIA…` + paired secret; GCP `service-account.json` / `private_key`; `AZURE_*`; connection strings with `;Password=`; `mongodb(+srv)://user:pass@`; `postgres://user:pass@`.
4. **`.env` / config secrets** — `DB_PASSWORD=`, `SECRET_KEY=`, `CLIENT_SECRET=`, `NODE_ENV=production` creds, any `=…` assignment whose value is high-entropy or named secret/token/key/pass.
5. **Session & refresh material** — `sessionid=`, `connect.sid=`, `refresh_token=`, signed cookies, CSRF tokens.
6. **PII in unauthorized contexts** — real emails, phone numbers, ID numbers, health/finance records outside an authorized, consented surface.
7. **Internal paths & hosts** — `/Users/<name>/…`, `C:\Users\…`, absolute workspace paths, internal hostnames, RFC1918 IPs (`10.x`, `192.168.x`, `172.16–31.x`), `.corp` / `.internal` domains.

## What is NOT a leak

Unaided pattern matches fail both ways. Apply these keep rules:

- **Placeholder / example values** — `YOUR_KEY_HERE`, `sk-xxxx`, `AKIAIOSFODNN7EXAMPLE`, `<API_KEY>`, doc samples clearly fenced as non-live. Keep, but note they are placeholders.
- **Public identifiers** — repo URLs, public package names, documented API *endpoints* (not keys), OAuth *client_id* shown by the provider's own docs.
- **Hashes of secrets** — a salted/hashed value with no reversible path is usually fine; a raw unsalted hash of a secret is borderline — prefer removing.
- **Self-referential workspace paths inside the repo** — a relative path within the project is fine; an absolute path pointing at a specific machine user is not.

## Workflow

1. **Name the egress surface and its trust boundary.** A commit to a private repo, a post to a public forum, a message to a colleague, and a published doc each have a different boundary. The more public, the stricter.
2. **Scan the candidate content** against the taxonomy — regex for the shapes above, then a semantic pass for context (is this key real or a sample? is this path machine-specific?).
3. **Triage each hit:**
   - *Live secret* → do **not** redact-and-ship blindly. Remove the value entirely; source it from a vault / env var at runtime. Flag the owner to **rotate** the exposed credential immediately.
   - *Placeholder / example* → keep, annotate why.
   - *Uncertain* → treat as live. When in doubt, block.
4. **Report** every hit with: location, type, severity (live / suspected / placeholder), and recommended action (remove / rotate / keep). Never reprint a live value into the response.
5. **Gate egress.** Confirmed live secrets do not ship. If the surface is a publish/post, require explicit user confirmation **after** the secret is cleared — clearance is a precondition, not a formality.
6. **Already-escaped content.** If you find a live secret in something already pushed/posted, stop the line, flag rotation at once, and do not echo the value back into conversation or logs.

## Failure handling

- A miss is worse than a false positive: when unsure, block and surface it.
- Never transcribe a discovered secret into the reply, a note, or a log line. State its *type and location*, not its *value*.
- If the scan tooling itself would write the value to a log, disable that logging for the run or pipe through a redactor first.
