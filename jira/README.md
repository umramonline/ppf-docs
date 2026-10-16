# PPF — Jira backlog rehberi

Proje: **PPF** (Jira adı: `ppf.umranoto.com`).

Doküman kaynakları: [03-requirements.md](../03-requirements.md) (`FR-xxx`), [07-release-plan.md](../07-release-plan.md) (fix version ve epic indeksi).

---

## 1. Proje kurulumu (bir kez)

| Alan | Durum (PPF projesi) |
|------|---------------------|
| **Fix versions** | Oluşturuldu: `PPF-MVP-1`, `PPF-MVP-2`, `PPF-Phase-3`, `PPF-Phase-4` |
| **Components** | Oluşturuldu: `ppf-api`, `ppf-admin-web`, `integration`, `api-contract`, `docs` |
| **Issue hiyerarşisi** | Epic → Story → (Task / Sub-task) |
| **PPF-1** | “GitHub deposu açılmalıdır” — Platform epic altında bırakın veya Epic yapıp alt story’lere bölün. |
| **Örnek story** | [PPF-2](https://umranoto.atlassian.net/browse/PPF-2) — fix version + component şablonu |
| **Backlog** | Epic’ler **PPF-7** … **PPF-21**; story’ler epic **parent** ile bağlı (team-managed) |

Geçici kurulum task’ları (`PPF-4`, `PPF-5`, `PPF-6`, `[setup]` öneki) yalnızca component kaydı içindir; silinebilir.

### Epic → Jira key (2026-10-15)

| Epic | Key |
|------|-----|
| Platform ve API sözleşmesi | [PPF-7](https://umranoto.atlassian.net/browse/PPF-7) |
| Kimlik ve RBAC | [PPF-8](https://umranoto.atlassian.net/browse/PPF-8) |
| Tanımlar | [PPF-9](https://umranoto.atlassian.net/browse/PPF-9) |
| İş emri | [PPF-10](https://umranoto.atlassian.net/browse/PPF-10) |
| Satış sihirbazı | [PPF-11](https://umranoto.atlassian.net/browse/PPF-11) |
| Umran entegrasyonu | [PPF-12](https://umranoto.atlassian.net/browse/PPF-12) |
| KVKK / tedarikçi görünümü | [PPF-13](https://umranoto.atlassian.net/browse/PPF-13) |
| Ürün ve stok | [PPF-14](https://umranoto.atlassian.net/browse/PPF-14) |
| Fiyatlandırma | [PPF-15](https://umranoto.atlassian.net/browse/PPF-15) |
| Garanti ve sertifika | [PPF-16](https://umranoto.atlassian.net/browse/PPF-16) |
| Finans | [PPF-17](https://umranoto.atlassian.net/browse/PPF-17) |
| Prim ve raporlar | [PPF-18](https://umranoto.atlassian.net/browse/PPF-18) |
| Bildirimler | [PPF-19](https://umranoto.atlassian.net/browse/PPF-19) |
| Pano | [PPF-20](https://umranoto.atlassian.net/browse/PPF-20) |
| Müşteri web | [PPF-21](https://umranoto.atlassian.net/browse/PPF-21) |

**Not:** Atlassian MCP’de component oluşturma API’si yok; component’ler ilk kez bir issue’ya atanırken Jira tarafından otomatik açılır.

---

## 2. Story açıklama şablonu

```markdown
## Bağlam
…

## Doküman
- FR-xxx / NFR-xxx — docs/03-requirements.md

## Kabul kriterleri
- [ ] …
- [ ] API: 02-api-responses.md (RFC 9457)

## Notlar
…
```

---

## 3. Önerilen Epic listesi

| Epic adı | Fix version | FR özeti |
|----------|-------------|----------|
| Platform ve API sözleşmesi | PPF-MVP-1 | NFR-001–003, NFR-007–009 |
| Kimlik ve RBAC | PPF-MVP-1 | 04-rbac |
| Tanımlar | PPF-MVP-1/2 | FR-1101–1105 |
| İş emri | PPF-MVP-1 | FR-201–206, 05-work-order-lifecycle |
| Satış sihirbazı | PPF-MVP-2 | FR-301–305 |
| Umran entegrasyonu | PPF-MVP-2 | FR-1201–1203, 06-umran-integration |
| KVKK / tedarikçi görünümü | PPF-MVP-2 | FR-1220, 04-rbac §4, ADR-PPF-007 |
| Ürün ve stok | PPF-MVP-2 | FR-401–404, FR-501–506 |
| Fiyatlandırma | PPF-MVP-2 | FR-601–605 |
| Garanti ve sertifika | PPF-MVP-2 / Phase-3 | FR-801–804 |
| Finans | PPF-Phase-3 | FR-701–708 |
| Prim ve raporlar | PPF-Phase-3 | FR-901–904 |
| Bildirimler | PPF-Phase-3 | FR-1001–1004 |
| Pano | PPF-MVP-1 | FR-101–104 |
| Müşteri web | PPF-Phase-4 | docs/00-product-plan §4.3 |

---

## 4. MCP / otomasyon

Jira MCP Cursor’a bağlandığında:

1. Fix versions ve components (UI veya MCP tool).
2. Epic’leri oluştur (yukarı tablo).
3. Story’leri Epic Link ile aç; Description’da `FR-xxx` kullan.

CSV import alternatifi: Jira **Import** ile `Summary, Issue Type, Epic Name, Description, Components, Fix Version` sütunları.

---

## 5. İzlenebilirlik

### NFR → Jira (platform / altyapı)

| ID | Kısa başlık | Tipik epic |
|----|-------------|------------|
| NFR-001 | OIDC JWT, `/v1/admin/*` | Kimlik ve RBAC |
| NFR-002 | RFC 9457 / `{ data }` | Platform ve API sözleşmesi |
| NFR-003 | OpenAPI contract-first | Platform ve API sözleşmesi |
| NFR-004 | X-Request-Id, log, PII maskeleme | Platform |
| NFR-005 | IDOR / scope testleri | Kimlik ve RBAC |
| NFR-006 | UI TR / API EN | (çapraz) |
| NFR-007 | Idempotency-Key | Platform / Finans |
| NFR-008 | **PostgreSQL** + migration | Platform (ADR-PPF-004) |
| NFR-009 | `/health`, `/ready` | Platform |

**Not:** [PPF-2](https://umranoto.atlassian.net/browse/PPF-2) health story → **NFR-009** (NFR-004 değil).

### FR / ADR

| Doküman | Jira alanı |
|---------|------------|
| FR-201 | Story description + label `fr-201` (opsiyonel) |
| FR-1220 | Epic “KVKK / tedarikçi görünümü” |
| ADR-PPF-003 | Spike veya Epic “Kimlik” |
| ADR-PPF-004 | Kabul — migration / PostgreSQL story’leri NFR-008 |

---

### Geriye dönük izlenebilirlik (2026-10-16)

| Aralık | İçerik |
|--------|--------|
| PPF-57 … PPF-74 | ADR kabul, GitHub/Origin repo, teslim zinciri, admin Phase-3 rollup |
| PPF-78 … PPF-83 | Component setup, Redocly CI, WO stub (FR-203 kısmi), public verify |
| PPF-75 … PPF-77 | Phase-4+ / Faz 5 epics — **Done** |
| PPF-84 … PPF-126 | Production + operasyon — **Done** |
| PPF-127 … PPF-139, 157 | Faz 6 (contract, sqlc, OIDC staging) — **Done** |
| PPF-140 … PPF-156 | Faz 7 roadmap — **To Do** ([backlog-index](./backlog-index.md)) |

Tam liste: [backlog-index.md](./backlog-index.md).

**Not:** `PPF-3` anahtarı kullanılmıyor; şablon için [PPF-2](https://umranoto.atlassian.net/browse/PPF-2).

---

**Son güncelleme:** 2026-10-16
