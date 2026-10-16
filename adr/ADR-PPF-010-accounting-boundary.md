# ADR-PPF-010: Muhasebe entegrasyon sınırı (FR-705)

- **Durum:** Kabul (Phase-3 MVP)
- **Tarih:** 2026-10-16

## Bağlam

FR-705: muhasebe programı entegrasyonu Umran tarafı bilgisiyle tanımlanır. PPF iş emri ve tahsilat kaynağıdır; resmi fatura/ERP fişi üretimi merkezi Umran veya onaylı muhasebe köprüsüne aittir.

## Karar

1. **PPF sorumluluğu:** Teslim sonrası `invoice_queue` kaydı (`pending_invoice` → `issued` | `failed`), tutar/şube/müşteri özeti, iş emri durumu `invoiced` geçişi.
2. **Umran / ERP sorumluluğu:** Gerçek fatura numarası, e-Fatura/e-Arşiv, muhasebe hesap planı — PPF yalnızca `external_invoice_ref` alanında referans tutar (mock veya webhook ile doldurulur).
3. **Phase-3 MVP:** Admin UI’da kuyruk listesi + manuel “kesildi” (mock ref); canlı ERP adapter **Phase-3.1** ayrı story.

## Sonuçlar

- PPF veritabanında ERP şeması yok; çift kayıt riski azalır.
- İnsan onayı: canlı muhasebe eşlemesi ve hesap kodları iş tarafında onaylanmadan adapter açılmaz.

## Referans

- [03-requirements.md](../03-requirements.md) FR-704–705
- [05-work-order-lifecycle.md](../05-work-order-lifecycle.md)
