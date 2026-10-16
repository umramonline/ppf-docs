# PPF — Dokümantasyon

PPF (Paint Protection Film / ürün operasyonları) yazılım planı, gereksinimler ve mimari referansları.

## Katmanlar

| Katman | Dosyalar |
|--------|----------|
| **Plan ve mimari** | [00-product-plan.md](./00-product-plan.md), [01-architecture.md](./01-architecture.md), [02-api-responses.md](./02-api-responses.md) |
| **İş gereksinimleri** | [03-requirements.md](./03-requirements.md) (`FR-xxx`, `NFR-xxx`) |
| **RBAC** | [04-rbac.md](./04-rbac.md) |
| **Domain** | [05-work-order-lifecycle.md](./05-work-order-lifecycle.md) |
| **Entegrasyon** | [06-umran-integration.md](./06-umran-integration.md) |
| **Release / Jira** | [07-release-plan.md](./07-release-plan.md), [jira/README.md](./jira/README.md) |
| **Kararlar** | [adr/README.md](./adr/README.md) |

## Okuma sırası (yeni geliştirici)

1. `00-product-plan.md` — stack ve repolar  
2. `03-requirements.md` — ne inşa ediliyor  
3. `01-architecture.md` — nasıl inşa ediliyor  
4. `04-rbac.md` + `05-work-order-lifecycle.md` — çekirdek davranış  
5. `07-release-plan.md` — hangi fazda ne var  

## Sabit kararlar

- **Veritabanı:** PostgreSQL — [adr/ADR-PPF-004-postgresql.md](./adr/ADR-PPF-004-postgresql.md) (NFR-008)

## Güncelleme kuralı

- Yeni iş kuralı → `03-requirements.md` (+ gerekirse `05` / `06`)  
- API sözleşmesi → org `api-contract` repo (PPF repo’sunda yalnızca referans)  
- Mimari karar → `docs/adr/ADR-PPF-xxx.md`  
- Jira epic/faz değişikliği → `07-release-plan.md` ve `jira/README.md`  

Repo AI girişleri: `../ppf-api/AGENTS.md`, `../ppf-admin-web/AGENTS.md` (+ kısa `CLAUDE.md`).

**İlgili org dokümanları:** `/Users/aras/codespace/umran/docs/platform-mimarisi.md`, `organization-directory-structure.md`

**Yayın (mirror):** [github.com/umramonline/ppf-docs](https://github.com/umramonline/ppf-docs) — bkz. [PUBLISH.md](./PUBLISH.md)

**Son güncelleme:** 2026-10-16
