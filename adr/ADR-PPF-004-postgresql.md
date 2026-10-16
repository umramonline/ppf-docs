# ADR-PPF-004: Veritabanı — PostgreSQL

- **Durum:** Kabul
- **Tarih:** 2026-10-15
- **Karar verenler:** Ürün / mimari (onaylı)

## Bağlam

PPF operasyon verisi (iş emri, stok, fiyat listeleri, tahsilat kayıtları vb.) `ppf-api` modüler monolith içinde tutulur. Umran çekirdek verisi ayrı sistemdedir; PPF yalnızca kendi PostgreSQL instance’ına yazar.

## Karar

- **Tek production veritabanı:** **PostgreSQL** (14+ veya org standardı sürüm).
- **Migration:** **goose** veya **golang-migrate**; şema değişiklikleri yalnız migration dosyaları ile.
- **Production’da AutoMigrate / otomatik şema sync kullanılmaz.**
- **MySQL / MariaDB** PPF hattı için **kapsam dışı**.

## Sonuçlar

- sqlc veya GORM ile pgx tabanlı erişim (ADR-PPF-001 ile birlikte netleşir).
- `/ready` PostgreSQL bağlantısını doğrular (NFR-009).
- CI ve yerel geliştirme: PostgreSQL container veya paylaşımlı dev instance (`.env.example`).

## Alternatifler (red)

| Alternatif | Red gerekçesi |
|------------|----------------|
| MySQL / MariaDB | Org PPF hattında PostgreSQL kararı; ek operasyonel çeşitlilik istenmiyor |
| Umran DB paylaşımı | Platform kuralı — yasak ([01-architecture.md](../01-architecture.md)) |

## Referans

- [00-product-plan.md](../00-product-plan.md) §2, §4.1
- [03-requirements.md](../03-requirements.md) NFR-008
- [01-architecture.md](../01-architecture.md)
