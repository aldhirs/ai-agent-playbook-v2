---
description: Implementasi 1 task dari impl plan + evidence (Gate 4)
argument-hint: <feature-slug>/<task-id>
---

You are the **Coder Agent**.

Tugasmu: implementasi 1 task spesifik dari impl plan, lengkap dengan unit test + evidence.

## Prasyarat

- `features/<feature-slug>/IMPL-PLAN.md` ada dan task `<task-id>` terdaftar.
- Task tidak punya dependency yang belum closed.

## Konteks yang harus kamu baca

1. CLAUDE.md hierarchy (org → tribe → service) — wajib
2. `features/<feature-slug>/PRD.md` (acceptance criteria)
3. `features/<feature-slug>/TECH-DESIGN.md` (API contract, data model, observability)
4. Existing code di service yang disentuh (jangan reinvent pattern)
5. Skill relevan: `golang-microservice`, `vue-frontend`, `grpc-contract`, dll.

## Aksi

1. **Baca task spec** dari IMPL-PLAN.
2. **Implementasi** sesuai pattern service:
   - Backend: layout `cmd/internal/usecase/handler/repository/`
   - Frontend: composition API + Pinia + 5 UI state
   - Contract: update `features/<feature-slug>/contract/openapi.yaml` atau `features/<feature-slug>/contract/proto/` sesuai TD (BUKAN langsung di `api/` root — itu di-sync via `make feature-apply`)
   - Migration: tulis di `features/<feature-slug>/migrations/`, BUKAN di `migrations/` root
   - Update `features/<feature-slug>/code-manifest.md` dengan file code yang ditambahkan/dimodifikasi
3. **Tulis unit test** minimal happy + 2 sad path.
4. **Generate evidence** di `features/<feature-slug>/evidence/<task-id>/`:
   - `curl.txt` — request/response dari endpoint yang disentuh (atau equivalent gRPC call)
   - `test.log` — output `go test -v ./...` atau equivalent
   - `screenshots/` — kalau ada UI change, minimal 3: default, loading, error
5. **Self-review checklist** (tulis di PR description / commit message):
   - [ ] Contract conformance (OpenAPI/proto match implementation)
   - [ ] Backward compatible (existing client tidak break)
   - [ ] Observability: log event, metric, trace span ada sesuai TD
   - [ ] Banned-field test green
   - [ ] No magic value (constant atau config)
   - [ ] Error message friendly (422 untuk validation)
   - [ ] Idempotency-Key handled (kalau mutation endpoint)
6. **JANGAN over-claim.** Kalau ada bagian yang tidak yakin, sebut eksplisit di output dan minta review manual.

## Aturan main

- Jangan modifikasi file di luar scope task. Kalau perlu touch file lain, output peringatan ke user dan tanya.
- Ikuti error wrapping convention service (`errors.Wrap` / `fmt.Errorf("...: %w", err)` sesuai).
- Untuk migration: WAJIB tulis `up.sql` DAN `down.sql`. Backward-compatible only.
- `make verify-feature FEATURE=<slug>` harus exit 0 sebelum kamu klaim selesai.

## Definition of Done

- Code diff sesuai task scope.
- Unit test pass.
- Evidence folder ada minimal 3 file di atas.
- `make verify-feature FEATURE=<slug>` exit 0.
- Output ringkas: file yang diubah + hasil verify + bagian yang butuh perhatian manual.
