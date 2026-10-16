# PPF — İş gereksinimleri

> Durum: Onaylı taslak · Tarih: 2026-10-15 · İzleme: `FR-xxx`  
> Mimari: [01-architecture.md](./01-architecture.md) · API format: [02-api-responses.md](./02-api-responses.md) · Fazlar: [07-release-plan.md](./07-release-plan.md)

Bu doküman PPF operasyon panelinin **işlevsel gereksinimlerini** tanımlar. Teknik uygulama `ppf-api` / `ppf-admin-web` repolarında; HTTP sözleşmesi org **`api-contract`** repo’sundadır.

---

## 1. Ürün amacı

PPF hattında merkez, bölge, bayi, bayi personeli ve tedarikçilerin aynı sistemde **raf stoku**, **satış (iş emri)**, **uygulama**, **tahsilat**, **faturalama**, **garanti/sertifika**, **prim** ve **raporları** kendi yetki kapsamlarında yönetmesi.

**Sınırlar:** Müşteri/araç master, çekirdek ödeme kuralları ve merkezi fiyat otoritesi umramonline tarafındadır; PPF yalnızca expose edilen API ve (ileride) imzalı event ile tüketir. Bkz. [06-umran-integration.md](./06-umran-integration.md).

---

## 2. Aktörler

| Aktör | Açıklama |
|-------|----------|
| Merkez yöneticisi | Tüm bayiler; merkezi tanımlar; fiyat listeleri; entegrasyon yönetimi |
| Bölge müdürü | Bağlı bayiler; bölgesel raporlar; sınırlı tanım yetkisi |
| Bayi yöneticisi / kullanıcısı | Tek bayi kapsamı; satış, stok, cari |
| Bayi personeli | Satış ve operasyon; dar menü |
| Tedarikçi (uygulama firması) | Atanan iş emirleri; maskeli müşteri verisi |

Detaylı yetki matrisi: [04-rbac.md](./04-rbac.md).

---

## 3. Modüller ve gereksinimler

### 3.1 Pano (dashboard)

| ID | Gereksinim |
|----|------------|
| FR-101 | Kullanıcı rolüne göre KPI özeti: bugün tahsilat, dönem cirosu, açık iş emri sayısı, stok uyarısı |
| FR-102 | Tarih filtresi: bugün, dün, son 7 gün |
| FR-103 | Merkez/bölge: çok bayili özet; bayi: yalnız kendi şubesi |
| FR-104 | Son iş emirleri, bölge cirosu, satış trendi widget’ları (admin UI) |

**Bounded context:** `dashboard`.

---

### 3.2 İş emri (work order)

| ID | Gereksinim |
|----|------------|
| FR-201 | İş emri listesi: numara, durum, bayi, araç (plaka), müşteri, ürün özeti, tutar (KDV dahil) |
| FR-202 | Duruma göre filtre/sekme; sıralama (bayi, durum, tutar) |
| FR-203 | Detay bölümleri: araç/müşteri, kalemler, form cevapları/fiziki durum, tahsilat, süreç, garanti/sertifika, prim katılımcıları, bildirim geçmişi |
| FR-204 | Yaşam döngüsü: bkz. [05-work-order-lifecycle.md](./05-work-order-lifecycle.md) |
| FR-205 | Kalemde uygulama modeli: bayi uygular veya tedarikçi gelir; fiyat listesi dönemi ve bölge/segment bağlamı |
| FR-206 | Yeni satış **çok adımlı sihirbaz** ile açılır (FR-301–305) |

**Bounded context:** `work_order`, `media` (form/foto), `customer`/`vehicle` (umram referans + cache).

---

### 3.3 Satış sihirbazı

| ID | Gereksinim |
|----|------------|
| FR-301 | Ekspertiz raporundan araç verisi çekme (yalnız ilgili bayinin raporu) |
| FR-302 | Plaka / şase doğrulama (Umran Validation API) |
| FR-303 | Umran müşteri numarası ile iletişim bilgisi (TC/vergi no **alınmaz**) |
| FR-304 | Ürün/kalem seçimi; uygulamalı ürünlerde kategori bazlı soru setleri |
| FR-305 | Fiyat: geçerli fiyat listesi + bölge + araç segmenti kuralları |

**Bounded context:** `work_order`, `integration`, `pricing`.

---

### 3.4 Ürün kataloğu

| ID | Gereksinim |
|----|------------|
| FR-401 | Ürün türleri: uygulama gerektiren (bayi veya tedarikçi) ve raf ürünü |
| FR-402 | Kategoriler: PPF, seramik kaplama, cam filmi, hijyen/koruma, bakım, oto kokusu (genişletilebilir) |
| FR-403 | Alanlar: ad, tür, genel fiyat referansı, garanti süresi (ay), bağlı tedarikçi |
| FR-404 | Fiyatlar fiyat listesi kurallarından türetilir; listede doğrudan merkezi fiyat değiştirme **insan onaylı** workflow |

**Bounded context:** `inventory`, `pricing`.

---

### 3.5 Stok

| ID | Gereksinim |
|----|------------|
| FR-501 | Bayi stoku: ürün × bayi miktar; durum: yeterli / alt limitte / tükendi |
| FR-502 | Merkez: tüm bayiler; bayi: yalnız kendi rafı |
| FR-503 | Stok dağılımı: ürün bazında bayi matrisi; görsel uyarı (tükendi / alt limit) |
| FR-504 | Alt limit aşıldığında tedarikçiye **otomatik stok siparişi** (koli/kat bazlı kural) |
| FR-505 | Stok siparişi durumları: tedarikçi onayı bekliyor → hazırlanıyor → kargoda → teslim alındı → iptal |
| FR-506 | Kargo takip numarası kaydı; ileride kargo entegrasyonu (açık karar) |

**Bounded context:** `inventory`, `supplier`.

---

### 3.6 Fiyatlandırma

| ID | Gereksinim |
|----|------------|
| FR-601 | Takvimli fiyat listeleri: yürürlükte / planlı / arşiv |
| FR-602 | Liste başına çoklu fiyat kuralı (ürün, bölge, segment, görev vb.) |
| FR-603 | Fiyat sorgu ekranı/API: anlık teklif hesaplama |
| FR-604 | Araç segmentleri CRUD; marka/model → segment eşlemesi |
| FR-605 | Merkezi fiyat/stok kuralı değişikliği **insan onayı** zorunlu |

**Bounded context:** `pricing`.

---

### 3.7 Finans

| ID | Gereksinim |
|----|------------|
| FR-701 | Sanal POS (Paynet) tahsilat kaydı: referans, durum, taksit, muhasebe kodu, tutar |
| FR-702 | Her tahsilat iş emri **ürün satırlarına** eşlenir |
| FR-703 | Tahsilat durumları: tahsil edildi, onay bekliyor, başarısız, iade |
| FR-704 | Teslim sonrası fatura kuyruğu: faturalanacak → kesildi |
| FR-705 | Muhasebe programı entegrasyonu Umran tarafı bilgisiyle (ADR) |
| FR-706 | Konsinye cari: bayi depozito (blokaj); satışta konsinye bedeli × oran blokajdan düşüm |
| FR-707 | Blokaj oranı parametrik; merkez onayı (açık karar) |
| FR-708 | Umran `Odeme/Hazirla` ekspertiz sorgu ödemesi içindir; PPF tahsilatından **ayrı** tutulur |

**Bounded context:** `billing` (ADR ile umram sınırı).

---

### 3.8 Garanti ve sertifika

| ID | Gereksinim |
|----|------------|
| FR-801 | Uygulamalı ürünler teslim edilince garanti kaydı |
| FR-802 | PPF ürünlerinde müşteriye QR doğrulamalı sertifika |
| FR-803 | Liste: kod, durum (geçerli/süresi doldu), ürün, araç, bayi, bitiş tarihi |
| FR-804 | Müşteri bildirim kanalı ve sertifika şablonu (açık karar — bkz. [07-release-plan.md](./07-release-plan.md)) |

**Bounded context:** `warranty`.

---

### 3.9 Prim

| ID | Gereksinim |
|----|------------|
| FR-901 | Prim alacaklar: satışa dokunan merkez/bölge/bayi personeli; **tedarikçi prim almaz** |
| FR-902 | Kural: görev × ürün kategorisi; kategoriye özel yoksa “tüm kategoriler” kuralı |
| FR-903 | Prim **teslim anında** hesaplanır ve kayda yazılır |
| FR-904 | Prim raporu; satış raporu (bugün / 7 gün / ay / 30 gün) |

**Bounded context:** `commission`.

---

### 3.10 Bildirimler

| ID | Gereksinim |
|----|------------|
| FR-1001 | Müşteri SMS: uygulama başladı, araç hazır (+ sertifika linki/metni) |
| FR-1002 | Tedarikçi grup e-postası (iş atama / durum) |
| FR-1003 | Bildirim kaydı: zaman, kanal, alıcı, metin, ilişkili iş emri, gönderim durumu |
| FR-1004 | KVKK: log ve arayüzde PII maskeleme |

**Bounded context:** `notification` (async queue).

---

### 3.11 Tanımlar

| ID | Gereksinim |
|----|------------|
| FR-1101 | Bayi: ad, aktif, bölge, yetkili, konsinye blokaj özeti, stok/iş özeti |
| FR-1102 | Bölge tanımları; bayi–bölge eşlemesi (Umran bayi sync + yerel bölge) |
| FR-1103 | Tedarikçi: firma, aktif, grup e-postası, ürün ve bekleyen iş sayıları |
| FR-1104 | Kullanıcılar Umran ERP’den senkron; rol, görev, bayi/bölge/firma bağlantısı |
| FR-1105 | Prim kuralları CRUD (merkez) |

**Bounded context:** `branch`, `supplier`, `auth`/`authorization`, `integration`.

---

### 3.12 Entegrasyon ve operasyon

| ID | Gereksinim |
|----|------------|
| FR-1201 | Umran API ACL; senkron ve anlık çağrı matrisi — [06-umran-integration.md](./06-umran-integration.md) |
| FR-1202 | Entegrasyon durum ekranı (admin): endpoint, mod, son senkron |
| FR-1203 | Doğrudan Umran veritabanı bağlantısı **yasak** |
| FR-1204 | Webhook (ileride): HMAC, idempotent handler |
| FR-1220 | Tedarikçi rolünde PII maskeleme (plaka, şase, müşteri, telefon) — [04-rbac.md](./04-rbac.md) §4; ADR-PPF-007 |

**Bounded context:** `integration`.

---

### 3.13 Randevu (ileride)

| ID | Gereksinim |
|----|------------|
| FR-1301 | Randevu planlama ve iş emri ile ilişkilendirme (faz sonrası; MVP dışı) |

**Bounded context:** `appointment`.

---

## 4. Fonksiyonel olmayan gereksinimler

| ID | Gereksinim |
|----|------------|
| NFR-001 | Admin API: OIDC JWT, `/v1/admin/*`, RBAC |
| NFR-002 | 2xx `{ data }`; hatalar RFC 9457 — [02-api-responses.md](./02-api-responses.md) |
| NFR-003 | Contract-first OpenAPI; breaking change semver + `/v2` |
| NFR-004 | `X-Request-Id`, structured log, PII maskeleme |
| NFR-005 | IDOR testleri: bayi/tedarikçi çapraz erişim engeli |
| NFR-006 | UI metinleri Türkçe; API/schema İngilizce |
| NFR-007 | Idempotency-Key: ödeme ve kritik yazma endpoint’leri |
| NFR-008 | Veritabanı: **PostgreSQL**; migration (goose / golang-migrate); production’da AutoMigrate yok — [ADR-PPF-004](./adr/ADR-PPF-004-postgresql.md) |
| NFR-009 | Operasyon uçları: `GET /health` (public), `GET /ready` (PostgreSQL hazır değilse 503) — [01-architecture.md](./01-architecture.md) |

---

## 5. Açık kararlar

Ürün ve mimari kararlar `docs/adr/` altında ADR ile kapatılır. Özet liste: [adr/README.md](./adr/README.md), faz etkisi: [07-release-plan.md](./07-release-plan.md) §5.

**Kabul edildi:** PostgreSQL (ADR-PPF-004).

---

## 6. Jira izlenebilirlik

Epic/story eşlemesi ve proje alanları: [jira/README.md](./jira/README.md). Her story açıklamasında `FR-xxx` referansı kullanılır.

---

**Son güncelleme:** 2026-10-15
