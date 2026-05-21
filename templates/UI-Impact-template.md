# UI Impact — <Feature Name>

> **Artifact WAJIB** untuk feature yang menyentuh UI. Default ON saat scaffold; skip HANYA kalau feature 100% BE-only (`has_ui_impact: false` di `manifest.yaml`).
>
> **Posisi di workflow:**
> - **Section 1 (Pre-Dev) WAJIB lengkap sebelum LGTM-PRD** (Gate 1). Tanpa ini, Gate 1 tidak boleh close.
> - **Section 2 (Post-Dev)** diisi FE Dev di Gate 4 (PR ready for review).
> - **Section 3 (Agent Analysis)** auto-generated via `/ui-impact-analysis <slug>`.

| Field | Value |
|---|---|
| **Status** | DRAFT / PRE-DEV / IN-DEV / POST-DEV / SIGNED-OFF |
| **Feature slug** | `<slug>` |
| **PM** | <nama> |
| **Designer** | <nama> |
| **FE Dev** | <nama> |
| **Tech Lead (consulted)** | <nama> |
| **Figma / mockup link** | <url> |
| **Existing page reference** | <path kalau improvement; "N/A — new page" kalau baru> |
| **Created** | YYYY-MM-DD |
| **Last Agent Analysis** | (belum dijalankan) |

---

## 1. Pre-Dev Section

> **Diisi oleh:** Designer + PM (dibantu agent `ui-impact-analyst` via `/groom` atau `/ui-impact-analysis`).
> **Posisi:** **Gate 1 — WAJIB sebelum LGTM-PRD.**

### 1.1 Existing Page Reference (kalau ini improvement)

- **Page existing:** `<path>` (mis. `apps/<app>/pages/<area>/<page>.vue` atau equivalent untuk stack lain)
- **Screenshot existing:** `<path>` atau "perlu di-capture manual"
- **Baseline commit / versi:** `<commit hash atau tag>` (opsional)
- **Kalau page baru:** tulis "N/A — new page" + alasan kenapa page baru dibutuhkan

### 1.2 Expected UI

- **Figma node / mockup:** [link] atau path `expected/<screenshot>.png`
- **Description (1-3 kalimat):** apa yang berubah dari user perspective
- **Tribe scope FE:** (kalau ada multi-tribe setup) — sebut tribe + feature code
- **Release guard / feature flag target:** sesuai pattern monorepo

### 1.3 DIFF — Apa yang Berubah dari Existing

> Diisi oleh agent `ui-impact-analyst` (atau manual oleh Designer/PM kalau page baru).

| Kategori | Existing | Proposed | Magnitude |
|---|---|---|---|
| Komponen visual | <deskripsi / "N/A — new"> | <deskripsi> | MAJOR / MINOR / NONE |
| State & Behavior | ... | ... | MAJOR / MINOR / NONE |
| API call | ... | ... | MAJOR / MINOR / NONE |
| Route / Navigation | ... | ... | MAJOR / MINOR / NONE |
| Data shape | ... | ... | MAJOR / MINOR / NONE |

### 1.4 Constraints & Existing Patterns

- **Komponen catalog yang dipakai (dari project design system catalog):**
  - `<Component A>` — alasan dipilih
  - `<Component B>` — alasan dipilih
- **Pitfall warning** (dari catalog project — variant naming, prop mapping, dll):
  - mis. "Figma 'outlined' button → di code pakai `variant='secondary'`, BUKAN `variant='outlined'`"
- **Design system tokens:** tipografi, color, spacing yang spesifik
- **Responsive scope:** desktop-first / mobile-first / both

> **Note:** Agent `ui-impact-analyst` butuh akses ke catalog komponen project (mis. file `design-system-context.md` atau equivalent). Tiap project sediakan sendiri sesuai stack mereka.

### 1.5 5-State Coverage Plan (WAJIB)

| State | Expected? | Catatan konkret |
|---|---|---|
| Empty | ya / N/A | (kondisi kapan empty) |
| Loading | ya / N/A | (skeleton / spinner / inline — komponen catalog yang mana) |
| Error | ya / N/A | (422 validation / 404 / 5xx / network — mapping ke field) |
| Success | ya | (happy path) |
| Edge case | ya / N/A | (long text, banyak item, role-based variation — spesifik) |

### 1.6 Open UI Questions (untuk PM/Designer/TL jawab sebelum LGTM-PRD)

- [ ] OPEN — UI-Q1: ...
- [ ] OPEN — UI-Q2: ...
- [x] RESOLVED — UI-Q3: ... → Jawaban: ...

---

## 2. Post-Dev Section

> **Diisi oleh:** FE Dev di Gate 4 (saat PR open / ready for review).

### 2.1 Actual Implementation

- **Screenshot bukti:** `evidence/<task-id>/screenshots/<name>.png` (per 5-state kalau relevan)
- **Implemented at (file paths):**
  - `<repo>/<path>/Component.<ext>` — komponen utama
  - `<repo>/<path>/store.<ext>` — state management (kalau ada)
  - `<repo>/<path>/composable.<ext>` — composable / hook (kalau ada)
- **Description (1-3 kalimat):** deviasi vs Pre-Dev plan, atau "mostly as expected"
- **Build & lint status:** ok / ada warning

### 2.2 Deviations from Pre-Dev Plan

> Setiap deviasi WAJIB punya alasan grep-able. Kalau tidak ada, tulis "tidak ada".

- (list deviasi vs Pre-Dev plan, dengan alasan teknis atau UX-driven)

---

## 3. Agent Analysis

> **Diisi oleh:** Claude via `/ui-impact-analysis <slug>` setelah Pre-Dev DAN Post-Dev section lengkap.
> Section ini auto-generated — re-run command kalau perlu refresh.

> _Belum dijalankan. Invoke `/ui-impact-analysis <slug>` setelah Pre-Dev + Post-Dev terisi._

### 3.1 Komponen Baru

- (path file yang di-build dari nol)

### 3.2 Komponen Reuse

- (komponen dari catalog yang dipakai — path + alasan)
- **Flag**: komponen yang di-build dari nol padahal ada di catalog (regression risk konsistensi)

### 3.3 State Management

- Store baru / modified: ...
- Composable / hook baru / modified: ...
- Reactivity hot path / concern: ...

### 3.4 Route & Navigation

- Route baru: ...
- Sidebar / breadcrumb update: ...
- Navigation guard / middleware: ...

### 3.5 API Integration

- BE endpoint yang dipanggil: ...
- Request shape: ...
- Response handling (success/error): ...
- Validation message mapping (FE→BE 422): ...

### 3.6 5-State Coverage Actual

| State | Implemented? | Path ke screenshot / komponen | Match vs Pre-Dev? |
|---|---|---|---|
| Empty | | | |
| Loading | | | |
| Error | | | |
| Success | | | |
| Edge case | | | |

### 3.7 A11y Notes

- Keyboard navigation (tab order, ESC handler): ...
- Screen reader: aria-label, aria-describedby, aria-live: ...
- Focus management: ...
- Color contrast (WCAG AA): ...

### 3.8 Release Guard / Feature Flag

- Placement di kode: ...
- Backend feature flag yang dipair: ...
- Fallback behavior kalau gate closed: ...

### 3.9 Flags / Concerns

- (Hal-hal yang AI catat sebagai potensi regression / divergence dari pattern catalog)
- (Pattern yang break consistency dengan komponen serupa)
- (Performance concern: bundle size, layout shift, INP)

---

## 4. Sign-off (Gate 1 LGTM-PRD WAJIB lengkap)

> **3 approver wajib + 1 consulted.** Approval di Slack/DM TIDAK terhitung — harus grep-able di file ini.

| Role | Nama | LGTM-PRD (Gate 1) | Tanggal |
|---|---|---|---|
| **PM** | | | |
| **Designer** | | | |
| **Frontend Dev** | | | |
| Tech Lead (consulted, optional sign) | | | |

| Role | Nama | LGTM Post-Dev (Gate 4) | Tanggal |
|---|---|---|---|
| FE Dev | | | |
| Designer review | | | |
| PM verify | | | |
