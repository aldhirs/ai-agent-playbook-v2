# PRD — <Feature Name>

| Field | Value |
|---|---|
| **Status** | DRAFT / IN-REVIEW / **SIGNED** / IMPLEMENTED |
| **Owner (PM)** | <nama> |
| **Designer** | <nama> |
| **Frontend Dev** | <nama> |
| **Tech Lead (consulted)** | <nama> |
| **Tribe / Sub-tribe** | <id> |
| **Jira / Linear** | <link> |
| **Created** | YYYY-MM-DD |
| **Signed at Gate 1** | YYYY-MM-DD by <PM> & <Designer> & <FE Dev> (Tech Lead: consulted) |

> **UI-IMPACT.md link:** `./G1-UI-IMPACT.md` — Section 1 (Pre-Dev) **WAJIB lengkap sebelum LGTM-PRD**. Tanpa itu, Gate 1 tidak boleh close.
> (Untuk feature 100% BE-only — `has_ui_impact: false` di manifest — skip prasyarat ini.)

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

## 5. UI Vision (ringkas — detail di G1-UI-IMPACT.md)

- **Figma link:** <url>
- **Existing page yang di-improve:** `<path>` atau "N/A — new page"
- **Ringkasan perubahan UI (2-3 kalimat):** ...
- **Detail lengkap** (DIFF, 5-state plan, komponen reuse, A11y): → `./G1-UI-IMPACT.md` Section 1

> *Skip section ini kalau feature 100% BE-only. Tulis: "Tidak berlaku — BE-only feature."*

## 6. Non-Functional Requirements

- **Performance:** p99 latency ≤ <X>ms; throughput ≤ <Y> RPS.
- **Availability:** ≥ <Z>%
- **Security / privacy:** field PII yang tersentuh: <list>
- **Compliance:** <regulasi yang berlaku, kalau ada>
- **Accessibility (UI):** WCAG AA minimal, keyboard navigation, screen reader label.

## 7. Dependencies

| Service / Tim | Tipe ketergantungan | Sudah dibahas? |
|---|---|---|
| <service> | API contract change | ya/tidak |
| <tim> | manual setup | ya/tidak |

## 8. Risks & Mitigations

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| ... | L/M/H | L/M/H | ... |

## 9. Open Questions

> Diisi oleh Groomer Agent sebagai daftar awal. **Harus tuntas sebelum Gate 1.** Status: OPEN → RESOLVED.

- [ ] OPEN — Q1: ...
- [ ] OPEN — Q2: ...
- [x] RESOLVED — Q3: ... → Jawaban: ...

## 10. Out-of-Scope Decisions (untuk fase berikutnya)

(Hal yang sengaja ditunda. Dokumentasi supaya tidak hilang.)

## 11. Approval Gate 1 (LGTM-PRD)

> **3 approver wajib + 1 consulted.** Approval di Slack/DM TIDAK terhitung — harus grep-able di file ini.
> Prasyarat: Section 1 (Pre-Dev) di `G1-UI-IMPACT.md` harus lengkap (kecuali feature BE-only).

| Role | Nama | LGTM-PRD | Tanggal |
|---|---|---|---|
| **PM** | | | |
| **Designer** | | | |
| **Frontend Dev** | | | |
| Tech Lead (consulted, optional sign) | | | |
