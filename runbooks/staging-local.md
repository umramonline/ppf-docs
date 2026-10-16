# Staging / local full stack

## 1. PostgreSQL

```bash
cd ppf-api
make docker-up
make migrate-up
```

## 2. API

**Host (default):**

```bash
cp .env.example .env
make run
# http://localhost:8080/health
```

**Container (postgres + API):**

```bash
docker compose --profile full up -d --build
# API on http://localhost:8080/health (AUTH_MODE=dev, mock integrations)
```

## 3. Admin SPA

```bash
cd ppf-admin-web
cp .env.example .env   # VITE_AUTH_MODE=dev
npm ci && npm run dev
```

## 4. Customer portal

```bash
cd ppf-customer-web
cp .env.example .env
npm ci && npm run dev
```

## 5. Production-like auth (optional)

Keycloak realm: [keycloak-ppf.md](./keycloak-ppf.md)

```bash
cd ppf-api && make idp-up
cp .env.keycloak.example .env   # AUTH_MODE=jwt against localhost:8081
make oidc-smoke                 # JWT smoke (API running)
```

- Admin: `VITE_AUTH_MODE=oidc`
- API: `AUTH_MODE=jwt`
- Customer: `NEXT_PUBLIC_AUTH_MODE=oidc`, redirect `http://localhost:3000/api/auth/callback`

## 6. Deploy (CI)

GitHub **workflow_dispatch** → `deploy` workflow in each repo (build image / static bundle). Registry push hedefi ADR-PPF-009.

**ppf-api staging:** `deploy` (environment = staging) build sonrası otomatik **smoke-staging** job — Postgres + migrate + API (`AUTH_MODE=dev`) + `make staging-smoke` (`/health`, `/ready`, `/v1/admin/ping`).

Canlı staging URL’leri için ayrı workflow: **staging-smoke** (`workflow_dispatch`, `api_base` input; opsiyonel `STAGING_ADMIN_BEARER` secret).

```bash
cd ppf-api && make staging-smoke   # localhost:8080, Bearer dev
```
