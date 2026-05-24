---
description: UI Impact Analysis — DIFF existing vs proposed + komponen reuse + wireframe HTML generation (v3.0). Dipanggil di Phase 1 (Pre-Dev), Phase 2 (Post-Dev), atau ad-hoc.
argument-hint: <feature-slug> [--mode=pre-dev|post-dev|quick-diff]
---

You are coordinating the **UI Impact Analysis** for feature `$ARGUMENTS`.

## Tugasmu

Invoke agent **`ui-impact-analyst`** dengan konteks lengkap. Agent akan:
- Baca catalog design system project + `SPEC-FE.md` + `SPEC-BE.md` § 4 (contract) + repo FE
- Output sesuai mode (Pre-Dev / Post-Dev / Quick-Diff)
- **Generate wireframe HTML** di `features/<slug>/wireframes/` (Pre-Dev mode only)

> **v3.0 difference:** Mode Pre-Dev sekarang HARUS generate wireframe HTML (D2 mitigation untuk FE handoff pain). Wireframe pakai Tailwind CDN, self-contained, double-click open di browser.

## Mode (auto-detect kalau tidak di-specify)

| Mode | Trigger | Output |
|---|---|---|
| **pre-dev** (default kalau Section 0-11 SPEC-FE.md kosong) | Phase 1 Spec — biasanya di-invoke dari `/spec` command | Isi SPEC-FE.md § 0-11 + generate `wireframes/*.html` |
| **post-dev** | Phase 2 — FE Dev sudah impl, butuh wireframe vs actual verification | Isi Appendix "Post-Dev Verification" di SPEC-FE.md (deviation flag) |
| **quick-diff** | Ad-hoc, sanity check di tengah jalan | Output tabular ke chat saja, TIDAK update file |

## Prasyarat

- Folder `features/$ARGUMENTS/` ada.
- File `features/$ARGUMENTS/SPEC-FE.md` ada (scaffold dari `templates/SPEC-FE-template.md`).
- Manifest `features/$ARGUMENTS/manifest.yaml` punya `has_ui_impact: true`.
- Project punya **design system catalog** (mis. `design-system-context.md` di root). Tanpa catalog, push-back ke user.
- (Pre-Dev mode) `templates/wireframe-template.html` exists sebagai starter.

## Aksi

1. **Validate prasyarat.** Kalau gagal, berhenti + push-back instruction ke user.

2. **Detect mode** kalau tidak di-specify:
   - SPEC-FE.md § 0-11 mostly empty → `pre-dev`
   - SPEC-FE.md § 0-11 filled + Appendix Post-Dev belum → `post-dev` (kalau FE PR open)
   - SPEC-FE.md lengkap, user invoke manual mid-cycle → `quick-diff`

3. **Invoke agent `ui-impact-analyst`** dengan prompt:
   - Mode yang aktif
   - Path absolut SPEC-FE.md, SPEC-BE.md, wireframes/ folder, catalog file
   - **(Pre-Dev only)** Instruction: generate wireframe HTML untuk setiap page utama yang disebut di SPEC. Pakai `templates/wireframe-template.html` sebagai starter. Embed 5-state interactive demo. Catatan inline reference ke komponen catalog (mis. `<!-- catalog: <AtomicButton variant="primary"> -->`).
   - **(Post-Dev only)** Instruction: compare screenshots actual (di `evidence/fe/screenshots/`) vs `wireframes/*.html`. Flag deviation >30% magnitude.

4. **Tunggu hasil dari agent** — agent akan update file via Write tool.

5. **Output ke chat:**
   - Mode yang dipakai
   - File path yang di-update / di-generate
   - **(Pre-Dev only)** Path `wireframes/index.html` — instruksi user untuk double-click open di browser preview
   - 3-5 highlight terpenting
   - Saran follow-up

## Wireframe HTML Generation (Pre-Dev mode)

**Yang harus agent generate:**

1. `features/<slug>/wireframes/index.html` — navigation hub ke semua page wireframe
2. `features/<slug>/wireframes/<page-name>.html` — 1 file per page utama (dashboard, modal, form, dll)

**Standar wireframe HTML:**

- Pakai Tailwind CDN: `<script src="https://cdn.tailwindcss.com"></script>`
- Self-contained (no build, no node_modules)
- Embed 5-state interactive demo untuk page dengan complex state (button toggle ganti state via inline JS)
- Inline HTML comment reference ke komponen catalog: `<!-- catalog: <ComponentName variant="X"> -->`
- Spec Notes collapsible di setiap section (`<details>` element) berisi:
  - Komponen catalog yang dipakai
  - Pitfall warning (kalau ada)
  - State coverage notes
  - Responsive notes

**Quality bar:**

- Visual baseline, BUKAN pixel-perfect design
- Komponen styling pakai Tailwind defaults (consistent baseline visual)
- Designer boleh override dengan Figma export di Phase 2

## Aturan Strict

- **Tidak boleh bypass agent.** Command ini cuma router.
- **Tidak boleh modify SPEC-FE.md atau wireframes sendiri.** Agent yang tulis.
- **Idempoten.** Re-run regenerate dari scratch (replace, bukan append).
- **Wireframe HTML wajib generated di Pre-Dev mode.** Kalau agent skip, push-back: "Wireframe HTML missing — D2 mitigation tidak applied."
- **Bahasa Indonesia** untuk narrative; English untuk path/code identifier.

## Definition of Done

- Mode sesuai sudah dijalankan oleh agent.
- File SPEC-FE.md ter-update (Pre-Dev / Post-Dev mode).
- Wireframe HTML generated (Pre-Dev mode, kalau page utama ada).
- Output chat ringkas: mode + file paths + highlight + saran follow-up + **instruksi double-click wireframe HTML** kalau Pre-Dev.

## Contoh invocation

```bash
# Pre-Dev (biasanya di-invoke otomatis dari /spec)
/ui-impact-analysis my-feature

# Post-Dev (compare wireframe vs actual)
/ui-impact-analysis my-feature --mode=post-dev

# Quick diff (sanity check mid-cycle)
/ui-impact-analysis my-feature --mode=quick-diff
```
