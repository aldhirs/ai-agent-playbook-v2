# Spec FE — <Feature Name>

| Field | Value |
|---|---|
| **Status** | DRAFT / LGTM-SPEC-FE / IN-DEV / DONE |
| **Owner (PM)** | <nama> |
| **Designer** | <nama> |
| **FE Dev** | <nama> |
| **Tech Lead (consulted)** | <nama> |
| **Tribe / FE feature code** | <id> / `<feature-code>` |
| **Created** | YYYY-MM-DD |
| **LGTM-SPEC-FE at** | (belum — lihat § 12) |

> **Kontrak BE:** lihat `./SPEC-BE.md` § 4 (status: `[BE-CONTRACT-FROZEN]` = boleh paralel work).
> **Wireframes:** `./wireframes/index.html` (open di browser untuk preview semua page)
> **Open questions:** `./OPEN-QUESTIONS.md`

---

## 0. Existing Analysis (WAJIB — Prinsip 19)

> AI **tidak boleh proceed** ke § 1 tanpa § 0 lengkap.

| Audit area | Finding | Reuse opportunity |
|---|---|---|
| Page existing yang di-improve | `<path>` atau "N/A — new page" | EXTEND / NEW |
| Komponen catalog yang dipakai | <list dari design-system-context.md> | YA / TIDAK |
| Composable / hook existing | mis. `useReleaseGuard`, `useFormValidation` | YA → reuse |
| Pinia store / state existing | mis. `useAuthStore`, `useFeatureXStore` | EXTEND / NEW |
| Pattern serupa di tribe lain | <link page atau component> | — |

**Magnitude pick:** ⬜ NO-CHANGE · ⬜ **EXTEND** (default) · ⬜ NEW-MODULE · ⬜ REWRITE

**Justifikasi kalau > EXTEND:** <kenapa EXTEND tidak cukup, komponen catalog apa yang masih dipakai, design system consistency plan>

---

## 1. Problem & UI Vision (1 paragraf)

(Apa masalah user-facing-nya. Apa yang akan berubah dari user perspective. Max 3 kalimat.)

---

## 2. Wireframe Preview 🖼️

> **Open di browser** untuk preview interactive 5-state.
>
> Wireframe = baseline visual + behavior intent. Bukan final pixel-perfect. Designer boleh override dengan Figma export di Phase 2.

| Page | Wireframe file | Screenshot inline | Catatan |
|---|---|---|---|
| Dashboard | [`./wireframes/dashboard.html`](./wireframes/dashboard.html) | ![](./wireframes/dashboard.png) | Composite score + active commitments |
| Form X | [`./wireframes/form-x.html`](./wireframes/form-x.html) | ![](./wireframes/form-x.png) | Inline validation demo |
| Modal Y | [`./wireframes/modal-y.html`](./wireframes/modal-y.html) | ![](./wireframes/modal-y.png) | 5-state interactive (klik tombol di demo) |

> **Designer override:** Kalau Figma sudah final, replace wireframe HTML dengan PNG export + Figma link di kolom Catatan.

---

## 3. DIFF — Existing vs Proposed

| Kategori | Existing | Proposed | Magnitude |
|---|---|---|---|
| Komponen visual | <deskripsi / "N/A — new"> | <deskripsi> | MAJOR / MINOR / NONE |
| State & Behavior | ... | ... | MAJOR / MINOR / NONE |
| API call | ... (refer SPEC-BE § 4) | ... | MAJOR / MINOR / NONE |
| Route / Navigation | ... | ... | MAJOR / MINOR / NONE |
| Data shape | ... | ... | MAJOR / MINOR / NONE |

---

## 4. State Management Changes

### 4.1 Store (Pinia atau equivalent)

- **Store baru:** `<store-name>` di `<path>` — alasan
- **Store modified:** `<existing-store>` tambah action `<x>` / getter `<y>`
- **State shape:**
  ```typescript
  interface FeatureState {
    items: Item[]
    loading: boolean
    error: string | null
  }
  ```

### 4.2 Composable / hook

- **Baru:** `useFeatureX()` di `<path>` — wraps API call + transform
- **Reuse:** `useReleaseGuard`, `useFormValidation` dari shared

---

## 5. API Integration (refer SPEC-BE § 4)

| FE call | BE endpoint | Request shape | Response handling | Error mapping |
|---|---|---|---|---|
| `createResource()` di `useFeatureX` | `POST /v1/<resource>` (SPEC-BE § 4.1) | `{ field1, field2 }` + `Idempotency-Key` UUID generate | 201 → toast success + refresh list; cached idempotent → silent | 422 → inline error (lihat validation matrix SPEC-BE § 4.3); 5xx → toast retry |

**Validation:** Refer **SPEC-BE.md § 4.3 Validation Matrix** — kolom "FE UI placement" sudah specify inline/toast/banner/modal per field.

---

## 6. 5-State Plan per Page

> **WAJIB lengkap per page yang di-touch.** State yang skip harus tulis "N/A + alasan".

### Page: Dashboard

| State | Expected? | Komponen yang dipakai | Catatan |
|---|---|---|---|
| Empty | YA | `<EmptyState type="first-use">` (catalog: `MoleculeEmpty`) | Trigger: user belum punya commitment. Kalimat: "Belum ada commitment. Pilih aktivitas pertama." + CTA "Lihat rekomendasi" |
| Loading | YA | `<AtomicSkeleton>` (catalog) untuk composite score widget + grid | Tampilkan skeleton selama API in-flight |
| Error | YA | `<MoleculeBanner type="error">` (catalog) | 5xx: "Gagal load dashboard. <button>Refresh</button>" |
| Success | YA | Default render | Happy path |
| Edge | YA | Score 100% capped: `<AtomicBadge>` highlight "maxed out" per dimensi | Refer SPEC-BE § 5 (logic capping di BE) |

### Page: Modal Commit

| State | Expected? | Komponen | Catatan |
|---|---|---|---|
| Empty | N/A | — | Modal pre-filled defaults |
| Loading | YA | Submit button: `<AtomicButton loading>` (catalog) | Disable button + spinner saat in-flight |
| Error | YA | Inline date picker error (validation matrix SPEC-BE § 4.3) | 422 deadline invalid → red border + message di bawah picker |
| Success | YA | Close modal + redirect dashboard + toast success | — |
| Edge | YA | Activity sudah committed parallel: dialog `<MoleculeDialogConfirm>` | Block dengan "Kamu sudah punya commitment aktif untuk ini." |

(Tambah section per page yang touched.)

---

## 7. Komponen Detail

### 7.1 Reuse dari catalog ✅

| Komponen catalog | Dipakai di | Alasan |
|---|---|---|
| `<AtomicButton variant="primary">` | CTA utama | Convention catalog, Figma "filled" → variant=primary |
| `<MoleculeModal>` | Commit modal | Match interaction pattern existing |
| `<AtomicInput>` + `<AtomicLabel>` | Form field | Inline validation built-in |

### 7.2 Komponen baru (kalau ada — justify)

| Komponen | Path baru | Justifikasi kenapa catalog tidak cukup |
|---|---|---|
| `<FeatureXChart>` | `apps/<your-app>/components/<feature>/Chart.vue` | Chart specific untuk composite score visualisasi, catalog tidak punya equivalent |

### 7.3 Pitfall warning dari catalog

- **Figma 'outlined' button → di code pakai `variant='secondary'`, BUKAN `variant='outlined'`** (catalog naming convention)
- `iconLeft` di `AtomicButton` = component, di `AtomicInput` = string — jangan swap
- (refer pitfall section di `design-system-context.md`)

---

## 8. A11y Plan (WCAG AA minimum)

- **Keyboard navigation:** Tab order Dashboard → Manage → Commit modal trap focus, ESC close
- **Screen reader:** `aria-label` untuk button icon-only, `aria-live="polite"` untuk score updates, `aria-invalid` + `aria-describedby` untuk error
- **Focus management:** Modal trap focus + return ke trigger button saat close
- **Color contrast:** Cek semua text vs background ≥ 4.5:1 (gunakan WebAIM checker)

---

## 9. Release Guard / Feature Flag

- **Pattern:** `<GuardReleaseGuard tribe="<tribe>" code="<feature-code>">` di sekeliling entry component
- **Backend feature flag yang dipair:** `<flag-name>` (lihat SPEC-BE § 9 Rollout Plan)
- **Fallback kalau gate closed:** Redirect ke existing page atau tampilkan "Coming soon"

---

## 10. Test Approach

### 10.1 Component test (Vitest + Vue Test Utils)

- Render dengan props normal
- Render loading state
- Render error state  
- User interaction (click → emit event)
- Edge case (long text, empty data)

### 10.2 E2E test (Playwright) — kalau termasuk critical path

- Happy: login → dashboard → commit → mark done
- Validation: invalid input → error inline
- Release guard: flag OFF → tidak render feature

### 10.3 Evidence path (Phase 2 output)

`features/<slug>/evidence/fe/{screenshots/, test.log}`
Screenshots: minimum 5 (default, loading, error, success, edge per page utama)

---

## 11. Open Questions

> Kosong → ✅ siap LGTM.
>
> Push-back ke `./OPEN-QUESTIONS.md` kalau block downstream.

- [ ] OPEN — Q1: ...
- [x] RESOLVED — Q2: ... → Jawaban: ...

---

## 12. Approval — LGTM-SPEC-FE

> Grep-able. Slack/DM TIDAK terhitung.

| Role | Nama | LGTM-SPEC-FE | Tanggal |
|---|---|---|---|
| **Designer** | | | |
| **FE Dev** | | | |
| PM (consulted, optional) | | | |

**Approval format:** `LGTM-SPEC-FE by <nama> on YYYY-MM-DD`

---

## Appendix — Post-Dev Verification (Phase 2 output)

> Diisi FE Dev di Gate 4 saat PR open. Triggers `ui-impact-analyst` Mode B untuk auto-compare wireframe vs actual.

### Implemented at (file paths)
- `apps/<your-app>/pages/<area>/<page>.vue`
- `apps/<your-app>/components/<feature>/<Component>.vue`
- `apps/<your-app>/stores/<store>.ts`

### Wireframe vs Actual deviation
- ☐ Match (no deviation)
- ☐ Deviation: <list + alasan teknis/UX-driven>

### Screenshot per 5-state (link evidence folder)
- Empty: `evidence/fe/screenshots/dashboard-empty.png`
- Loading: ...
- ...
