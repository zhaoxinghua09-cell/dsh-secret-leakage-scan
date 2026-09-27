# dsh-secret-leakage-scan
## 许可说明 · License Notice

- **权利状态**：本仓库以 **MIT 许可** 许可发布，可依该许可证条款自由使用、修改与再分发。
- **引用建议**：引用时请标注仓库名与原文链接 `https://github.com/zhaoxinghua09-cell/dsh-secret-leakage-scan`
  与权利人「赵兴华 / Steven Zhao·China」。
- **品牌状态限定**：MedXpert、SynomosAI、LGD 等为相关项目标识，
  **均未申请实体注册、未申请商标注册**；出现仅作来源标识，
  不构成对法人实体或商标权的任何主张。
- **完整条款**：见仓库根目录 [LICENSE](LICENSE)。
- **联系**：zhaoxinghua06@126.com ｜ ORCID 0009-0001-0512-1237

---


A **DeepSeek Harness (DSH)** skill/plugin that scans content about to leave the trusted workspace — commits, PRs, posts, docs, chat, published artifacts, logs — for **secret / credential / PII / internal-path leakage** before it escapes.

> Why it exists: once a live secret hits a public repo, a post, or an append-only log, it is irreversible. Rotation is the only fix, and you can't un-see it. This skill is the gate at the boundary. It echoes the standing rule *never put a secret into conversation or logs in cleartext*.

## What it catches

API keys & bearer tokens (`AKIA…`, `sk-` / `sk-ant-`, `ghp_` / `github_pat_`, `glpat-`, `xox[baprs]-`, `AIza…`, `ya29.`, `eyJ…` JWT, `Bearer `), private keys (`-----BEGIN … PRIVATE KEY-----`), cloud credentials (AWS/GCP/Azure/connection strings), `.env` secrets, session/refresh cookies, PII in unauthorized contexts, and absolute internal paths/hosts.

Full taxonomy + keep-rules + workflow are in [`skills/dsh-secret-leakage-scan/SKILL.md`](skills/dsh-secret-leakage-scan/SKILL.md).

## Install (DSH plugin)

```bash
npx @deepseek-ai/dsh plugin --profile web add github:<owner>/dsh-secret-leakage-scan
# pin for reproducible deployments:
npx @deepseek-ai/dsh plugin --profile web add github:<owner>/dsh-secret-leakage-scan#<commitSHA>
```

The plugin mounts `./skills` via `cordis.patch.yml` so DSH discovers the `dsh-secret-leakage-scan` skill.

## Use

Once loaded, the skill is invoked automatically when content is about to egress, or force it:

```
/skill dsh-secret-leakage-scan
```

It reports every hit (location, type, severity, action) and gates egress on confirmed live secrets — without ever reprinting a live value into the reply.

## Make it discoverable in the ecosystem

Add the `dsh-plugin` topic to the GitHub repo; the DSH Plugin Ecosystem syncs `dsh-plugin`-tagged repos every 6 hours and validates the manifest. (Held until explicit publish step.)

## License

MIT
