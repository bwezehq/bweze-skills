# Deploying a BWEZE frontend project

A frontend project builds from a git repository and serves on `*.bweze.app` with
automatic SSL (Cloudflare-fronted). Zero-config for common frameworks; a build
command + output dir for the rest.

## Deploy from git

1. Create the project (`--type frontend`) — see the main SKILL.md.
2. Connect a git repository (GitHub). BWEZE builds on every push to the tracked
   branch and serves the result.
3. The app goes live at a `*.bweze.app` URL immediately; add a custom domain when
   ready (`domains/custom-domains.md`).

Rollbacks and preview URLs follow the same deploy record — a new push supersedes
the previous build; redeploy an older commit to roll back.

## Environment variables

Set build- and runtime-time env vars per project (e.g. `VITE_*`, `NEXT_PUBLIC_*`,
API base URLs). They are injected at build time for static frameworks and at
runtime for server frameworks.

- If the app talks to a BWEZE **backend** project, use that backend's URL + anon
  key (from the backend project's keys) — not your `bw_sk_` platform key.
- If the app calls the BWEZE **platform** API/SDK from the server, pass
  `BWEZE_API_KEY` as a server-only secret; never expose `bw_sk_` to the browser.

## Logs & terminal

- **Logs**: build logs (per deploy) and runtime logs are available per project in
  the console and via the CLI.
- **Terminal**: an interactive, audited shell into the running deployment for
  quick inspection (env, processes, a one-off command). Prefer it for debugging,
  not as a deploy mechanism — deploys come from git.

## Notes

- Apps are served over HTTPS on `bweze.app` (Cloudflare TLS on 443); internal
  origins are plain http behind that edge.
- A project can pair a frontend with a backend — the frontend is the web app, the
  backend is its data stack. Manage the backend with the `bweze` skill.
