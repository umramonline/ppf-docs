# NFR-006 — UI TR / API EN tutarlılık denetimi (PPF-97)

> Tarih: 2026-10-16

## Kural

- **Admin UI (`ppf-admin-web`):** Kullanıcıya görünen metinler **Türkçe** (menü, form, hata özeti).
- **API (`ppf-api`):** Problem `title`/`detail`, enum kodları, log alan adları **İngilizce**.
- **OpenAPI:** `summary`/`description` İngilizce; örnek değerler nötr.

## Örnek uyum

| Katman | Örnek |
|--------|--------|
| UI | «İş emri bulunamadı» (kullanıcı mesajı) |
| API | `404` · `title: "Not Found"` · `detail: "work order not found"` |
| DB enum | `payment_pending`, `in_transit` (İngilizce snake) |

## Bilinen istisnalar

- Seed/demo müşteri adları Türkçe (test verisi).
- Jira / doküman karışık TR+EN (ürün ekibi).

## Doğrulama

- `ppf-admin-web`: grep İngilizce nav label — yalnız teknik terimler (Retry, KPI kodları).
- `ppf-api`: handler `Detail` alanları İngilizce; yeni endpoint PR’ında kontrol listesi.
