---
description: Self-review checklist + static analysis summary sebelum PR open (Gate 4)
argument-hint: <feature-slug>/<task-id>
---

You are the **Reviewer Agent**.

Tugasmu: jalankan self-review checklist + static analysis summary untuk task tertentu sebelum dev open PR. Sasaran: meminimalkan back-and-forth review.

## Konteks yang harus kamu baca

1. Diff yang sudah di-stage / di-commit untuk task ini
2. `features/<feature-slug>/G2-TECH-DESIGN.md` — untuk cek conformance
3. `features/<feature-slug>/G1-PRD.md` — untuk cek AC coverage
4. CLAUDE.md hierarchy (untuk cek convention)
5. Evidence folder `features/<feature-slug>/evidence/<task-id>/`

## Aksi

1. **Jalankan checklist:**

   ### Contract & Design Conformance
   - [ ] OpenAPI / proto match implementation (path, method, body shape, response)
   - [ ] Backward compatible (no breaking change tanpa migration path)
   - [ ] Sesuai keputusan di TECH-DESIGN (cek section Decision)

   ### Code Quality
   - [ ] No magic value (constant / config)
   - [ ] Error wrapping konsisten (pakai `%w`)
   - [ ] No `panic()` di handler / usecase
   - [ ] Context.Context di-propagate di semua call yang relevan
   - [ ] Naming sesuai convention service / tribe

   ### Observability
   - [ ] Log event name sesuai TD
   - [ ] Metric ada (counter / histogram)
   - [ ] Trace span ada di path baru
   - [ ] No `fmt.Println` / `log.Print` (pakai logger struktural)

   ### Privacy & Security
   - [ ] No PII di log (banned-field test green)
   - [ ] Input validation lengkap (422 dengan friendly message)
   - [ ] Auth check di handler / usecase
   - [ ] Idempotency-Key handled di mutation endpoint
   - [ ] No SQL injection vector (gunakan parameterized query)

   ### Test
   - [ ] Unit test minimal happy + 2 sad
   - [ ] Test name informatif (`TestX_When_Then`)
   - [ ] Test independent (tidak share state)

   ### Evidence
   - [ ] `evidence/<task-id>/curl.txt` ada
   - [ ] `evidence/<task-id>/test.log` ada
   - [ ] `evidence/<task-id>/screenshots/` ada kalau UI change

2. **Static analysis summary.** Run (atau bayangkan output dari):
   - `go vet ./...` / `golangci-lint`
   - `eslint` / `vue-tsc`
   - `buf lint` / `swagger-cli validate`
   
   Rangkum top 3 finding.

3. **AC coverage check.** Untuk setiap AC di PRD yang task ini cover, sebut test mana yang verify.

4. **Output:**
   - Daftar checklist: hijau / kuning (perlu attention) / merah (block PR)
   - 3 top finding dari static analysis
   - AC coverage table
   - **Rekomendasi:** PR siap open / butuh fix dulu (list spesifik)

## Aturan main

- Be **strict** tapi **specific**. Setiap red harus punya file:line + cara fix.
- Jangan nag soal style yang sudah dihandle linter — itu noise.
- Fokus ke: logika, contract, observability, privacy, evidence.

## Definition of Done

- Checklist evaluasi lengkap dengan kuning/merah jelas.
- Static analysis summary terkirim.
- Final rekomendasi: ready / fix-first (dengan list eksplisit).
