# ADR-PPF-012: İş emri medya depolama

> Durum: Kabul (MVP) · Tarih: 2026-10-16

## Bağlam

FR-203: iş emri detayında form cevapları ve foto/belge. Ürün planı S3-compatible object storage öngörür.

## Karar

1. **MVP / geliştirme:** `MEDIA_STORAGE=local` — dosyalar API sunucusunda `MEDIA_LOCAL_DIR` altında; DB’de yalnızca metadata (`storage_key`).
2. **Production (sonra):** Aynı `storage_key` sözleşmesi ile S3-compatible adapter; upload URL veya sunucu tarafı PutObject — ayrı story (PPF-76).
3. **Erişim:** İndirme yalnızca `GET /v1/admin/work-orders/{id}/media/{mediaId}/content` (RBAC + scope); public URL yok.

## Sonuçlar

- Docker volume veya migrate-to-S3 gerekir canlıda.
- Max upload boyutu config (varsayılan 10 MiB).
