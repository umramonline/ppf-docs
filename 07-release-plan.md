# PPF — Release planı ve Jira eşlemesi

> Durum: Onaylı taslak · Tarih: 2026-10-15  
> Jira proje: **PPF** (`ppf.umranoto.com`) · Backlog rehberi: [jira/README.md](./jira/README.md)

Fix version isimleri Jira’da aşağıdaki gibi oluşturulmalıdır; doküman ve board **tek kaynak** olarak bu dosyayı kullanır.

---

## 1. Fix versions

| Version | Karşılık gelen plan fazı | Hedef |
|---------|--------------------------|--------|
| `PPF-MVP-1` | Ürün planı Faz 1 | Deploy edilebilir iskelet + ilk CRUD |
| `PPF-MVP-2` | Faz 2 | Umran read, RBAC, stok/fiyat/sihirbaz |
| `PPF-Phase-3` | Faz 3+ (finans/prim/bildirim ağırlıklı) | Paynet, fatura, konsinye, prim, SMS |
| `PPF-Phase-4` | Faz 3 (müşteri web) | `ppf-customer-web`, `/v1/customer/*` |

---

## 2. MVP-1 kapsamı

| Epic (Jira öneri adı) | FR / NFR | Çıktı |
|------------------------|----------|--------|
| Platform ve API sözleşmesi | NFR-001–003, NFR-007–009 | health/ready, auth iskelet, OpenAPI, PostgreSQL migration iskelet, admin shell |
| Kimlik ve RBAC | FR-1104, NFR-005, [04-rbac.md](./04-rbac.md) | Rol middleware, menü guard |
| Tanımlar (read) | FR-1101–1103 | Bayi/bölge/tedarikçi listeleri |
| İş emri (temel) | FR-201–203, [05-work-order-lifecycle.md](./05-work-order-lifecycle.md) | Liste + detay (kısmi bölümler stub) |
| Ürün kataloğu (read) | FR-401–403 | Ürün listesi |
| Pano (temel) | FR-101–103 | KPI iskelet |

**Mevcut issue:** `PPF-1` (GitHub deposu) → Platform epic altında story olarak bırakılabilir veya epic yapılıp altına `ppf-api` / `ppf-admin-web` story’leri eklenir.

---

## 3. MVP-2 kapsamı

| Epic | FR | Çıktı |
|------|-----|--------|
| Satış sihirbazı | FR-301–305 | Wizard UI + API |
| Umran entegrasyon | FR-1201–1203, [06-umran-integration.md](./06-umran-integration.md) | ACL, sync job, entegrasyon ekranı |
| KVKK / tedarikçi görünümü | [04-rbac.md](./04-rbac.md) §4 | Maskeli DTO |
| Stok | FR-501–506 | Bayi stoku, dağılım, otomatik sipariş |
| Fiyatlandırma | FR-601–604 | Listeler, sorgu, segment |
| Garanti (temel) | FR-801–803 | Teslim tetikleyici |
| Satış raporu | FR-904 | Rapor ekranı |

---

## 4. Phase-3 kapsamı

| Epic | FR | Çıktı |
|------|-----|--------|
| Tahsilat (Paynet) | FR-701–703, FR-708 | POS entegrasyonu |
| Faturalama | FR-704–705 | Kuyruk + muhasebe ADR |
| Konsinye cari | FR-706–707 | Blokaj defteri |
| Prim | FR-901–904 | Kurallar + teslimde yazım |
| Bildirimler | FR-1001–1004 | SMS/e-posta + kayıt |
| QR sertifika | FR-802 | Müşteri kanalı ADR ile |

---

## 5. Ürün kararları (ADR — 2026-10-16)

| Konu | ADR | Durum |
|------|-----|--------|
| Go stack (chi, pgx, goose; sqlc bootstrap) | ADR-PPF-001 | Kabul |
| OpenAPI layout | ADR-PPF-002 | Kabul |
| IdP Keycloak + JWT claims | ADR-PPF-003 | Kabul |
| PostgreSQL | ADR-PPF-004 | Kabul |
| Umran MVP mock/live ACL | ADR-PPF-005 | Kabul |
| Konsinye blokaj oranı | ADR-PPF-006 | Kabul |
| Tedarikçi PII maskeleme | ADR-PPF-007 | Kabul |
| Müşteri sertifika kanalı | ADR-PPF-008 | Kabul |
| Production barındırma | ADR-PPF-009 | Kabul |
| Mobil kapsam dışı | ADR-PPF-011 | Kabul |

Operasyon / platform story’leri: [jira/backlog-index.md](./jira/backlog-index.md). Keycloak runbook: [runbooks/keycloak-ppf.md](./runbooks/keycloak-ppf.md).

---

## 6. Jira Components (öneri)

`ppf-api` · `ppf-admin-web` · `integration` · `api-contract` · `docs`

---

## 7. Epic → FR hızlı indeks

| Epic | FR aralığı |
|------|------------|
| Pano | FR-101–104 |
| İş emri | FR-201–206 |
| Sihirbaz | FR-301–305 |
| Ürün | FR-401–404 |
| Stok | FR-501–506 |
| Fiyat | FR-601–605 |
| Finans | FR-701–708 |
| Garanti | FR-801–804 |
| Prim | FR-901–904 |
| Bildirim | FR-1001–1004 |
| Tanımlar | FR-1101–1105 |
| Entegrasyon | FR-1201–1204, FR-1220 |
| Platform (DB, health) | NFR-008, NFR-009 |
| Randevu (ileride) | FR-1301 |

---

**Son güncelleme:** 2026-10-15
