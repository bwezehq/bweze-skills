# Traffic analytics for a BWEZE frontend project

BWEZE gives each frontend project cookieless, privacy-preserving traffic
analytics — page views, unique visitors (a salted daily hash, no PII), top
paths, and a daily breakdown.

## 1. Add the beacon

Drop the loader into your site's HTML (or inject it in your framework's document
head). It's cookieless and fires a lightweight beacon per page view.

```html
<script
  src="https://api.bweze.com/bweze-analytics.js"
  data-project="<projectId>"
  defer
></script>
```

The beacon POSTs to the public `/api/collect` endpoint (CORS-open, always 204,
no PII, no cookies).

## 2. Read the analytics

```ts
const { analytics } = (await bweze.projects.analytics(projectId, 30)) as {
  analytics: { trafficReady: boolean; traffic: {
    views: number; visitors: number;
    topPaths: { path: string; count: number }[];
    byDay: { day: string; views: number }[];
  } };
};
```

```
GET /v1/projects/{id}/analytics?days=30
```

`days` is clamped to 1–90. `trafficReady` is `false` until the first beacon hit
lands. The console shows the same data plus the beacon snippet to copy.

## What is (and isn't) collected

- **Collected**: path, a per-day salted visitor hash, timestamp.
- **Not collected**: IP (only hashed with a daily salt), cookies, personal data,
  cross-site identifiers. It's not a fingerprinting product.
