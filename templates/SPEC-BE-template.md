# Spec BE — <Feature Name>

| Field | Value |
|---|---|
| **Status** | DRAFT / LGTM-SPEC-BE / IN-DEV / DONE |
| **Owner (PM)** | <nama> |
| **BE Dev** | <nama> |
| **Tech Lead (consulted)** | <nama> |
| **Tribe** | <id> |
| **Created** | YYYY-MM-DD |
| **LGTM-SPEC-BE at** | (belum — lihat § 12) |

> **Catatan paralelisme BE-FE.** § 4 API Contract di file ini adalah **source of truth** untuk FE. Saat agent selesai isi § 0-4 dan ada `[BE-CONTRACT-FROZEN]` marker di § 4, FE Dev boleh mulai mock + integrate paralel — tidak perlu nunggu BE Dev selesai impl. § 5-11 boleh di-finalize sambil FE Dev jalan.
>
> Companion: `./SPEC-FE.md` · Wireframes: `./wireframes/` · Open questions: `./OPEN-QUESTIONS.md`

---

## 0. Existing Analysis (WAJIB — Prinsip 19)

> AI **tidak boleh proceed** ke § 1 tanpa § 0 lengkap. Cuma "TBD" di sini = blocker hard.

| Audit area | Finding | Reuse opportunity |
|---|---|---|
| Service target | `<service-name>` di `<repo-path>` | — |
| Feature serupa existing | <grep hasil — endpoint/handler/usecase yang mirip> | EXTEND / NEW |
| Utility / helper | mis. `<your-shared-lib>/<pkg>` — sudah ada `<func>()` | YA → reuse |
| Proto / contract existing | <proto file + message yang relevan> | EXTEND field / NEW message |
| Migration recent yang sentuh table | <migration file path + ringkasan> | — |
| ADR / lesson relevan | <link ADR atau LESSONS.md entry> | — |

**Magnitude pick:** ⬜ NO-CHANGE · ⬜ **EXTEND** (default) · ⬜ NEW-MODULE · ⬜ REWRITE

**Justifikasi kalau > EXTEND:** <kenapa EXTEND tidak cukup, komponen existing apa yang masih dipakai, reversibility plan>

---

## 1. Problem & Scope (1 paragraf, no jargon)

(Apa masalahnya, untuk siapa, kenapa sekarang. Max 3 kalimat.)

**In scope (BE):**
- ...

**Out of scope (eksplisit):**
- ...

---

## 2. Acceptance Criteria (Given/When/Then)

```gherkin
Scenario: <happy path>
  Given <kondisi awal>
  When <aksi>
  Then <hasil terobservasi via API>

Scenario: <failure path 1>
  Given ...
  When ...
  Then ... (HTTP code, error message)
```

(Min 1 happy + 2 sad path per AC. Cover juga: idempotency, concurrency, validation boundary.)

---

## 3. Database Changes

> Skip section ini kalau tidak ada DB change. Tulis "N/A — no DB change."

### 3.1 Migration

**`migrations/NNN-<name>.up.sql`:**
```sql
-- ALTER TABLE ... ADD COLUMN ... (nullable, backward-compatible)
-- atau CREATE TABLE / INDEX
```

**`migrations/NNN-<name>.down.sql`:**
```sql
-- Reverse of up.sql (backward-compatible rollback)
```

### 3.2 Affected entities + index

| Entity / Table | Field baru | Index baru | Storage impact |
|---|---|---|---|
| `<table>` | `<col> <type>` | `idx_<table>_<col>` | <estimasi: +X MB/100k rows> |

### 3.3 Backward compatibility checklist

- [ ] Existing client tidak break (field baru nullable atau default)
- [ ] Migration reversible (down.sql tested)
- [ ] Dual-write plan kalau ada rename column (1 release window)
- [ ] No destructive DDL di first rollout

---

## 4. API Contract Changes 🔒 [BE-CONTRACT-FROZEN]

> **WAJIB stable** sebelum LGTM-SPEC-BE. Marker `[BE-CONTRACT-FROZEN]` = FE Dev boleh mulai paralel work mock + integrate.
>
> Setiap change setelah freeze butuh re-LGTM dari FE Dev.

### 4.1 REST endpoints (OpenAPI snippet)

```yaml
# features/<slug>/contract/openapi.yaml (sinkron ke api/ via make feature-apply)
paths:
  /v1/<resource>:
    post:
      summary: <ringkasan>
      operationId: <op>
      parameters:
        - name: Idempotency-Key
          in: header
          required: true
          schema: { type: string, format: uuid }
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required: [field1, field2]
              properties:
                field1: { type: string, minLength: 3, maxLength: 50 }
                field2: { type: integer, minimum: 1, maximum: 100 }
      responses:
        '201':
          description: Created
          content:
            application/json:
              schema: { $ref: '#/components/schemas/<Resource>' }
        '422':
          description: Validation error
          content:
            application/json:
              schema: { $ref: '#/components/schemas/ValidationError' }
```

### 4.2 gRPC (proto snippet) — kalau applicable

```protobuf
// features/<slug>/contract/proto/<name>.proto (sinkron ke <your-proto-contract>/)
service ResourceService {
  rpc CreateResource(CreateResourceRequest) returns (CreateResourceResponse);
}

message CreateResourceRequest {
  string idempotency_key = 1;
  string field1 = 2;
  int32 field2 = 3;
}
```

### 4.3 Validation matrix

> **Dipakai langsung oleh FE Dev untuk inline validation + error mapping.**

| Field | Rule | Error message (untuk user) | HTTP code | FE UI placement |
|---|---|---|---|---|
| field1 | Required, 3-50 char, unique per tenant | "Nama minimal 3 karakter dan harus unik." | 422 | inline below input |
| field2 | Required, integer 1-100 | "Nilai harus 1-100." | 422 | inline below input |
| Idempotency-Key | Required, UUID format | "Header Idempotency-Key wajib." | 400 | toast (silent retry) |

### 4.4 Backward compatibility

- ⬜ Endpoint baru (no risk)
- ⬜ Endpoint existing diubah → migration path: `<plan>`
- ⬜ Field deprecated → window: 1 release dual-support

---

## 5. Logic Changes per Layer

### 5.1 Handler (`internal/handler/<name>.go`)

```go
// Pseudo-code atau diff snippet
func (h *Handler) CreateResource(c *gin.Context) {
    // Idempotency check
    // Validation (pakai <your-shared-lib>/validator)
    // Call usecase
    // Response wrap dengan responsePackage
}
```

### 5.2 Usecase (`internal/usecase/<name>.go`)

- Business logic: <ringkasan>
- Side effects: <DB write, event publish, external call>
- Idempotency strategy: <pakai key di header → check cache/DB before process>

### 5.3 Repository (`internal/repository/<name>.go`)

- Query baru / extend: <SQL atau ORM call>
- Transaction boundary: <kalau ada multi-table write>

### 5.4 Domain entity (kalau ada perubahan)

- <Entity> tambah field <field> (nullable)
- Validation di entity level (kalau bukan di handler)

---

## 6. Observability Plan

| Signal | Name / Format | Trigger | Sink |
|---|---|---|---|
| Log event | `resource.created` (JSON structured) | Setelah commit DB | Loki / structured log aggregator |
| Metric | `<tribe>_<service>_resource_created_total{result=success\|failed}` (counter) | Per request | Prometheus |
| Metric | `<tribe>_<service>_resource_create_duration_seconds` (histogram) | Per request | Prometheus |
| Trace span | `<service>.CreateResource` | Per request | Jaeger / OTEL collector |
| Alert | Error rate > 5% selama 5 menit | Threshold trigger | PagerDuty / Slack #oncall |

---

## 7. Test Approach

### 7.1 Unit test (min coverage 80% di usecase layer)

- Happy: <case>
- Sad 1: validation fail (cover validation matrix § 4.3)
- Sad 2: downstream fail (DB / external)
- Edge: idempotency replay (same key 2x → return cached result)

### 7.2 Integration test

- Endpoint smoke (curl-style): happy + 1 sad
- Cross-tenant access (IDOR): user A request resource user B → 403
- Banned-field assertion (kalau ada PII): response tidak include `<list field internal>`

### 7.3 Evidence path (Phase 2 output)

`features/<slug>/evidence/be/{curl.txt, test.log}`

---

## 8. Security & Privacy

- **PII fields tersentuh:** <list> · Atau: "N/A — no PII"
- **Auth requirement:** <JWT / mTLS / API key / public>
- **Banned-field test status:** ⬜ added / ⬜ N/A
- **Threat model singkat (STRIDE):** <link ke section atau inline 6 row>
- **Compliance:** <regulasi yang berlaku, atau "N/A">

---

## 9. Rollout Plan

- **Feature flag:** `<flag-name>` (default OFF)
- **Canary:** 1% → 10% → 100% (interval per tribe convention)
- **Rollback trigger:** error rate > X%, latency p95 > Yms
- **Rollback action:** flag OFF (no destructive DDL di first rollout → instant rollback)

---

## 10. Risks

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| ... | L/M/H | L/M/H | ... |

---

## 11. Open Questions

> Diisi kalau ada ambiguity yang block downstream. Kalau kosong → ✅ siap LGTM.
>
> **Push-back ke `OPEN-QUESTIONS.md` (root feature folder)** kalau pertanyaan butuh jawab user sebelum agent bisa proceed.

- [ ] OPEN — Q1: ...
- [x] RESOLVED — Q2: ... → Jawaban: ...

---

## 12. Approval — LGTM-SPEC-BE

> **Grep-able.** Approval di Slack/DM TIDAK terhitung.

| Role | Nama | LGTM-SPEC-BE | Tanggal |
|---|---|---|---|
| **BE Dev** | | | |
| **Tech Lead** | | | |
| PM (consulted, optional sign) | | | |

**Approval format yang valid:** `LGTM-SPEC-BE by <nama> on YYYY-MM-DD` — edit langsung di tabel, commit ke repo.

---

## Appendix — Quick reference untuk FE Dev (paralel work)

Kalau Anda FE Dev mau mulai paralel:

1. Baca **§ 4 API Contract** (sudah frozen) — pakai sebagai mock baseline
2. Pakai validation matrix § 4.3 untuk inline validation FE
3. Refer ke **`./SPEC-FE.md`** untuk UI vision + wireframe
4. Kalau ada pertanyaan ke BE Dev, tulis di **`./OPEN-QUESTIONS.md`** (jangan langsung DM — supaya tercatat)
