# Keycloak — PPF realm kurulum özeti

> ADR-PPF-003 · Production giriş (PPF-84/85/86)

## Local Keycloak (dev)

```bash
cd ppf-api
make idp-up
# Admin console: http://localhost:8081 (admin / admin)
# Realm: ppf (imported from deploy/keycloak/ppf-realm.json)
# Demo user: admin.demo / demo-demo
```

API (JWT resource server):

```bash
cp .env.keycloak.example .env
make migrate-up && make run
```

Admin SPA:

```env
VITE_AUTH_MODE=oidc
VITE_OIDC_ISSUER=http://localhost:8081/realms/ppf
VITE_OIDC_CLIENT_ID=ppf-admin
VITE_OIDC_REDIRECT_URI=http://localhost:5173/callback
```

Customer portal: client `ppf-customer`, redirect `http://localhost:3000/api/auth/callback`.

### OIDC smoke (PPF-138)

Önkoşul: `make docker-up`, `make idp-up`, `make migrate-up`, API `.env` = `.env.keycloak.example`, `make run`.

```bash
cd ppf-api
make oidc-smoke
```

Script: Keycloak hazır → password grant (`admin.demo`) → `GET /v1/admin/me` JWT doğrulama.

Admin SPA (manuel): `ppf-admin-web` `.env` OIDC değerleriyle `npm run dev`, tarayıcıda login + API çağrısı.

## Realm

- Ad: `ppf`
- Issuer: `https://<idp-host>/realms/ppf`

## Clients

| Client | Public | PKCE | Redirect URI (örnek) |
|--------|--------|------|----------------------|
| `ppf-admin` | evet | S256 | `http://localhost:5173/callback` |
| `ppf-customer` | evet | S256 | `http://localhost:3000/api/auth/callback` |
| `ppf-webhook-m2m` | hayır | — | service account; client secret (prod: secret store); token `aud` → `ppf-api` |

## Mapper’lar (JWT)

| Claim | Kaynak | Client |
|-------|--------|--------|
| `ppf_roles` / `roles` | user attribute veya group | admin |
| `branch_id`, `region_id`, `supplier_id` | user attribute | admin |
| `customer_umram_id` | user attribute | customer |

## API

```env
AUTH_MODE=jwt
JWT_JWKS_URL=https://<idp>/realms/ppf/protocol/openid-connect/certs
JWT_ISSUER=https://<idp>/realms/ppf
JWT_AUDIENCE=ppf-api
```

Local dev smoke hâlâ `JWT_AUDIENCE=ppf-admin` (`.env.keycloak.example`); production/staging **`ppf-api`**. Realm import: `ppf-admin` ve `ppf-webhook-m2m` client’larında `audience-ppf-api` mapper.

**M2M webhook (prod):** Keycloak client `ppf-webhook-m2m`, service account enabled; API ve ingress bu client’ın client-credentials token’ını `Authorization: Bearer` ile doğrular (`JWT_AUDIENCE=ppf-api`). Dev secret: `deploy/keycloak/ppf-realm.json` (rotate in prod).
```

## SPA

**Admin:** `VITE_AUTH_MODE=oidc` + `VITE_OIDC_*` (.env.example)

**Customer:** `NEXT_PUBLIC_AUTH_MODE=oidc` + redirect `NEXT_PUBLIC_OIDC_REDIRECT_URI` = callback route
