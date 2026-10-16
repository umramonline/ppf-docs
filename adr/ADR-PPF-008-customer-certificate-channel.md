# ADR-PPF-008: Müşteri sertifika kanalı (FR-804)

- **Durum:** Kabul (Phase-3/4 MVP)
- **Tarih:** 2026-10-16

## Karar

1. **Kanal:** `ppf-customer-web` (Next.js) + public doğrulama URL’si; teslim SMS’inde sertifika linki (FR-1001, PPF-113).
2. **Sertifika:** Teslim + garanti kaydı sonrası `warranty_certificate.verify_token`; QR = müşteri portalı `/verify/{token}`.
3. **PDF:** `GET /v1/public/certificates/download?token=` — gofpdf + gömülü Noto Sans (TR), kurumsal şablon + QR (FR-802/804, PPF-112).
4. **OAuth:** Ayrı customer client ADR-PPF-003 ile; dev modda `X-Dev-Role: customer` + `X-Dev-Customer-Umram-Id`.

## Referans

- [03-requirements.md](../03-requirements.md) FR-802, FR-804
