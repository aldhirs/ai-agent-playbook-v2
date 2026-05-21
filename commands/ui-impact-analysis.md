---
description: UI Impact Analysis — bandingkan existing page vs proposed page, screening reuse komponen design system, draft plan implementasi FE. Dipanggil di Gate 1 (Pre-Dev), Gate 4 (Post-Dev), atau ad-hoc (Quick Diff). Invoke agent `ui-impact-analyst`.
argument-hint: <feature-slug> [--mode=pre-dev|post-dev|quick-diff]
---

You are coordinating the **UI Impact Analysis** for feature `$ARGUMENTS`.

## Tugasmu

Invoke agent **`ui-impact-analyst`** dengan konteks lengkap. Agent akan baca catalog design system project + `G1-UI-IMPACT.md` + PRD + repo FE, lalu output sesuai mode.

## Mode (auto-detect kalau tidak di-specify)

| Mode | Trigger | Output |
|---|---|---|
| **pre-dev** (default kalau Section 1 kosong) | Gate 1 grooming — Pre-Dev belum lengkap | Isi/update Section 1 di `G1-UI-IMPACT.md` |
| **post-dev** | Gate 4 — Section 2 (Post-Dev) sudah diisi FE Dev | Isi Section 3 (Agent Analysis) di `G1-UI-IMPACT.md` |
| **quick-diff** | Ad-hoc, mid-gate sanity check | Output tabular ke chat saja, TIDAK update file |

## Prasyarat

- Folder `features/$ARGUMENTS/` ada.
- File `features/$ARGUMENTS/G1-UI-IMPACT.md` ada. Kalau tidak ada, **stop** dan minta user scaffold dulu (UI-IMPACT.md default ON saat scaffold).
- Manifest `features/$ARGUMENTS/manifest.yaml` punya `has_ui_impact: true`.
- Project punya **design system catalog** (mis. file `design-system-context.md` di root, atau equivalent path) yang agent bisa baca.

## Aksi

1. **Validate prasyarat** di atas. Kalau gagal, berhenti dan instruksikan user.

2. **Detect mode** kalau tidak di-specify:
   - Section 1 (Pre-Dev) kosong / placeholder → `pre-dev`
   - Section 1 lengkap + Section 2 lengkap → `post-dev`
   - Section 1 lengkap, Section 2 belum, user invoke manual → `quick-diff`

3. **Invoke agent `ui-impact-analyst`** dengan prompt:
   - Mode yang aktif
   - Path absolut `G1-UI-IMPACT.md`, `G1-PRD.md`, `G2-TECH-DESIGN.md` (kalau ada)
   - Path catalog design system project
   - Path repo FE relatif
   - Tribe / module scope yang aktif (dari folder feature)
   - Instruksi: ikuti Mode A/B/C di agent definition.

4. **Tunggu hasil dari agent** — agent akan langsung update file via Write tool.

5. **Output ke chat:**
   - Konfirmasi mode yang dipakai
   - Path file yang di-update
   - 3-5 highlight terpenting dari analisa agent
   - Saran follow-up

## Aturan main

- **Tidak boleh bypass agent.** Command ini cuma router — analisa actual dilakukan oleh `ui-impact-analyst`.
- **Tidak boleh modify file** sendiri. Agent yang tulis.
- **Idempoten.** Re-run regenerate section dari scratch (replace, bukan append).
- **Bahasa Indonesia** untuk narrative; English untuk path/code identifier.

## Definition of Done

- Mode yang sesuai sudah dijalankan oleh agent.
- File `G1-UI-IMPACT.md` ter-update (atau output di chat saja untuk mode quick-diff).
- Output ringkas ke chat: mode + file path + 3-5 highlight + saran follow-up (≤200 words).
- Tidak ada placeholder `<...>` di section yang di-claim sudah diisi.

## Contoh invocation

```
/ui-impact-analysis pricing-revamp
/ui-impact-analysis pricing-revamp --mode=post-dev
/ui-impact-analysis pricing-revamp --mode=quick-diff
```
