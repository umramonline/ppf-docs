# ADR-PPF-007: Tedarikçi PII maskeleme (FR-1220)

- **Durum:** Kabul (MVP-2 uygulama, Phase-3.1 dokümantasyon)
- **Tarih:** 2026-10-16

## Karar

`supplier_user` rolünde iş emri listesi ve detayında:

| Alan | Kural |
|------|--------|
| Plaka | İlk 2 + `***` + son 2 karakter |
| Müşteri adı | İlk harf + `*` |
| `customer_umram_id` | Boş |
| Ödeme / fatura / prim bölümleri | `restricted` (detay yok) |
| `vehicle_customer` bölümü | Maskeleme notu |

Uygulama: `internal/workorder/mask.go` + detay section scrub.

## Referans

- [04-rbac.md](../04-rbac.md) §4
