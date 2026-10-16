# ADR-PPF-001: Go HTTP framework ve ORM

- **Durum:** Kabul (bootstrap)
- **Tarih:** 2026-10-15
- **Karar verenler:** (atanacak)

## Bağlam

`ppf-api` modüler monolith olarak geliştirilecek. Ekip tutarlılığı için HTTP router ve veri erişim katmanı seçilmeli.

## Seçenekler

| Alan | Seçenek A | Seçenek B |
|------|-----------|-----------|
| HTTP | chi | echo |
| DB erişim | sqlc + pgx | GORM |

## Karar

- **HTTP:** **chi** (`github.com/go-chi/chi/v5`)
- **DB:** **pgx** pool; sorgular MVP-1’de elle/SQL; **sqlc** ilk domain modülünde devreye alınır
- **Migration:** **goose** (`migrations/`, `cmd/migrate`)
- **GORM / echo:** kapsam dışı (bootstrap sonrası değişiklik yeni ADR ister)

## Sonuçlar

- sqlc: tip güvenli SQL, migration ile uyum; daha fazla SQL yazımı.
- GORM: hızlı CRUD; karmaşık sorgularda dikkat.
- chi vs echo: middleware ekosistemi ve ekip deneyimi belirleyici.

## Referans

- [00-product-plan.md](../00-product-plan.md) §4.1
- [01-architecture.md](../01-architecture.md)
