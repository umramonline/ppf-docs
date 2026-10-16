# PPF API — Yanıt ve hata formatı

> **RFC 9457** — [Problem Details for HTTP APIs](https://www.rfc-editor.org/rfc/rfc9457.html) (obsoletes RFC 7807). PPF yeşil saha; legacy `{ success: false, message }` hata gövdeleri **kullanılmaz**.

---

## Başarılı yanıtlar (2xx)

```http
HTTP/1.1 200 OK
Content-Type: application/json
X-API-Version: v1
X-Request-ID: 550e8400-e29b-41d4-a716-446655440000
Cache-Control: private, no-store

{
  "data": { }
}
```

- Gövde kök alanı: **`data`** (kaynak veya liste + meta).
- İsteğe bağlı üst seviye `message` eklenebilir; **`success: true` kullanılmaz** (hata ayrımı HTTP status + problem+json ile yapılır).
- Liste endpoint’lerinde `data` içinde veya yanında pagination meta (OpenAPI’de tanımlı).

---

## Hatalı yanıtlar (4xx / 5xx) — RFC 9457

```http
HTTP/1.1 422 Unprocessable Content
Content-Type: application/problem+json
X-API-Version: v1
X-Request-ID: 550e8400-e29b-41d4-a716-446655440000

{
  "type": "https://ppf.umran.example/problems/validation-error",
  "title": "Unprocessable Content",
  "status": 422,
  "detail": "One or more fields are invalid.",
  "instance": "/v1/admin/work-orders",
  "errors": [
    { "field": "scheduled_at", "code": "INVALID_FORMAT", "message": "Geçersiz tarih formatı." }
  ],
  "traceId": "550e8400-e29b-41d4-a716-446655440000"
}
```

### Zorunlu üyeler (9457)

| Alan | Kural |
|------|--------|
| `type` | URI referansı (problem tipi); bilinmeyen için `about:blank` + anlamlı `title`/`detail` |
| `title` | Kısa, insan okunur özet |
| `status` | HTTP status kodu ile **aynı** |
| `detail` | Bu oluşuma özel açıklama (UI’da gösterilebilir — Türkçe olabilir) |
| `instance` | İsteğin path’i (query hariç önerilir) |

### Onaylı extension üyeler (org)

| Alan | Açıklama |
|------|----------|
| `errors` | Validation: `{ field, code, message }[]` |
| `traceId` | `X-Request-ID` ile aynı değer (log korelasyonu) |

Yeni extension alanı → `api-contract` + ADR.

### Yasak

- `Content-Type: application/json` ile 4xx/5xx hata gövdesi
- `{ "success": false, "message": "..." }`
- `status` alanının HTTP kodu ile uyumsuzluğu

---

## OpenAPI (`api-contract`)

- Şema: `ProblemDetails`, `ProblemDetailsValidation` (validation için `errors` extension).
- Tüm error response’lar `application/problem+json` media type.
- Başarı şemaları: `*Response` wrapper with `data` property.

---

## İstemci (ppf-admin-web)

- Codegen error type = `ProblemDetails`.
- TanStack Query / fetch: `response.ok` false iken body’yi problem+json parse et; **`success === false` kontrolü yok**.

---

## Referans

- Org: `/Users/aras/codespace/umran/docs/api-response-format.md`
- umramonline (kademeli): `HasApiStandardResponse` — yeni Customer API hataları aynı modele yakın; legacy route’lar ayrı migrasyon.
