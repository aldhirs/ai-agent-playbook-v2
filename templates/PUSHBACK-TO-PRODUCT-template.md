# Pushback to Product Team — <Feature Name>

> **Kapan template ini dipakai:** Saat tim implementasi adopt PRD eksternal (mis. dari tim product korporat / vendor / handoff doc) ke `G1-PRD.md` versi tim. Selama adopsi pasti ada **gap konkret** — template ini dipakai untuk dokumentasikan gap + dorong feedback balik ke author PRD eksternal.
>
> **Tujuan dokumen:** Setiap gap = pertanyaan + saran resolusi. Author PRD eksternal tinggal pilih: accept saran / propose alternatif / clarify.
>
> **Aturan:** push back ini **bukan** kritik. Ini bagian dari proses "AI Proposes, Human Approves" (Prinsip 15) — supaya keputusan kritis tercatat sebelum coding mulai.

| Field | Value |
|---|---|
| **Feature slug** | `<slug>` |
| **Source PRD** | `<path / link ke PRD eksternal>` |
| **Status PRD source** | DRAFT / SIGNED |
| **Reviewer (tim implementasi)** | <tribe / squad> |
| **Created** | YYYY-MM-DD |
| **Target acknowledgement deadline** | YYYY-MM-DD |
| **Channel komunikasi** | <Slack channel / ClickUp thread / dll> |

---

## Ringkasan Gap

| # | Kategori | Jumlah gap | Severity |
|---|---|---|---|
| A | Field metadata PRD kosong (KR, BRD, sign) | <N> | LOW (administrative) |
| B | Scope ambiguity (target user, sub-feature boundary) | <N> | MEDIUM |
| C | NFR & technical constraint tidak ada | <N> | HIGH (block Gate 2 design) |
| D | Behavior detail tidak tertulis (edge case) | <N> | HIGH (block AC sign-off) |
| E | UI spec gap (mobile, accessibility, edge layout) | <N> | MEDIUM (block UI-IMPACT sign-off) |

**Total: <N> gap** — <N> HIGH (wajib resolve sebelum LGTM-PRD), <N> MEDIUM (resolve sebelum Gate 2), <N> LOW.

---

## A. Field Metadata PRD Kosong (LOW)

### A1. <Nama gap>

- **Konteks:** (apa yang kurang, dengan referensi line / section ke PRD asli)
- **Dampak:** (kenapa penting untuk tim implementasi)
- **Saran:** (draft solusi konkret dari tim impl)
- **Action:** (siapa yang harus take action — biasanya PM eksternal)

### A2. ...

---

## B. Scope Ambiguity (MEDIUM)

### B1. <Nama gap>

- **Konteks:** ...
- **Dampak:** ...
- **Saran:** ...
- **Action:** ...

---

## C. NFR & Technical Constraint Tidak Ada (HIGH — block Gate 2)

### C1. <Nama gap, mis. Latency target>

- **Konteks:** PRD tidak punya target performance untuk endpoint X
- **Saran tim impl draft:**
  - p99 latency ≤ Xms untuk happy path
  - Throughput ≤ Y RPS sustained
- **Action:** PM eksternal confirm atau revise.

### C2. ...

---

## D. Behavior Detail Tidak Tertulis (HIGH — block AC sign-off)

### D1. <Nama gap, mis. edge case>

- **Konteks:** (skenario yang tidak dibahas di AC)
- **Saran tim impl draft:** (behavior yang diusulkan)
- **Action:** PM eksternal decide behavior.

---

## E. UI Spec Gap (MEDIUM — block UI-IMPACT.md sign-off)

### E1. <Nama gap, mis. mobile responsive>

- **Konteks:** Figma desain desktop. Tidak ada mockup mobile.
- **Saran tim impl draft:** Layout default untuk mobile
- **Action:** Designer eksternal provide mobile mockup, ATAU confirm desktop-only.

### E2. ...

---

## Action Items (untuk tim product / PRD author eksternal)

- [ ] **HIGH** — Resolve C1-C5 + D1-D5 sebelum **YYYY-MM-DD** (target LGTM-PRD tim)
- [ ] **MEDIUM** — Resolve B + E sebelum Gate 2 mulai
- [ ] **LOW** — A boleh paralel, tidak block

## Komunikasi

Channel: <Slack / ClickUp / dll>.

Update file ini setiap ada response — append ke section relevant, sertakan tanggal + nama responder.

## Status Tracking

| Gap | Status | Resolved Date | Resolution |
|---|---|---|---|
| A1 | OPEN | — | — |
| A2 | OPEN | — | — |
| B1 | OPEN | — | — |
| C1 | OPEN | — | — |
| D1 | OPEN | — | — |
| E1 | OPEN | — | — |
