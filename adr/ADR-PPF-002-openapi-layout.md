# ADR-PPF-002: OpenAPI dosya yapısı

- **Durum:** Kabul
- **Tarih:** 2026-10-16

## Karar

1. **Tek dosya:** `openapi/ppf.yaml` (OpenAPI 3.1), kaynak repo **`umramonline/api-contract`**.
2. **Ayrım:** `tags` ile yüzeyler — `Platform`, `Admin`, `Customer`, `Public` (admin/customer route prefix’leri ile uyumlu).
3. **Mirror:** `ppf-api/contracts/openapi/ppf.yaml` implementasyon PR’larında güncellenir; ardından `api-contract` PR + semver tag.
4. **Split yok:** `ppf-admin.yaml` / `ppf-customer.yaml` ayrımı Phase-4’e kadar gerekmez; tag filtre codegen yeterli.

## Referans

- [00-product-plan.md](../00-product-plan.md) §5
- `ppf-api/contracts/README.md`
