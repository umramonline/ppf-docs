# PPF — Umran entegrasyon matrisi

> Durum: Onaylı taslak · Tarih: 2026-10-15  
> İlgili: [01-architecture.md](./01-architecture.md) · [03-requirements.md](./03-requirements.md) (FR-1201–1204)

Tüm çağrılar `ppf-api` içinde **`internal/integration/umramonline`** (Anti-Corruption Layer) üzerinden yapılır. Org seviyesinde detay: `api-contract/docs/integrations/umram-ppf.md` (ayrı repo).

**Yasak:** Umran (umramonline) veritabanına doğrudan bağlantı.

---

## 1. Modlar

| Mod | Açıklama |
|-----|----------|
| **Senkron** | Zamanlanmış veya admin tetiklemeli toplu çekme; yerel cache/tablolar güncellenir |
| **Anlık** | İş emri sihirbazı veya ekran aksiyonu sırasında tek kayıt |

Kimlik: scope’lu servis token veya client credentials; **tek global API key yok** — [00-product-plan.md](./00-product-plan.md) §5.

---

## 2. Endpoint matrisi (hedef)

Kaynak API: `api.umranoto.com` (UmranOto Web API / Umran Online). Swagger referansı org `api-contract` veya `docs/umran-api/` kopyasında tutulur.

| Panel özelliği | Mod | Umran ucu (özet) | Not |
|----------------|-----|------------------|-----|
| Ekspertiz raporu → araç | Anlık | `GET /api/Rapor/GetByCode` | Yalnız ilgili bayinin raporu |
| Plaka / şase doğrulama | Anlık | `POST /api/Validation/ValidatePlakaSasi` | |
| Hasar / KM / borç sorgusu | Anlık (opsiyonel) | `POST /api/AracHasarKm/Sorgula` | Ücretli olabilir; ayrı onay |
| Bayi master | Senkron | `GET /api/Bayi/GetCrmBayiList`, `GetCrmBayiListByIlId/{id}` | Bölge eşlemesi PPF’de |
| İl / ilçe | Anlık | `GET /api/Il/GetCities`, `GET /api/Ilce/GetCitiesIlce/{cityId}` | |
| Kullanıcı / yetki | Senkron | `GET /api/Kullanici/GetAllKullanici`, `GET /api/Yetki/GetAllYetki`, `POST /api/Yetki/CheckUserPermission` | Parola alınmaz |
| Bayi personeli | Senkron | `GET /api/BayiUser/GetUsersByBranchId/{branchId}` | Prim görev eşlemesi |
| Müşteri bilgisi | Anlık | `GET /api/Customer/GetCustomerInfoById` | TC/vergi no **alınmaz** |
| Marka / model | Senkron | `GET /api/AracMarka/GetAllAracMarka`, `GET /api/AracModel/GetByAracMarkaIdAracModel/{No}` | Segment eşlemesi PPF’de |
| Araç kataloğu (yıl/marka/model) | Anlık | `GET /api/VehicleValuation/GetYears`, `GetBrands`, `GetModels` | |
| Ekspertiz sorgu ödemesi | Ayrı | `POST /api/Odeme/Hazirla` | PPF Paynet tahsilatından **ayrı** |

Durum sütunu admin **Entegrasyon** ekranında gösterilir: bağlı / anahtar bekleniyor / kapalı.

---

## 3. Yerel referans alanları

PPF PostgreSQL’de umram id’ler İngilizce kolon adlarıyla tutulur, örnek:

- `umram_customer_id`
- `umram_vehicle_id`
- `umram_branch_id`
- `umram_user_id`

Master veri sahibi umramonline; PPF yalnızca operasyonel uzantı ve cache.

---

## 4. Webhook (ileride)

- Giriş: `/v1/webhooks/*`, HMAC imza, idempotent handler, retry / DLQ.
- Kullanım: bayi/müşteri değişikliği, merkezi fiyat sinyali (ADR).

---

## 5. Operasyon

- Senkron job: `umran:sync` (CLI veya scheduler); admin UI’dan manuel tetik.
- Outbound: `X-Request-Id` propagate.
- Hata: problem+json; entegrasyon hataları `traceId` ile log.

---

## 6. MVP kapsamı

Hangi endpoint’lerin Faz 2’de zorunlu olduğu **ADR-PPF-005** ile sabitlenir. Önerilen minimum:

1. Bayi listesi (senkron)  
2. Plaka/şase validation (anlık)  
3. Müşteri lookup (anlık)  
4. Kullanıcı/yetki (senkron, RBAC için)

---

**Son güncelleme:** 2026-10-15
