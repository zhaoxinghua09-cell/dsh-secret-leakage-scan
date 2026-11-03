# dsh-secret-leakage-scan
[![Trust Score](https://img.shields.io/badge/Trust%20Score-72%2F100%20B-dfb317.svg)](https://github.com/zhaoxinghua09-cell/skillgate)
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

## Ecosystem / 生态闭环

- [awesome-trusted-agents](https://github.com/zhaoxinghua09-cell/awesome-trusted-agents) — 四角度评分精选清单（生态榜单入口）
- [skill-scorecard](https://github.com/zhaoxinghua09-cell/skill-scorecard) — 四角度评分引擎
- [lgd-hub](https://github.com/zhaoxinghua09-cell/lgd-hub) — LGD 理论资产统一入口
- [lgd-skill-marketplace](https://github.com/zhaoxinghua09-cell/lgd-skill-marketplace) — LGD 技能超市总目录
- [governanceos](https://github.com/zhaoxinghua09-cell/governanceos) — 治理编排枢纽（SPEC→PLAN→GATE→AUDIT→EVO）
- [skillgate](https://github.com/zhaoxinghua09-cell/skillgate) — 技能信任评分引擎
