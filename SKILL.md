---
name: dsh-secret-leakage-scan
version: 1.0.0
description: >-
  Scan agent skills and workspace files for secrets/PII leaks before release —
  hardcoded credentials, tokens, keys, and sensitive patterns. Complements the
  LGD governance gate by blocking data leakage at the PLAN stage.
read_when: >-
  Run before publishing any skill or repository; scan a directory for leaked
  credentials; CI gate for secret leakage.
---

# dsh-secret-leakage-scan

Secrets/PII leak scanner for the LGD agent-skills governance chain (PLAN stage: leak-scan). Detects hardcoded credentials, API tokens, private keys and sensitive personal data in skill bundles and repos.

## Usage

```bash
python dsh_scan.py scan ./target/ --format json
```

## Output
- `--format terminal|json|sarif|badge`
- Findings: credential-leak / secret-pattern / pii-pattern categories

## Governance
Part of LGD three-laws: 有证 (EVIDENCED) / 有门禁 (GATED). Evidence chain: https://github.com/zhaoxinghua09-cell/skillgate
