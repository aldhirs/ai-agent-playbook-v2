# Test Plan — <Feature Name>

| Field | Value |
|---|---|
| **Status** | DRAFT / IN-REVIEW / **GREEN** |
| **Owner (QA)** | <nama> |
| **PRD** | `features/<slug>/PRD.md` |
| **Tech Design** | `features/<slug>/TECH-DESIGN.md` |
| **Disiapkan paralel dengan dev?** | Ya / Tidak (target: YA) |

---

## 1. Scope

Mapping eksplisit ke acceptance criteria di PRD. Setiap AC ≥1 test case.

| PRD AC ID | Test case ID(s) |
|---|---|
| Story 1 / AC 1 | TC-001, TC-002 |
| Story 1 / AC 2 | TC-003 |
| ... | ... |

## 2. Test Matrix (State × Action)

| State \ Action | Action A | Action B | Action C |
|---|---|---|---|
| Default | TC-001 | TC-004 | TC-007 |
| Loading | n/a | TC-005 | TC-008 |
| Empty | TC-002 | n/a | TC-009 |
| Error (server) | TC-003 | TC-006 | TC-010 |
| Success | TC-001 | TC-004 | TC-007 |

*5+ state coverage wajib (default/loading/empty/error/success).*

## 3. Edge Cases

(Tester Agent menghasilkan list awal. QA tambahkan domain-specific.)

- Boundary: input minimum/maximum
- Format: locale, timezone, encoding
- Concurrency: 2 request bersamaan (idempotency)
- Network: slow 3G, intermittent, offline
- Auth: token expired, scope tidak cukup
- Permission: cross-tenant access blocked
- Data: NULL, empty string, very long, special chars (PII!)

## 4. Automation Strategy

| Tipe | Tool | Coverage target |
|---|---|---|
| Unit (Go) | `go test` + table-driven | ≥ 80% line coverage di paket `usecase/` |
| Integration (Go) | testcontainers + chi router test | semua endpoint, semua failure mode |
| Unit (Vue) | Vitest | komponen kritikal (form, state machine) |
| E2E | Playwright | happy path + 3 sad path utama |
| Contract | OpenAPI validator + buf | tiap PR yang sentuh api/ |

## 5. Manual Test (yang TIDAK diautomasi)

- Visual regression / Pixel-perfect UI (kalau belum ada visual test)
- Compliance scenario (auditor sign-off)
- Exploratory (45 menit sesi/release)

## 6. Regression Set

Tag test mana yang masuk regression suite (dijalankan tiap release).

- TC-001, TC-003, TC-007 (smoke)
- ...

## 7. Performance / Load (Opsional)

- Target: <X> RPS sustained, p99 ≤ <Y>ms
- Tool: k6 / vegeta / locust
- Scenario: ramp-up 5 menit → plateau 15 menit → ramp-down
- Pass criteria: error rate <0.1%, p99 budget tidak terlewati

## 8. Exit Criteria

- [ ] 100% test case di matrix executed
- [ ] Pass rate ≥ 95%
- [ ] No P0/P1 defect open
- [ ] P2 defect ≤ 3
- [ ] Defect density: <X bug per 100 LOC changed
- [ ] Banned-field test green
- [ ] Performance budget tidak terlewati

## 9. Risk-Based Adjustment

(Kalau feature high-risk — payment, auth, data deletion — exit criteria diperketat. Tulis penyesuaian di sini.)

## 10. Test Plan Sign-off (Gate 5)

| Role | Nama | qa-greenlight | Tanggal |
|---|---|---|---|
| QA | | | |
| Tech Lead | | | |
