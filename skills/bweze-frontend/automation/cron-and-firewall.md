# Scheduled jobs (cron) and the IP firewall

Two per-project automation surfaces for frontend projects: scheduled HTTP jobs
and an edge IP allow/block list.

## Scheduled jobs (cron)

A cron job POSTs to an http(s) URL on a 5-field cron schedule (minute hour dom
month dow). BWEZE's scheduler fires due jobs every minute.

```ts
// create
await bweze.projects.createCron(projectId, {
  name: "nightly-rebuild",
  schedule: "0 0 * * *",              // every day at 00:00
  target_url: "https://my-app.bweze.app/api/rebuild",
});

await bweze.projects.listCron(projectId);
await bweze.projects.setCronEnabled(jobId, false);   // pause
await bweze.projects.deleteCron(jobId);
```

```
GET|POST /v1/projects/{id}/cron
PATCH    /v1/cron/{id}   { "enabled": false }
DELETE   /v1/cron/{id}
```

Notes:
- The schedule is a standard 5-field cron expression.
- The runner is SSRF-hardened: it resolves the target once and pins the
  connection to a validated **public** IP, follows no redirects, and refuses
  private/loopback/link-local targets. Point it at public URLs only.

## IP firewall (allow / block)

Allow or block individual IPs or CIDR ranges at the edge for a project.

```ts
await bweze.projects.createFirewallRule(projectId, {
  action: "block",              // "allow" | "block"
  cidr: "203.0.113.0/24",
  note: "abusive range",
});

await bweze.projects.listFirewall(projectId);
await bweze.projects.deleteFirewallRule(ruleId);
```

```
GET|POST /v1/projects/{id}/firewall   { "action": "allow"|"block", "cidr": "…", "note"?: "…" }
DELETE   /v1/firewall/{id}
```

Rules are stored immediately; enforcement pushes an edge IP-allow-list middleware
onto the project's router. `cidr` accepts a single IP (`1.2.3.4`) or a CIDR
(`1.2.3.0/24`).
