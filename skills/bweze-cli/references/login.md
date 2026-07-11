# npx @bweze/cli login

Authenticate with the BWEZE platform.

## Syntax

```bash
npx @bweze/cli login [options]
```

## Options

| Option | Description |
|--------|-------------|
| `--user-api-key <key>` | Authenticate directly with a `uak_` user API key (no browser, no prompt) — best for headless / agent / CI use |
| `--email` | Use email/password login instead of OAuth |
| `--client-id <id>` | Custom OAuth client ID |

## Authentication Methods

### OAuth (Default)

Opens your browser for OAuth 2.0 authentication with PKCE:

```bash
npx @bweze/cli login
```

The CLI starts a local callback server, opens the browser, and waits up to 5 minutes for you to authorize.

### User API Key (direct) — recommended for headless / agent / CI

No browser, no interactive prompt. Create a key in the dashboard (Profile → API Keys):

```bash
npx @bweze/cli login --user-api-key "$BWEZE_USER_API_KEY"
```

The key is stored and sent directly as the bearer credential on every request — it authenticates as your account with full access. There is no token exchange or refresh: if the key is revoked or expires, the CLI asks you to log in again.

### Email/Password

```bash
npx @bweze/cli login --email
```

Prompts for email and password interactively. For non-interactive use (CI/CD), set environment variables:

```bash
BWEZE_EMAIL=user@example.com BWEZE_PASSWORD=secret npx @bweze/cli login --email
```

## Credential Storage

Credentials are saved to `~/.insforge/credentials.json` with restricted file permissions (0600). The shape depends on the login method:
- OAuth / email — `access_token` (JWT) + `refresh_token`
- User API key — `user_api_key` (the `uak_`, used directly as the bearer)

Plus user info (id, name, email). OAuth/email sessions refresh their JWT automatically on 401; a user-API-key session isn't refreshed — an invalid key prompts a re-login.

## Examples

```bash
# Interactive OAuth login (recommended for humans)
npx @bweze/cli login

# Headless / agent / CI: user API key login (no browser)
npx @bweze/cli login --user-api-key "$BWEZE_USER_API_KEY" --json

# Email/password login
npx @bweze/cli login --email

# CI/CD non-interactive login via email/password
BWEZE_EMAIL=$EMAIL BWEZE_PASSWORD=$PASSWORD npx @bweze/cli login --email --json
```
