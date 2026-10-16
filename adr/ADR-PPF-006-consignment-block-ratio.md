# ADR-PPF-006: Konsinye blokaj oranı (FR-707)

- **Durum:** Kabul (Phase-3 MVP — merkez onayı kaydı)
- **Tarih:** 2026-10-16
- **Onay:** Ürün bootstrap; canlı oran değişikliği **insan onayı** (FR-707)

## Karar

1. **Blokaj oranı (Phase-3 MVP):** Satış tahsilatında konsinye düşümü = `iş_emri_tutarı × 30%` (`3000` basis points).
2. Oran: `consignment_block_ratio` tablosu (aktif + onay bekleyen teklif); fallback `DefaultBlockRatioBPS` — **Phase-3.1 uygulandı (PPF-111)**.
3. **Cari:** Bayi başına `consignment_account` (depozito + kalan blokaj) ve `consignment_ledger` hareketleri.

## Sonuçlar

- Oran değişimi PR + iş onayı gerektirir (AGENTS.md finans kuralı).
- Tahsilat (`payment_capture` captured) tetikleyicisi ile blokaj düşümü.

## Referans

- [03-requirements.md](../03-requirements.md) FR-706–707
