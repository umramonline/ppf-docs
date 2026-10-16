# PPF — Ürün ve Teknoloji Planı

> Durum: Onaylı taslak · Tarih: 2026-10-15 · Kapsam: PPF bağımsız ürün hattı

Bu doküman, PPF için kararlaştırılmış teknoloji yığınını, GitHub repo stratejisini, umramonline ile entegrasyon kurallarını ve fazlama yol haritasını tanımlar. Yeni repo açılışında ve ADR yazımında referans alınır.

---

## 1. Amaç ve konumlandırma

PPF, Umran ekosisteminde **bağımsız bir ürün** (`products/ppf`) olarak konumlanır:

- **Kendi iş domain’i ve veritabanı** PPF’ye özgü kayıtları tutar (iş emri, uygulama, medya, PPF stok/süreç vb.).
- **Çekirdek iş verisi** (müşteri master, araç, expertise, ödeme, Plus Card, merkezi stok kuralları) **umramonline**’da kalır; PPF tekrar yazmaz, yalnızca **API ve/veya imzalı event** ile tüketir.
- **Doğrudan umramonline (veya başka servis) veritabanına bağlanmak yasaktır** (platform entegrasyon kuralı).

---

## 2. Karar özeti

| Konu | Karar |
|------|--------|
| Backend | **Go** — modüler monolith (`ppf-api`) |
| Admin arayüz | **React 19 + TypeScript + Vite** (`ppf-admin-web`) |
| Müşteri web (sonra) | **Next.js + TypeScript** (`ppf-customer-web`) |
| Mobil | **Kapsam dışı (2026 program)** — ileride ihtiyaç olursa Flutter / `ppf-mobile`; bkz. [ADR-PPF-011](./adr/ADR-PPF-011-mobile-out-of-scope.md) |
| Veritabanı | **PostgreSQL** — [ADR-PPF-004](./adr/ADR-PPF-004-postgresql.md); migration (**goose** / **golang-migrate**); production’da AutoMigrate yok |
| API sözleşmesi | **OpenAPI 3.1**, org **`api-contract`** repo (`openapi/ppf.yaml` veya admin/customer split) |
| Kimlik | **OIDC** (hedef): ayrı OAuth client — admin SPA vs customer; API **JWT resource server** (JWKS, `aud`, scope) |
| Yanıt formatı | 2xx: `{ data }`; 4xx/5xx: **RFC 9457** `application/problem+json` — bkz. [02-api-responses.md](./02-api-responses.md) |
| İsimlendirme | Schema, API alanları, Go tanımlayıcıları **İngilizce**; UI metinleri **Türkçe** |

---

## 3. GitHub repoları

Deploy edilen her uygulama **ayrı GitHub reposudur**. Yerelde `/Users/aras/codespace/umran/ppf/` altında klasör gruplaması yapılabilir; git remote’lar repolara işaret eder.

### Şimdi (MVP)

| Repo | Yerel klasör | Rol |
|------|----------------|-----|
| **`ppf-api`** | `ppf/ppf-api/` | Go HTTP API, domain, PostgreSQL |
| **`ppf-admin-web`** | `ppf/ppf-admin-web/` | İç operasyon / bayi paneli (React + Vite) |

### Sonra

| Repo | Rol |
|------|-----|
| **`ppf-customer-web`** | Müşteri portalı (Next.js, SEO/SSR) |

**Kapsam dışı (şimdilik):** `ppf-mobile` — [ADR-PPF-011](./adr/ADR-PPF-011-mobile-out-of-scope.md).

### Org ortak (PPF’ye özel repo değil)

| Repo | Rol |
|------|-----|
| **`api-contract`** | `openapi/ppf.yaml`, CHANGELOG, entegrasyon ADR |
| **`standards`** / **`ui-kit`** | Org kuralları, paylaşılan React bileşenleri |

**GitHub ACL:** `ppf-admin-web` en dar team erişimi; contractor’a verilmez.

---

## 4. Teknoloji yığını (detay)

### 4.1 `ppf-api` (Go)

| Alan | Seçim |
|------|--------|
| Go | 1.22+ |
| HTTP | **chi** veya **echo** (Fiber alternatif; tutarlılık için ADR) |
| DB | PostgreSQL + **sqlc** veya **GORM** |
| Migration | goose / golang-migrate |
| Config | env + `.env.example` (secret’lar vault / GitHub Environments) |
| Auth | OAuth2 resource server middleware; scope/RBAC |
| Dosya | S3-compatible object storage |
| Cache / rate limit | Redis (ihtiyaç halinde) |
| Arka plan iş | Redis queue (**asynq** vb.) — thumbnail, webhook retry |
| Gözlem | OpenTelemetry / **Sentry**, `/health`, `/ready`, **X-Request-Id** / traceparent |

**Mimari:** Clean / hexagonal — `internal/<module>/{domain,application,infrastructure}`, composition root `cmd/api/main.go`. Ayrıntı: [01-architecture.md](./01-architecture.md).

### 4.2 `ppf-admin-web` (React)

| Alan | Seçim |
|------|--------|
| React | 19 |
| Build | **Vite** |
| Dil | TypeScript (strict) |
| Routing | React Router |
| Veri | **TanStack Query** |
| API client | OpenAPI codegen (orval / openapi-typescript + openapi-fetch) |
| Form | React Hook Form + **Zod** |
| Stil | Tailwind + **@umran/ui-kit** (hazır olunca) veya shadcn/Radix |
| Test | Vitest + **Playwright** (kritik akışlar) |
| CI | ESLint, Prettier, `tsc`, test, gitleaks |

Auth: SPA **Authorization Code + PKCE** → access token → `ppf-api` (`Authorization: Bearer`).

### 4.3 `ppf-customer-web` (gelecek — Next.js)

| Alan | Seçim |
|------|--------|
| Framework | **Next.js** (App Router) |
| Dil | TypeScript |
| Veri / form | TanStack Query, RHF + Zod (admin ile aynı kalıp) |
| UI | ui-kit |
| Auth | Ayrı OAuth **client**; PKCE; gerekirse BFF/cookie pattern |

Customer API yüzeyi: `ppf-api` **`/v1/customer/*`** (admin **`/v1/admin/*`**).

---

## 5. API ve sözleşme

- **Yanıt/hata:** [02-api-responses.md](./02-api-responses.md) (RFC 9457; legacy `success: false` hata **yok**).
- **Contract-first:** Yeni endpoint önce `api-contract` PR → semver tag → `ppf-api` implementasyon → client regenerate.
- **Versiyon:** URL prefix `/v1`; breaking change → `/v2` + deprecation süresi.
- **OpenAPI ayrımı:** Tek `ppf.yaml` (tag: Admin / Customer) veya `ppf-admin.yaml` + `ppf-customer.yaml` — ekip tercihini ADR-PPF-002 ile kilitle.
- **Idempotency-Key** yazma/ödeme benzeri endpoint’lerde.
- **Servis → servis (umram):** Scope’lu, süreli token veya client credentials; **tek global API key yok**.

---

## 6. Kimlik doğrulama

| İstemci | OAuth client | API audience / scope |
|---------|----------------|----------------------|
| ppf-admin-web | `ppf-admin` (PKCE) | `ppf-api`, admin scopes |
| ppf-customer-web | `ppf-customer` (PKCE) | `ppf-api`, customer scopes |
| umram → PPF webhook | M2M client | `webhook:consume` vb. |

IdP: Keycloak, Zitadel, Entra ID veya org seçimi — **ADR-PPF-003 Identity**.

RBAC: personel rolleri (şube, bölge); müşteri yalnızca kendi kaynakları (IDOR testleri zorunlu).

---

## 7. umramonline entegrasyonu

- HTTP client: `internal/integration/umramonline` (Anti-Corruption Layer).
- Okuma: müşteri/araç/şube referansları umram id ile cache’lenebilir.
- Yazma: yalnızca umram’ın expose ettiği API; PPF çekirdek fiyat/ödeme kurallarını duplicate etmez.
- Webhook: HMAC imza, idempotent handler, dead-letter / retry.
- Doküman: `api-contract/docs/integrations/umram-ppf.md` (org repo).

---

## 8. Her repo standart iskeleti

```
<repo>/
├── AGENTS.md
├── CLAUDE.md              # @AGENTS.md, ~200 satır üstü
├── README.md
├── docs/
├── .github/workflows/ci.yml
├── .env.example
├── Dockerfile
└── (src / cmd / internal …)
```

**CI kapıları (Go):** gofmt, golangci-lint, go test, gitleaks.

**CI kapıları (React):** eslint, prettier, tsc, vitest.

Deploy hedefi: immutable Docker → registry → ortam promote (platform standardı).

---

## 9. Domain modülleri (ilk kesim)

İş netleştikçe genişler; başlangıç bounded context önerisi:

| Modül | Sorumluluk |
|-------|------------|
| `auth` / `authorization` | JWT, RBAC |
| `branch` | Şube (umram referans + local) |
| `supplier` | Tedarikçi firması |
| `customer` / `vehicle` | Umram id + local cache |
| `work_order` | PPF uygulama işi |
| `appointment` | Randevu (ileride; bkz. [03-requirements.md](./03-requirements.md) FR-1301) |
| `inventory` | Film/stok (merkezi stok kuralları **insan onayı**) |
| `pricing` | Fiyat listeleri, segment, sorgu |
| `billing` | Tahsilat kaydı, fatura kuyruğu, konsinye (ADR sınırı) |
| `commission` | Prim kuralları ve tahakkuk |
| `notification` | SMS/e-posta kayıt ve gönderim |
| `media` | Foto, belge, form ekleri |
| `warranty` | Garanti ve sertifika |
| `dashboard` | KPI ve raporlar |
| `integration` | Webhook consume, umram client |

---

## 10. Yol haritası

| Faz | Kapsam | Çıktı |
|-----|--------|--------|
| **0** | ADR stack + identity; `api-contract` ppf iskelet | Spec + boş CI |
| **1** | `ppf-api` health, auth, 1–2 admin CRUD; `ppf-admin-web` login + liste | 2 repo deploy test |
| **2** | Umram read entegrasyonu; medya upload; RBAC | Entegrasyon dokümanı |
| **3** | `ppf-customer-web` (Next); `/v1/customer/*` | 3. repo |
| **4** | Outbox/event (yüksek entegrasyon hacminde) | Async ADR |
| *(ertelenmiş)* | ~~Flutter / `ppf-mobile`~~ | ADR-PPF-011 — **yapılmayacak** (ileride belki) |

---

## 11. AI ve insan onayı

- Kurallar repo `AGENTS.md` + org `standards`.
- **İnsan onayı:** fiyatlandırma, merkezi stok, ödeme, yetkilendirme, migration, KVKK/PII.
- Mobil API veya contract breaking değişiklik PR açıklamasında **açıkça belirtilir**.

---

## 12. ADR durumu

- [x] ADR-PPF-001: chi + pgx + goose — [ADR-PPF-001-go-stack.md](./adr/ADR-PPF-001-go-stack.md)
- [x] ADR-PPF-002: OpenAPI tek dosya + tag — [ADR-PPF-002-openapi-layout.md](./adr/ADR-PPF-002-openapi-layout.md)
- [x] ADR-PPF-003: JWT claim bootstrap — [ADR-PPF-003-identity.md](./adr/ADR-PPF-003-identity.md)
- [x] ADR-PPF-011: Mobil kapsam dışı — [ADR-PPF-011-mobile-out-of-scope.md](./adr/ADR-PPF-011-mobile-out-of-scope.md)
- [x] ADR-PPF-004: PostgreSQL — [ADR-PPF-004-postgresql.md](./adr/ADR-PPF-004-postgresql.md)
- [ ] ADR-PPF-005: MVP scope (hangi umram endpoint’leri)

Diğer ürün ADR’leri: [adr/README.md](./adr/README.md)

---

## 13. İlgili yollar

| Kaynak | Path |
|--------|------|
| Yerel API | `../ppf-api/` |
| Yerel admin | `../ppf-admin-web/` |
| Platform mimarisi | `../../docs/platform-mimarisi.md` |
| Org dizin yapısı | `../../docs/organization-directory-structure.md` |
