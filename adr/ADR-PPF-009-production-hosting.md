# ADR-PPF-009: Production barındırma

- **Durum:** Kabul
- **Tarih:** 2026-10-16
- **Karar verenler:** Ürün / platform (PPF-91)

## Bağlam

PPF üç deployable birim: `ppf-api` (Go), `ppf-admin-web` (static SPA), `ppf-customer-web` (Next.js). NFR-008 PostgreSQL zorunlu; NFR-009 health/ready mevcut.

## Karar

| Bileşen | Hedef | Not |
|---------|--------|-----|
| **ppf-api** | Container (Docker) → org container registry | `Dockerfile` mevcut; immutable tag promote |
| **PostgreSQL** | Yönetilen PostgreSQL (RDS / Cloud SQL / Azure DB) | Tek cluster, goose migration deploy öncesi |
| **ppf-admin-web** | Static object storage + CDN (S3+CloudFront, GCS+LB, vb.) | `vite build` → `dist/` |
| **ppf-customer-web** | Next.js standalone veya Node host / Vercel eşdeğeri | SSR + public verify |
| **IdP** | Ayrı cluster (Keycloak — bkz. ADR-PPF-003 güncelleme) | OIDC PKCE clients |
| **Secret’ler** | Ortam secret store (K8s secrets / vault) | `.env` production’da yok |

Ortamlar: **staging** → **production** (manual approve gate). CI: `.github/workflows/deploy.yml` (workflow_dispatch).

## Sonuçlar

- Lokal geliştirme docker-compose ile kalır; production IaC ayrı repo veya platform ekibi sorumluluğu.
- Medya dosyaları MVP: local volume; production: object storage adapter (ileride).

## Alternatifler

- Tek VM + systemd — reddedildi (ölçek ve rollback zayıf).
- Serverless API — reddedildi (uzun bağlantılar, outbox worker).
