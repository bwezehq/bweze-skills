---
name: bweze-frontend
description: >-
  Use this skill when deploying or managing a BWEZE FRONTEND project — a web app or static site hosted on BWEZE (the Vercel/Coolify-style surface). Covers git-based deploys, build and environment configuration, custom domains (buy one through BWEZE or connect your own, with automatic SSL), a free *.bweze.app subdomain, traffic analytics, scheduled cron jobs, an IP firewall, deploy/runtime logs, and the project terminal. Trigger on requests like: deploy my site, host my frontend, put my app online, add a custom domain, buy a domain, connect my domain, set env vars, see my traffic or analytics, schedule a job, block an IP. For the backend/data surface (Postgres, auth, storage, functions, the @bweze/sdk data client) use the bweze skill; for command-line project management use bweze-cli; for diagnosing failures use bweze-debug.
license: Apache-2.0
metadata:
  author: bweze
  version: "1.0.0"
  organization: BWEZE
  date: July 2026
---

# BWEZE Frontend Hosting Skill

This skill covers **frontend (web app) projects** on BWEZE — the hosting and deploy
surface. For the backend data surface (Postgres, auth, storage, edge functions,
realtime, the `@bweze/sdk` data client) use the **bweze** skill instead.

## Projects are typed: frontend vs backend

Every BWEZE project has a type:

- **`frontend`** — a web app / site deploy. Push from git; it goes live on
  `*.bweze.app` with automatic SSL. This skill.
- **`backend`** — a Postgres data stack (auth, storage, realtime, functions). Use
  the `bweze` skill.

You choose the type at creation. Domains, deploys, env, analytics, cron, and
firewall all belong to a project.

Authenticate the API/SDK/CLI with a BWEZE key (`Authorization: Bearer bw_sk_…`).

## Create a frontend project

```bash
# CLI
bweze projects create my-app --type frontend
```

```ts
// SDK — configure with your BWEZE key
import { createClient } from "@bweze/sdk";
const bweze = createClient({ bweze: { apiKey: process.env.BWEZE_API_KEY! } });
await bweze.projects.create({ name: "my-app", type: "frontend" });
```

```bash
# API
curl -X POST https://api.bweze.com/v1/projects \
  -H "authorization: Bearer bw_sk_..." -H "content-type: application/json" \
  -d '{ "name": "my-app", "type": "frontend" }'
```

## The frontend surface — what you can do

| Area | How | Details |
|---|---|---|
| **Deploy** | git push → build → live on `*.bweze.app` | See `deploy/hosting.md` |
| **Env vars** | per-project build/runtime env | `deploy/hosting.md` |
| **Custom domains** | buy through BWEZE, connect your own, or a free `*.bweze.app` | `domains/custom-domains.md` |
| **Analytics** | cookieless beacon + per-project traffic | `analytics/traffic.md` |
| **Cron jobs** | schedule an http(s) POST on a 5-field cron | `automation/cron-and-firewall.md` |
| **Firewall** | allow/block IPs & CIDRs at the edge | `automation/cron-and-firewall.md` |
| **Logs & terminal** | build + runtime logs, interactive shell | `deploy/hosting.md` |

## Quick reference

```bash
# Domains
bweze projects domains search my-brand              # availability + price
bweze projects domains buy   <projectId> acme.com   # buy through BWEZE
bweze projects domains connect <projectId> app.example.com   # bring your own
bweze projects domains verify <domainId>            # re-check DNS
```

```ts
// SDK
await bweze.domains.search("my-brand");
await bweze.domains.connect(projectId, "app.example.com");
await bweze.projects.analytics(projectId, 30);
await bweze.projects.createCron(projectId, { name: "nightly", schedule: "0 0 * * *", target_url: "https://…" });
await bweze.projects.createFirewallRule(projectId, { action: "block", cidr: "1.2.3.4/32" });
```

## Choosing between this skill and the others

- **bweze-frontend** (this): host a web app — deploys, domains, analytics, cron, firewall, env, logs.
- **bweze**: the data/backend surface — database CRUD, auth, storage, functions, realtime, payments via `@bweze/sdk`.
- **bweze-cli**: command-line project management (`@bweze/cli`) across both project types.
- **bweze-debug**: diagnose deploy, DNS, SDK, and runtime failures.
