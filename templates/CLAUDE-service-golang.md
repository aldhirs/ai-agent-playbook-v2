# CLAUDE.md — Service `<service-name>`

> Service-level context. Inherit dari `tribes/<id>/CLAUDE.md` dan `org/.claude/CLAUDE.md`. File ini hanya berisi hal yang spesifik service ini.

## What This Service Does

(1-2 paragraf. Fungsi inti, downstream/upstream, dan apa yang BUKAN tanggung jawab service ini.)

## Owners

- **Primary on-call:** <nama> · <slack handle>
- **Secondary on-call:** <nama> · <slack handle>
- **Tech Lead:** <nama>

## Stack & Layout

- **Language:** Go 1.22+ (sebutkan versi minimal)
- **Framework HTTP:** `net/http` + chi (atau yang dipakai)
- **gRPC:** v1.62+
- **DB:** PostgreSQL 15 / MySQL 8 (sebutkan)
- **Queue / event:** Kafka / NATS / Redis Stream (sebutkan)
- **Cache:** Redis 7

```
cmd/
  <service-name>/      # entrypoint (main.go, wire DI di sini)
internal/
  domain/              # entitas + interface repo
  usecase/             # business logic; tidak boleh import handler/repo concrete
  handler/
    http/              # REST handler
    grpc/              # gRPC server
  repository/          # impl repo (db, cache, queue)
  observability/       # logger, metric, tracer setup
pkg/                   # boleh di-import service lain (hindari kecuali perlu)
api/
  openapi.yaml         # REST contract
  proto/               # gRPC contract
migrations/            # SQL up/down (di-sync dari features/<slug>/migrations/ via `make feature-apply`)
test/
  integration/         # testcontainers-based — pattern shared cross-feature
features/              # 1 folder per feature aktif — SUMBER KEBENARAN untuk seluruh output feature
  <slug>/              # PRD · TECH-DESIGN · IMPL-PLAN · TEST-PLAN · POST-LAUNCH-RETRO
                       # contract/ · migrations/ · tests/ · runbook/ · evidence/ · G4-CODE-MANIFEST.md
                       # Detail: lihat playbook Section 5
```

## Local Dev (Wajib 1 Perintah)

```bash
make dev      # boot semua dependency + service di local
make test     # unit
make test-int # integration (testcontainers)
make verify-feature FEATURE=<slug>  # cek evidence + run check spesifik feature
```

Kalau ada deviasi (mis. butuh `.env.local` manual), tulis di sini.

## External Dependencies

| Dependency | Tipe | Pemilik | Fallback / circuit breaker |
|---|---|---|---|
| `auth-service` | gRPC | Tribe 1 | cache JWT 5 menit, deny on miss |
| `notification-service` | Kafka topic `notif.v1` | Tribe 2 | best-effort; tidak block flow utama |
| Stripe / vendor | REST | external | idempotency-key wajib; retry exp backoff |

## Conventions yang Non-standard

(Hanya isi kalau ADA deviasi dari konvensi tribe/org. Kalau kosong = ikut konvensi tribe.)

## Gotchas (Diturunkan dari LESSONS.md)

- **Migration & deploy:** schema change wajib backward-compatible (dual-write 1 release sebelum drop column).
- **Idempotency:** semua endpoint mutating WAJIB terima header `Idempotency-Key`. Tanpa key → 400.
- **Pagination:** cursor-based (`page_token`), bukan offset. Offset banned di service ini karena dataset >10M.
- **Time:** semua time disimpan UTC, response selalu RFC3339 dengan zona. Jangan render local time di backend.
- ...

## Performance Budget

- Endpoint hot path: p99 ≤ <X>ms
- DB query: ≤ <Y>ms p99
- External call: timeout <Z>s, retry max 2 (exp backoff)

## Privacy / Compliance

- PII fields: <list>
- Logging banned-field test: lihat `test/integration/banned_field_test.go` (test service-wide, dijalankan di semua build — bukan per feature)
- Audit log: write ke topic `audit.v1` untuk setiap state change kritikal
