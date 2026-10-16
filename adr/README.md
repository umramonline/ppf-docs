# PPF — Architecture Decision Records (ADR)

Kararlar kısa, kalıcı ve geri alınabilir olmalıdır. Şablon aşağıdadır; her ADR tek dosya: `ADR-PPF-NNN-kisa-baslik.md`.

---

## Durum

| ADR | Başlık | Durum |
|-----|--------|--------|
| [ADR-PPF-001](./ADR-PPF-001-go-stack.md) | chi + pgx + goose (sqlc sonra) | **Kabul** (bootstrap) |
| [ADR-PPF-002](./ADR-PPF-002-openapi-layout.md) | OpenAPI tek dosya + tag ayrımı | **Kabul** |
| [ADR-PPF-003](./ADR-PPF-003-identity.md) | IdP / JWT claim (bootstrap) | **Kabul** |
| [ADR-PPF-004](./ADR-PPF-004-postgresql.md) | PostgreSQL (tek DB) | **Kabul** |
| [ADR-PPF-005](./ADR-PPF-005-umran-mvp.md) | MVP Umran mock ACL | **Kabul** (MVP-2) |
| [ADR-PPF-006](./ADR-PPF-006-consignment-block-ratio.md) | Konsinye blokaj oranı (%30 MVP) | **Kabul** (Phase-3) |
| [ADR-PPF-007](./ADR-PPF-007-supplier-pii-masking.md) | Tedarikçi PII maskeleme (FR-1220) | **Kabul** |
| [ADR-PPF-008](./ADR-PPF-008-customer-certificate-channel.md) | Müşteri sertifika kanalı (FR-804) | **Kabul** (Phase-3) |
| [ADR-PPF-009](./ADR-PPF-009-production-hosting.md) | Production barındırma | **Kabul** |
| [ADR-PPF-011](./ADR-PPF-011-mobile-out-of-scope.md) | Mobil (ppf-mobile) kapsam dışı | **Kabul** |
| [ADR-PPF-010](./ADR-PPF-010-accounting-boundary.md) | Muhasebe entegrasyon sınırı (FR-705) | **Kabul** (Phase-3) |

Liste [00-product-plan.md](../00-product-plan.md) §12 ve [07-release-plan.md](../07-release-plan.md) §5 ile senkron tutulur.

---

## Şablon

```markdown
# ADR-PPF-NNN: Başlık

- **Durum:** Taslak | Kabul | Red | Kullanımdan kalktı
- **Tarih:** YYYY-MM-DD
- **Karar verenler:** …

## Bağlam
…

## Karar
…

## Sonuçlar
Olumlu / olumsuz …

## Alternatifler
…
```

---

## Kurallar

- Breaking API veya contract değişikliği ilgili ADR veya PR açıklamasında belirtilir.
- **İnsan onayı** gerektiren konular (fiyat, ödeme, RBAC, migration, KVKK): ADR + iş onayı kaydı.
