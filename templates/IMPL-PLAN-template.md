# Impl Plan — <Feature Name>

| Field | Value |
|---|---|
| **Status** | DRAFT / LGTM-PLAN / IN-DEV / DONE |
| **Spec ref** | `./SPEC-BE.md` + `./SPEC-FE.md` |
| **Created** | YYYY-MM-DD |
| **Last updated** | YYYY-MM-DD |
| **Target sprint** | Sprint N (YYYY-MM-DD → YYYY-MM-DD) |
| **Owner (PM)** | <nama> |
| **Tech Lead** | <nama> |

> **Lightweight timeline artifact** untuk sprint planning. Auto-generated oleh `/spec` aggregator dari estimate output `solution-architect` (BE) + `ui-impact-analyst` (FE).
>
> **Update saat sprint berjalan** — kalau ada slip atau scope change, edit langsung di sini (bukan SPEC-BE/SPEC-FE).
>
> **Audience:** PM + TL + all devs. Sprint review/planning artifact.

---

## 1. Effort Estimate (Chunk Level)

| Chunk | T-Shirt | Estimated Days | Owner | Spec ref |
|---|---|---|---|---|
| BE | <S/M/L> | <X-Y hari> | <BE Dev> | `SPEC-BE.md` § 11.6 |
| FE | <S/M/L> | <X-Y hari> | <FE Dev> | `SPEC-FE.md` § 11.6 |
| **Total (sequential)** | — | <total hari> | — | — |
| **Total (paralel via `[BE-CONTRACT-FROZEN]`)** | — | **<paralel hari>** | — | — |

**Konvensi T-shirt:** XS=<0.5d, S=1d, M=2-3d, L=4-5d, XL=>5d (split kalau XL).

---

## 2. Sub-items per Chunk

### BE Chunk (`/implement <slug>/be`)

| # | Sub-item | Estimate | Depends on | Blocks |
|---|---|---|---|---|
| BE-1 | <Migration / entity update> | XS | — | BE-2 |
| BE-2 | <Repository / query baru> | S | BE-1 | BE-3 |
| BE-3 | <Usecase + validation> | S | BE-2 | BE-4 |
| BE-4 | <Handler + OpenAPI sync> | S | BE-3 | BE-5, FE-6 |
| BE-5 | <Integration test + evidence> | S | BE-4 | — |

### FE Chunk (`/implement <slug>/fe`)

| # | Sub-item | Estimate | Depends on | Blocks |
|---|---|---|---|---|
| FE-1 | <Pinia store + composable> | S | — | FE-3 |
| FE-2 | <Mock API integration (paralel)> | S | **`[BE-CONTRACT-FROZEN]`** | FE-3 |
| FE-3 | <Komponen baru (compose catalog)> | M | FE-1, FE-2 | FE-4 |
| FE-4 | <5-state coverage + A11y> | S | FE-3 | FE-5 |
| FE-5 | <E2E + screenshot evidence> | S | FE-4 | FE-6 |
| FE-6 | <Switch mock → real BE integration> | XS | **BE-4 merged** | — |

---

## 3. Critical Path (ASCII Gantt)

```
       Day 1   Day 2   Day 3   Day 4   Day 5   Day 6   Day 7
BE:    [B1][B2-------][B3-----][B4----][B5----]
FE:    [F1][F2-------][F3----------][F4----][F5----][F6]
                       └─ paralel allowed via [BE-CONTRACT-FROZEN]
                                                    └─ FE-6 needs BE-4 merged

Critical path: <chain> = ~<X> hari (vs sequential BE+FE = <Y> hari)
```

> **Cara baca:** baris vertikal = hari. Bar `[B1]` = sub-item BE-1. Bar yang overlap dengan BE = paralel work dimungkinkan. Critical path = chain terpanjang.

---

## 4. Cross-Chunk Dependency Notes

- **FE-2 unlock condition:** SPEC-BE § 4 punya marker `🔒 [BE-CONTRACT-FROZEN]` (target: end of Day 1)
- **FE-6 unlock condition:** BE PR merged (target: Day 4-5)
- **Risk kalau contract berubah post-freeze:** BE Dev re-LGTM SPEC-FE + FE Dev re-impl FE-2 (estimasi +1 hari)
- **Risk kalau wireframe revision di Phase 2:** +0.5-1 hari (Designer + FE Dev sync)

---

## 5. Sprint Planning

| Item | Value |
|---|---|
| **Target sprint** | Sprint N (Mon-Fri × 1 minggu) |
| **Fit dalam sprint?** | Yes / No / Risk |
| **Demo readiness day** | Day <X> (FE PR ready) atau Day <X+1> (after QA smoke) |
| **Risk slip triggers** | (1) Push-back round 2 di Phase 1 (+1d), (2) Wireframe revision (+0.5-1d), (3) BE contract change post-freeze (+1d) |
| **Buffer untuk QA** | <X> hari (Phase 3 Gate 5 — terpisah dari critical path) |

---

## 6. Burndown (Update During Sprint)

> Update kolom "Status" + "Actual" saat sub-item closed.

### BE Burndown

| # | Sub-item | Estimate | Actual | Status | Notes |
|---|---|---|---|---|---|
| BE-1 | ... | XS (0.5d) | — | pending | — |
| BE-2 | ... | S (1d) | — | pending | — |
| ... | ... | ... | ... | ... | ... |

### FE Burndown

| # | Sub-item | Estimate | Actual | Status | Notes |
|---|---|---|---|---|---|
| FE-1 | ... | S (1d) | — | pending | — |
| FE-2 | ... | S (1d) | — | pending | — |
| ... | ... | ... | ... | ... | ... |

**Status legend:** pending / in-progress / blocked / done / dropped

---

## 7. Approval — LGTM-PLAN (Optional)

> Optional. Beberapa tim formal sign-off plan sebelum sprint start, beberapa tim langsung start setelah `/spec` selesai. Sesuaikan dengan team convention.

| Role | Nama | LGTM-PLAN | Tanggal |
|---|---|---|---|
| **TL** | | | |
| **PM** | | | |
| BE Dev (acknowledge) | | | |
| FE Dev (acknowledge) | | | |

---

## 8. Change Log

| Date | Change | Updated by |
|---|---|---|
| YYYY-MM-DD | Initial generation by `/spec` aggregator | — |
| YYYY-MM-DD | Scope change: tambah FE-7 (mobile responsive) | <PM> |
| ... | ... | ... |
