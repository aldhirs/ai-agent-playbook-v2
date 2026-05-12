# PRD — <Feature Name>

| Field | Value |
|---|---|
| **Status** | DRAFT / IN-REVIEW / **SIGNED** / IMPLEMENTED |
| **Owner (PM)** | <nama> |
| **Tech Lead** | <nama> |
| **Tribe / Sub-tribe** | <id> |
| **Jira / Linear** | <link> |
| **Created** | YYYY-MM-DD |
| **Signed at Gate 1** | YYYY-MM-DD by <PM> & <Tech Lead> |

---

## 1. Problem Statement

(1 paragraf, no jargon. Apa masalahnya, untuk siapa, kenapa sekarang. Kalau butuh >3 kalimat untuk jelaskan masalah, masalahnya belum cukup tajam.)

## 2. Target User & Jobs-to-be-Done

- **Primary user:** <persona>
- **Secondary user:** <persona, kalau ada>
- **Job-to-be-done:** "Ketika <konteks>, saya ingin <aksi>, supaya <hasil>."

## 3. Scope

### In scope

- ...
- ...

### Out of scope (eksplisit)

- ...
- ...

> *Catatan: kalau out-of-scope kosong, kemungkinan scope tidak cukup dipikirkan. Paksa minimal 2 item.*

## 4. User Stories & Acceptance Criteria

### Story 1: <judul singkat>

**Sebagai** <persona>, **saya ingin** <aksi>, **supaya** <hasil>.

**Acceptance criteria (Given/When/Then):**

- **Given** <kondisi awal>, **When** <aksi>, **Then** <hasil terobservasi>.
- **Given** <kondisi awal>, **When** <aksi>, **Then** <hasil terobservasi>.

### Story 2: ...

## 5. Non-Functional Requirements

- **Performance:** p99 latency ≤ <X>ms; throughput ≤ <Y> RPS.
- **Availability:** ≥ <Z>%
- **Security / privacy:** field PII yang tersentuh: <list>
- **Compliance:** <regulasi yang berlaku, kalau ada>
- **Accessibility (UI):** WCAG AA minimal, keyboard navigation, screen reader label.

## 6. Dependencies

| Service / Tim | Tipe ketergantungan | Sudah dibahas? |
|---|---|---|
| <service> | API contract change | ya/tidak |
| <tim> | manual setup | ya/tidak |

## 7. Risks & Mitigations

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| ... | L/M/H | L/M/H | ... |

## 8. Open Questions

> Diisi oleh Groomer Agent sebagai daftar awal. **Harus tuntas sebelum Gate 1.** Status: OPEN → RESOLVED.

- [ ] OPEN — Q1: ...
- [ ] OPEN — Q2: ...
- [x] RESOLVED — Q3: ... → Jawaban: ...

## 9. Out-of-Scope Decisions (untuk fase berikutnya)

(Hal yang sengaja ditunda. Dokumentasi supaya tidak hilang.)

## 10. Approval Gate 1

| Role | Nama | Approval (LGTM-PRD) | Tanggal |
|---|---|---|---|
| PM | | | |
| Tech Lead | | | |
