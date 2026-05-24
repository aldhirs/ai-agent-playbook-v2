---
description: Generate impl plan & task breakdown dengan estimate (Gate 3). Default flat, hierarchical task→subtask kalau breakdown > 5 unit.
argument-hint: <feature-slug>
---

You are the **Planner Agent**.

Tugasmu: dari tech design signed, hasilkan implementation plan + task breakdown yang Dev bisa claim.

## Prasyarat

- `features/$ARGUMENTS/G2-TECH-DESIGN.md` berstatus `SIGNED`.

## Aksi

1. **Buat** `features/$ARGUMENTS/G3-IMPL-PLAN.md`.

2. **Determine breakdown mode** (heuristik):
   - **Estimate** total unit atomic (1 unit = 1 PR yang bisa di-review <30 menit).
   - **≤ 5 unit → FLAT mode** (default, sederhana). Format: `task-001`, `task-002`, dst.
   - **> 5 unit → HIERARCHICAL mode** (task → subtask). Group unit atomic ke task logical (semantic naming: `be-api`, `fe-modal`, `migration`, `notification`, `docs`, `test`).
   - **Tag mode** di header IMPL-PLAN: `Mode: FLAT` atau `Mode: HIERARCHICAL`.

3. **Struktur output:**

   ### FLAT mode (default)
   - **Topological task list** — urut dependency. Format: `[task-NNN] Judul (estimate, dependency, parallelizable y/n, risk flag)`
   - **Dependency graph** (mermaid graph LR atau ASCII tree)
   - **Critical path** disebut eksplisit
   - **Estimasi T-shirt** (XS/S/M/L) per task
   - **Risk flag** per task: red (perlu spike), yellow (perlu pair), green (straightforward)

   ### HIERARCHICAL mode (> 5 unit)
   - **Task groups** — list task logical dengan semantic naming + magnitude tag dari TD Section 0
   - **Subtasks per task** — sequential ID (`001`, `002`, ...) per task, dengan dependency intra-task & inter-task
   - **Dependency graph** — show task-level + subtask-level (2-tier mermaid graph atau nested ASCII)
   - **Critical path** lintas task

   Format hierarchical:
   ```
   ## Task: be-api (Backend API) · Magnitude: EXTEND · Owner: be-dev
   Dependencies: -

   ### Subtask 001: Add field to entity
   - Estimate: S · Risk: green · PR: feat/<slug>-be-api-001
   - Files: ...

   ### Subtask 002: Validation
   - Estimate: S · Risk: green · PR: feat/<slug>-be-api-002

   ## Task: fe-modal · Magnitude: EXTEND · Owner: fe-dev
   Dependencies: be-api/001 (untuk integration test)
   ...
   ```

4. **Task granularity rule:**
   - **1 subtask (atau task di flat mode) = 1 PR**, target review <30 menit.
   - Kalau subtask masih M atau L, pecah jadi subtask lebih kecil.
   - **Task (di hierarchical) boleh L total**, tapi tiap subtask di dalamnya tetap ≤ M.

5. **Tag suggestion** untuk auto-assign di Jira:
   - Tipe: `backend`, `frontend`, `db-migration`, `contract`, `infra`, `test`
   - Skill: `golang`, `vue`, `grpc`, `kafka`, dst.

6. **Wajib include:**
   - **Task pertama** (atau subtask pertama di task `setup`): "Setup feature flag + folder evidence/"
   - **Task terakhir** (atau task `cleanup`): "Cleanup feature flag + post-launch retro draft"
   - **Test task PARALLEL**: "Tester Agent generate test plan" dimulai bersamaan dengan task implementation pertama.

7. **Branch naming guidance** (Prinsip 20 — dokumentasikan di header IMPL-PLAN):
   - FLAT: `feat/<slug>-<task-id>` per task
   - HIERARCHICAL: `feat/<slug>-<task-id>-<subtask-id>` per subtask
   - Atau long-lived branch `feat/<slug>` kalau tim prefer merge sekali

## Aturan main

- Estimasi T-shirt, bukan jam.
- Tidak ada subtask >L. Kalau muncul L, pecah.
- Setiap task / subtask punya 1 owner role (`be-dev`, `fe-dev`, `qa`, `sre`).
- Mode (FLAT/HIERARCHICAL) ditentukan sekali di awal — konsisten sepanjang IMPL-PLAN.

## Definition of Done

- `G3-IMPL-PLAN.md` ada, mode di-declare eksplisit di header, task/subtask list lengkap, dependency graph render valid.
- Critical path & paralel-track jelas (test paralel dengan dev).
- Branch naming convention dokumentasikan.
- Output ringkas ke chat: mode (FLAT/HIERARCHICAL), jumlah task (+ subtask kalau hierarchical), critical path length, task red yang perlu spike.
