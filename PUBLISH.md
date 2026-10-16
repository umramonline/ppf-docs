# docs/ yayını (PPF-98 / PPF-153)

Yerel `docs/` klasörü ürün kaynağıdır.

## GitHub (canonical mirror)

**Repo:** [github.com/umranonline/ppf-docs](https://github.com/umranonline/ppf-docs)

```bash
rsync -a --exclude '.DS_Store' docs/ ../ppf-docs-publish/
cd ../ppf-docs-publish && git add -A && git commit -m "docs sync" && git push origin main
```

GitHub Pages (opsiyonel): `mkdocs` / static export — ileride.

## Confluence

1. Ana sayfa: «PPF Ürün Dokümantasyonu».
2. `03-requirements.md`, `04-rbac.md`, ADR’ler child page olarak import.
3. Matris: [jira/fr-nfr-traceability.md](./jira/fr-nfr-traceability.md) tablosu Confluence table macro.

## Önerilen sahiplik

| İçerik | Sahip |
|--------|--------|
| FR/NFR | Ürün |
| ADR | Mimari |
| Jira matrisi | PMO / delivery |
