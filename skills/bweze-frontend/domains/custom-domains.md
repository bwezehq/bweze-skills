# Custom domains for a BWEZE frontend project

Three ways to give a project a domain - a free `*.bweze.app` subdomain, a domain
you buy through BWEZE, or a domain you already own. All get automatic SSL.

## 1. Free `*.bweze.app` subdomain (instant)

Connect `<label>.bweze.app` - it activates immediately (covered by the wildcard
DNS + TLS), no DNS setup:

```bash
bweze projects domains connect <projectId> myapp.bweze.app
```

```ts
await bweze.domains.connect(projectId, "myapp.bweze.app");
```

Reserved infrastructure labels (api, app, www, docs, edge, …) and labels already
taken by another project are rejected.

## 2. Buy a domain through BWEZE

Search availability + first-year price, then buy. Buying attaches it to the
project and (once registered) routes it automatically.

```bash
bweze projects domains search my-brand          # bare name → checked across TLDs
bweze projects domains buy <projectId> my-brand.com
```

```ts
const { results } = await bweze.domains.search("my-brand");
await bweze.domains.buy(projectId, "my-brand.com");   // spends money
```

```
GET  /v1/domains/search?q=my-brand
POST /v1/projects/{id}/register-domain { "domain": "my-brand.com" }
```

Availability is real (RDAP-backed). Registration is fulfilled by BWEZE; the
domain shows `requested` until it registers, then it is live.

## 3. Connect a domain you already own

Returns two DNS records to add at your registrar; then verify.

```bash
bweze projects domains connect <projectId> app.example.com
# add the printed TXT + CNAME records at your DNS provider, then:
bweze projects domains verify <domainId>
```

```ts
await bweze.domains.connect(projectId, "app.example.com");
await bweze.domains.verify(domainId);
```

The two records:

| Type | Name | Value |
|---|---|---|
| `TXT` | `_bweze-challenge.<your-domain>` | the verification token returned on connect |
| `CNAME` | `<your-domain>` | `edge.bweze.app` |

Once both resolve, the domain verifies and goes live with SSL. Detach with
`DELETE /v1/domains/{id}`.

## Listing + status

```bash
bweze projects domains list <projectId>
```

Status values: `pending` (add DNS) → `verifying` → `verified`/`active`, or
`error` (records not found yet - re-verify).
