# Tech Design — <Feature Name>

| Field | Value |
|---|---|
| **Status** | DRAFT / IN-REVIEW / **SIGNED** |
| **Owner (Tech Lead)** | <nama> |
| **Cross-tribe Reviewer** | <nama, tribe X> |
| **PRD link** | `features/<slug>/G1-PRD.md` |
| **Signed at Gate 2** | YYYY-MM-DD |
| **Change Magnitude** | NO-CHANGE / **EXTEND** (default) / NEW-MODULE / REWRITE |

---

## 0. Existing Analysis (WAJIB — diisi SEBELUM Section 3 Options)

> **Prinsip 19:** Analyze Existing First, Propose Minimal Change.
> Section ini WAJIB diisi sebelum brainstorm opsi. Tanpa ini, design ditolak di review.

### 0.1 Codebase Audit

| Area | Path / Reference | Status di Codebase |
|---|---|---|
| Feature serupa yang sudah ada | (mis. `service-X/internal/usecase/<name>`) | sudah ada / belum ada / partial |
| Utility / helper yang bisa di-reuse | (mis. `shared/<pkg>`) | sudah ada / belum ada |
| Pattern serupa di tribe ini / tribe lain | (mis. ADR-NNN di tribe Y) | ada / tidak |
| Database table / schema yang relevan | (mis. `tabel_X`) | sudah ada (schema attach) / belum |
| API endpoint serupa | (mis. `POST /v1/<resource>`) | sudah ada / belum |
| FE komponen / page existing (kalau ada UI change) | → lihat `G1-UI-IMPACT.md` Section 1.1 | — |

### 0.2 Change Magnitude — Pilih SATU

- [ ] **NO-CHANGE** — solusi sudah ada; cuma butuh dokumentasi / onboarding / config flag flip
- [x] **EXTEND** — tambah parameter / field / branch ke kode existing. **Default. Prefer ini.**
- [ ] **NEW-MODULE** — modul baru, tapi reuse infrastructure / utility / pattern existing
- [ ] **REWRITE** — perlu redesign signifikan, harus ADR justify + alternative comparison

### 0.3 Justification (kalau magnitude > EXTEND)

> Wajib kalau pilih NEW-MODULE atau REWRITE. Skip kalau NO-CHANGE / EXTEND.

- **Kenapa tidak cukup EXTEND?** (jelaskan kendala konkret: limitasi pattern existing, regression risk, dll)
- **Komponen existing apa saja yang dipakai ulang?** (jangan REWRITE 100% — masih ada bagian yang reuse)
- **Reversibility:** kalau magnitude besar, bagaimana cara rollback?

### 0.4 Reuse Plan

> List file/utility/komponen existing yang akan dipakai (bukan dibikin baru). Path konkret.

- (mis. `shared/response/Response` — pakai untuk wrapping output)
- (mis. `service-X/internal/repository/<base>` — extend dengan method baru)

---

## 1. Context

(Ringkasan 1-2 paragraf. Link ke PRD. State of the world sekarang.)

## 2. Goals & Non-Goals

- **Goals:**
  - ...
- **Non-goals:**
  - ...

## 3. Options Considered (Minimal 3)

> Disesuaikan dengan magnitude di Section 0.2:
> - **Magnitude EXTEND:** options fokus ke variasi cara extend (mis. add field di table A vs B, sync vs async). SKIP option yang loncat ke "rewrite arsitektur".
> - **Magnitude NEW-MODULE / REWRITE:** options boleh variatif. Tapi **WAJIB ada 1 option "minimal extend"** sebagai control.

### Option A — <judul>

- **Cara kerja:** ...
- **Pros:** ...
- **Cons:** ...
- **Effort:** S/M/L
- **Risk:** L/M/H

### Option B — <judul>

(idem)

### Option C — <judul>

(idem)

### Trade-off Matrix

| Aspek | A | B | C |
|---|---|---|---|
| Latency | | | |
| Effort | | | |
| Operational complexity | | | |
| Risk regression | | | |
| Reversibility | | | |

## 4. Decision (= ADR)

**Pilih: <Option X>**

**Rationale:** (kenapa dipilih, dengan referensi explicit ke trade-off matrix)

**Konsekuensi:** (apa yang kita terima sebagai cost-of-decision)

## 5. API Contract

### REST (kalau ada)

```yaml
# features/<slug>/contract/openapi.yaml (canonical), di-sync ke api/openapi.yaml via `make feature-apply`
paths:
  /v1/<resource>:
    post:
      summary: ...
      requestBody:
        content:
          application/json:
            schema: ...
      responses:
        '201': ...
        '422': ...   # validation error, friendly message
```

### gRPC (kalau ada)

```proto
// features/<slug>/contract/proto/<service>.proto (canonical), di-sync ke api/proto/ via `make feature-apply`
service Payment {
  rpc CreateCharge(CreateChargeRequest) returns (Charge) {}
}
```

## 6. Data Model

(ERD atau struct definition. Sertakan migration plan kalau ada DDL.)

```sql
-- features/<slug>/migrations/001-<name>.up.sql (canonical), di-sync ke migrations/ via `make feature-apply`
CREATE TABLE ...
```

## 7. Sequence Diagram

```mermaid
sequenceDiagram
  participant FE as Frontend
  participant API as Service A (REST)
  participant SVC as Service B (gRPC)
  participant DB
  FE->>API: POST /v1/charge {idempotency-key}
  API->>SVC: rpc CreateCharge()
  SVC->>DB: INSERT charge
  DB-->>SVC: ok
  SVC-->>API: Charge
  API-->>FE: 201 Created
```

## 8. Failure Modes & Recovery

| Mode | Probability | Detection | Recovery |
|---|---|---|---|
| DB connection drop | M | health-check + alert | retry exp backoff; circuit breaker |
| Downstream timeout | M | metric + trace | fail-fast 504; client retry idempotent |
| Partial write | L | reconciliation job | dual-write + outbox pattern |

## 9. Observability Plan

- **Logs:** event names: `<service>.charge.created`, `<service>.charge.failed`, dst.
- **Metrics:** `<service>_charge_total{result=success|failed}`, `<service>_charge_latency_ms`
- **Traces:** propagate `X-Trace-Id`. Span name: `<service>.<operation>`.
- **Dashboard:** <link Grafana>
- **Alerts:** SLO `<name>` (link runbook)

## 10. Rollout Plan

- [ ] Behind feature flag: `<flag_name>` (default off)
- [ ] Canary: 1% traffic for 24h, then 10%, then 100%
- [ ] Rollback plan: toggle flag off; data backward-compatible (no destructive migration)

## 11. Security & Privacy Notes

- Field PII yang masuk: <list>
- Backend strip + frontend hide (defense-in-depth)
- Banned-field test added: yes / no

## 12. Open Items (sebelum Gate 2)

- [ ] ...

## 13. Approval Gate 2

| Role | Nama | LGTM-TD | Tanggal |
|---|---|---|---|
| Tech Lead | | | |
| Cross-tribe reviewer | | | |
