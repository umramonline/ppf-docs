# ADR-PPF-005: MVP Umran endpoint kapsamı

- **Durum:** Kabul (bootstrap mock)
- **Tarih:** 2026-10-16

## Karar

MVP-2’de `UMRAN_MODE=mock` ile:

1. Plaka/şase doğrulama (`ValidatePlateVin`)
2. Müşteri lookup (`GetCustomerByID`) — TC/vergi yok
3. Ekspertiz raporu (`GetReportByCode`) — sihirbaz sonraki adım

Canlı HTTP client **Phase-2.1**; `api-contract` + scoped token ADR-PPF-003 ile birlikte.

## Referans

- [06-umran-integration.md](../06-umran-integration.md)
- `internal/integration/umramonline/mock_client.go`
