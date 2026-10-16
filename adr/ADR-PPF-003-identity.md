# ADR-PPF-003: Kimlik — IdP ve JWT claim şeması

- **Durum:** Kabul (production IdP seçildi — PPF-84)
- **Tarih:** 2026-10-15 · Güncelleme: 2026-10-16

## Bağlam

`ppf-admin-web` OIDC Authorization Code + PKCE ile token alır; `ppf-api` resource server olarak JWT doğrular (NFR-001).

## Karar (bootstrap)

| Ortam | `AUTH_MODE` | Davranış |
|-------|-------------|----------|
| Yerel geliştirme | `dev` (varsayılan) | `Authorization: Bearer` zorunlu; roller `X-Dev-Role` (csv); `X-Dev-Branch-Id`, `X-Dev-Supplier-Id` opsiyonel |
| Production hedef | `jwt` | JWKS (`JWT_JWKS_URL`), isteğe bağlı `JWT_ISSUER`, `JWT_AUDIENCE`; audience örn. `ppf-api` |

### JWT claim şeması (hedef)

| Claim | Admin | Customer |
|-------|-------|----------|
| `sub` | Kullanıcı id | Kullanıcı id |
| `roles` / `ppf_roles` | `hq_admin`, `branch_admin`, … | `customer` |
| `branch_id` | Bayi scope | — |
| `supplier_id` | Tedarikçi scope | — |
| `region_id` | Bölge scope | — |
| `customer_umram_id` | — | Umran müşteri id (IDOR) |

OAuth clients (PKCE): `ppf-admin`, `ppf-customer` — [00-product-plan.md](../00-product-plan.md) §6.

### Production IdP (PPF-84)

**Seçim: Keycloak** (org mevcut veya yeni realm `ppf`).

| Client | Grant | Redirect URI örneği |
|--------|-------|------------------------|
| `ppf-admin` | Authorization Code + **PKCE** (S256) | `https://admin.ppf.example/callback` |
| `ppf-customer` | Authorization Code + **PKCE** | `https://customer.ppf.example/callback` |
| `ppf-webhook-m2m` | Client credentials | — (Umran → PPF webhook) |

Realm mapper’lar: `ppf_roles`, `branch_id`, `region_id`, `supplier_id`, `customer_umram_id` (customer client).

API: `AUTH_MODE=jwt`, `JWT_JWKS_URL=https://<idp>/realms/ppf/protocol/openid-connect/certs`, `JWT_ISSUER=https://<idp>/realms/ppf`, `JWT_AUDIENCE=ppf-api`.

Alternatifler (Entra ID, Zitadel) aynı claim şeması ile değiştirilebilir; client id’ler sabit kalır.

Yerel geliştirme: `AUTH_MODE=dev` yeterli; SPA `VITE_AUTH_MODE=dev` / `NEXT_PUBLIC_AUTH_MODE=dev`.

## Sonuçlar

- `/v1/admin/*` route’ları auth middleware arkasında (health/ready public).
- `GET /v1/admin/me` principal doğrulama için.
- Admin SPA: ADR kapanınca gerçek OIDC client; şimdilik login placeholder.

## Referans

- [04-rbac.md](../04-rbac.md)
- `ppf-api/internal/platform/http/middleware/auth.go`
