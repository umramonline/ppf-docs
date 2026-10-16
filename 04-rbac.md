# PPF — RBAC ve navigasyon

> Durum: Onaylı taslak · Tarih: 2026-10-15  
> İlgili: [03-requirements.md](./03-requirements.md) · [01-architecture.md](./01-architecture.md)

Kimlik: OIDC (PKCE) admin SPA → Bearer JWT → `ppf-api` resource server. Rol ve görev claim’leri **ADR-PPF-003** ile netleştirilir; kullanıcı master Umran ERP senkronu ile uyumlu olmalıdır.

---

## 1. Rol tanımları

| Rol kodu | Türkçe UI | Veri kapsamı |
|----------|-----------|--------------|
| `hq_admin` | Merkez yöneticisi | Tüm bayiler ve merkezi tanımlar |
| `region_manager` | Bölge müdürü | Atanmış bölgedeki bayiler |
| `branch_admin` | Bayi yöneticisi | Tek `branch_id` |
| `branch_staff` | Bayi personeli | Tek `branch_id`; yazma yetkisi görev bazlı |
| `supplier_user` | Tedarikçi kullanıcısı | Atanan iş emirleri ve ilgili stok siparişleri |

**Tedarikçi** organizasyonu `supplier_id` ile bağlanır; bayi kullanıcıları `branch_id` ile.

---

## 2. Menü modülleri

Navigasyon grupları (admin UI):

| Grup | Modül |
|------|--------|
| Satış | Pano, İş emirleri, Garanti ve sertifika |
| Stok ve tedarik | Ürünler, Bayi stoku, Stok dağılımı, Stok siparişleri |
| Finans | Tahsilatlar, Faturalama, Konsinye cari |
| Fiyatlandırma | Fiyat listeleri, Fiyat sorgula, Araç segmentleri, Marka/model |
| Raporlar | Satış raporu, Prim raporu, Bildirim kaydı |
| Tanımlar | Bayiler, Bölgeler, Tedarikçiler, Kullanıcılar, Prim kuralları |
| Sistem | Entegrasyon (merkez) |

---

## 3. Rol × modül matrisi

**Yetki:** `—` yok · `R` okuma · `W` oluştur/güncelle · `A` merkez yönetimi

| Modül | hq_admin | region_manager | branch_admin | branch_staff | supplier_user |
|-------|:--------:|:--------------:|:------------:|:------------:|:-------------:|
| Pano | R | R | R | R | R |
| İş emirleri | R/W | R/W | R/W | R/W* | R* |
| Garanti/sertifika | R/W | R/W | R/W | R/W | R |
| Ürünler | R/W | R | R | R | — |
| Bayi stoku | R/W | R | R | R | — |
| Stok dağılımı | R | R | — | — | — |
| Stok siparişleri | R/W | R/W | R/W | R | R/W* |
| Tahsilatlar | R/W | R/W | R/W | R | — |
| Faturalama | R/W | R/W | R/W | R | — |
| Konsinye cari | R/W | R/W | R/W | — | — |
| Fiyat listeleri | R/W | R | — | — | — |
| Fiyat sorgula | R | R | R | R | — |
| Segment / marka-model | R/W | R | — | — | — |
| Satış raporu | R | R | R | — | — |
| Prim raporu | R | R | R | R | — |
| Bildirim kaydı | R | R | R | R | — |
| Bayiler | R/W | R | — | — | — |
| Bölgeler | R/W | — | — | — | — |
| Tedarikçiler | R/W | — | — | — | — |
| Kullanıcılar | R/W | R/W** | R/W** | — | — |
| Prim kuralları | R/W | R/W | R/W | — | — |
| Entegrasyon | R/W | — | — | — | — |

\* `branch_staff`: iş emri oluşturma/satış görevi varsa; tedarikçi yalnız atanan kayıtlar.  
\*\* Kullanıcı yönetimi kapsamı kendi bayi/bölgesi ile sınırlı (ADR).

---

## 4. Tedarikçi ve KVKK (alan bazlı)

Tedarikçi rolünde iş emri detayında:

| Alan | hq / bayi | supplier_user |
|------|-----------|---------------|
| Araç marka/model, yıl, renk, segment | Tam | Tam |
| Plaka | Tam | Kısmi maske |
| Şase (VIN) | Tam | Kısmi maske |
| Müşteri adı | Tam | Maske |
| Telefon / e-posta | Tam | Gizli veya maske |
| Fiyat / prim iç detay | Politikaya göre | Kısıtlı (yalnız tedarikçi gelir satırı) |

Tam kural seti **insan onayı** ile ADR olarak sabitlenir (**FR-1220**, ADR-PPF-007; NFR-005 IDOR testleri).

---

## 5. API uygulaması

- Middleware: JWT doğrulama → scope → RBAC policy → kaynak sahipliği (`branch_id`, `supplier_id`, `region_id`).
- Liste endpoint’leri: zorunlu scope filtresi (query override ile genişletme **yok**).
- Admin route’ları `/v1/admin/*`; customer yüzeyi ayrı middleware seti — [01-architecture.md](./01-architecture.md).

---

## 6. Test beklentisi

- Her rol için en az bir entegrasyon testi: yasak modül → 403.
- Cross-branch iş emri ID → 403.
- Tedarikçi: başka tedarikçinin iş emri → 403.
- Maskeleme: supplier DTO snapshot testleri.

---

**Son güncelleme:** 2026-10-15
