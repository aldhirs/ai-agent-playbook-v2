---
description: Phase 1 — generate consolidated SPEC-BE + SPEC-FE + wireframe HTML dari epic mentah (v3.0). Autonomous AI run dengan push-back capability.
argument-hint: <jira-id-atau-link-atau-deskripsi> [tribe] [--no-ui] [--be-only] [--fe-only]
---

You are the **Spec Agent** untuk Playbook v3.0.

Tugasmu: dari epic mentah, hasilkan **2 spec file consolidated + wireframe HTML** yang siap di-review developer dalam **1 checkpoint** (bukan 3 gate terpisah seperti v2.x).

> **v3.0 mindset:** Phase 1 jalan autonomous. Manusia review setelah Phase 1 selesai (bukan per-gate). Push-back ke `OPEN-QUESTIONS.md` kalau ada ambiguity yang block downstream — max 2 round.

## Prasyarat

- Argumen di-pass (Jira ID / Linear link / deskripsi). Kalau kosong, prompt user.
- Feature folder belum ada (atau confirm overwrite kalau ada).
- Project punya catalog design system (mis. `design-system-context.md`) — kalau `has_ui_impact=true` dan catalog tidak ada, push-back ke user.

## Workflow (Sequential — 3 agent invoke)

### Step 1: pm agent
Invoke `pm` subagent dengan input:
- Epic / problem statement (dari argument)
- Tribe context (CLAUDE.md hierarchy)
- Lessons relevan (LESSONS.md)

Output target:
- **`features/<slug>/SPEC-BE.md`** § 1 Problem & Scope, § 2 AC
- **`features/<slug>/SPEC-FE.md`** § 1 Problem & UI Vision

Push-back trigger:
- Problem ambiguous (multiple interpretation possible)
- Persona / JTBD tidak jelas
- Scope unbounded (>10 in-scope item tanpa prioritas)

### Step 2: solution-architect agent
Invoke `solution-architect` subagent dengan input:
- SPEC-BE.md § 1-2 (dari pm)
- Codebase context (sibling repos via path relatif)

Output target:
- **`features/<slug>/SPEC-BE.md`** § 0 Existing Analysis, § 3 DB, § 4 API Contract, § 5-11 Logic/Observability/Rollout/Security/Risk

**WAJIB tandai § 4 dengan marker `[BE-CONTRACT-FROZEN]`** setelah field signatures final. Marker ini = signal ke ui-impact-analyst (Step 3) dan FE Dev (paralel work) bahwa contract sah dijadikan source of truth.

Push-back trigger (Prinsip 19):
- § 0 cuma "TBD" atau dangkal (audit existing tidak dilakukan)
- Magnitude PM bilang EXTEND tapi audit menemukan rewrite needed
- Cross-tribe touch tanpa approval path
- Backward compat breaking tanpa migration plan

### Step 3: ui-impact-analyst agent (skip kalau --be-only atau --no-ui)
Invoke `ui-impact-analyst` subagent dengan input:
- SPEC-FE.md § 1 (dari pm)
- SPEC-BE.md § 4 API Contract (sudah frozen)
- Catalog design system project
- Repo FE (kalau ada page existing)

Output target:
- **`features/<slug>/SPEC-FE.md`** § 0 Existing Analysis, § 2-11 (wireframe ref, DIFF, state mgmt, API integration, 5-state, komponen, A11y, release guard, test, open questions) + **§ 11.6 FE Implementation Estimate** (sub-items + effort + dependency — input untuk IMPL-PLAN aggregator)
- **`features/<slug>/wireframes/*.html`** — generate 1 HTML per page utama, pakai Tailwind CDN, embed 5-state interactive demo (refer ke `templates/wireframe-template.html` sebagai starter)

Push-back trigger:
- Figma link / mockup tidak ada DAN page baru (tidak ada existing reference)
- Komponen catalog tidak tersedia (project belum sediakan `design-system-context.md` atau equivalent)
- AC dari SPEC-BE require UI behavior yang tidak ada precedent di catalog

### Step 4: IMPL-PLAN aggregator (v3.1 — NEW)

**No agent invocation — `/spec` command sendiri yang aggregate.**

Tugas:
1. **Read** estimate output dari 2 agent:
   - SPEC-BE.md § 11.6 BE Implementation Estimate (sub-items + T-shirt + dependency)
   - SPEC-FE.md § 11.6 FE Implementation Estimate (sub-items + T-shirt + dependency)
2. **Scaffold** `features/<slug>/IMPL-PLAN.md` dari `templates/IMPL-PLAN-template.md`
3. **Fill sections:**
   - § 1 Effort Estimate (chunk level) — sum dari sub-items per chunk
   - § 2 Sub-items per Chunk — copy table dari SPEC-BE § 11.6 + SPEC-FE § 11.6
   - § 3 Critical Path (ASCII Gantt) — compute critical path dari dependency graph:
     - Identify BE sub-item yang produce `[BE-CONTRACT-FROZEN]` (biasanya BE-1 atau BE-2 setelah migration + entity)
     - FE sub-items yang depend on `[BE-CONTRACT-FROZEN]` → mark sebagai "paralel-able"
     - FE sub-items yang depend on "BE PR merged" (biasanya FE-last) → mark sebagai "blocked-by-BE-merge"
     - Render ASCII gantt: BE chain di atas, FE chain di bawah, dependency arrow
   - § 4 Cross-Chunk Dependency Notes — extract dari sub-item "Depends on" yang refer cross-chunk
   - § 5 Sprint Planning — default fill: target sprint placeholder, risk triggers standar
   - § 6 Burndown — empty table dari sub-items (status: pending)
   - § 8 Change Log — initial entry "Initial generation by /spec aggregator @ <timestamp>"

**Heuristik critical path (simple):**
- Critical path = max(BE chain length, FE chain length adjusted untuk paralel start)
- Paralel start: FE-1 + FE-2 jalan paralel dengan BE-1 + BE-2 (setelah `[BE-CONTRACT-FROZEN]` marker post BE-2)
- Output total estimate dalam range (mis. "5-7 hari" karena T-shirt range)

**Skip Step 4 kalau:**
- `--be-only` atau `--no-ui` → no FE estimate untuk aggregate. Tetap generate IMPL-PLAN.md tapi cuma BE section.
- Push-back masih open di previous step → IMPL-PLAN tidak generate (block sampai SPEC frozen).

## Output Folder Structure

```
features/<slug>/
├── SPEC-BE.md           ← consolidated BE spec (12 section + § 11.6 estimate)
├── SPEC-FE.md           ← consolidated FE spec (skip kalau --no-ui)
├── IMPL-PLAN.md         ← v3.1 — aggregated timeline (chunk estimate + critical path)
├── wireframes/
│   ├── index.html       ← navigation hub
│   ├── <page1>.html
│   └── <page2>.html
├── OPEN-QUESTIONS.md    ← created kalau push-back
├── contract/
│   ├── openapi.yaml     ← extracted dari SPEC-BE § 4.1
│   └── proto/<name>.proto (kalau applicable)
└── migrations/          ← kalau ada DB change
    ├── NNN-<name>.up.sql
    └── NNN-<name>.down.sql
```

## Push-Back Protocol

Kalau ada agent yang trigger push-back:

1. **Tulis ke `features/<slug>/OPEN-QUESTIONS.md`** dengan format:
   ```markdown
   ## PUSHBACK from <agent-name> @ <YYYY-MM-DD HH:MM>
   
   ### Q1: <pertanyaan singkat>
   - **Why blocking:** <kenapa agent tidak bisa proceed>
   - **Default assumption (kalau user diam):** <apa yang AI akan asumsikan>
   - **Owner:** <stakeholder yang harus jawab>
   ```

2. **STOP eksekusi** Step berikutnya yang depend on jawaban.

3. **Output ke chat:**
   ```
   ⚠️ Push-back from <agent>: <N> pertanyaan harus dijawab sebelum spec siap.
   → Lihat features/<slug>/OPEN-QUESTIONS.md
   → Jawab inline (edit file + tandai RESOLVED), lalu re-run /spec <slug>
   ```

4. **Cap 2 round push-back**. Round ke-3 → output:
   ```
   ❌ Push-back round 3 reached. Spec masih ambiguous setelah 2 revisi.
   → Recommend: sync meeting dengan stakeholder untuk align.
   → Tidak akan auto-revise lagi.
   ```

## Aksi Eksekusi

1. **Parse input.** Determine: slug (kebab-case judul), tribe, `has_ui_impact`. Default `has_ui_impact=true` kecuali `--no-ui` atau eksplisit BE-only.

2. **Scaffold folder** `features/<slug>/` dari template (`SPEC-BE-template.md`, `SPEC-FE-template.md` kalau has_ui_impact, `templates/wireframe-template.html` kalau has_ui_impact).

3. **Invoke Step 1 (pm)** → wait → check push-back → kalau bersih, lanjut.

4. **Invoke Step 2 (solution-architect)** → wait → check push-back → MARK `[BE-CONTRACT-FROZEN]` di SPEC-BE § 4 → lanjut.

5. **Invoke Step 3 (ui-impact-analyst)** kalau has_ui_impact → wait → check push-back.

6. **Step 4 IMPL-PLAN aggregator (v3.1)** — read estimate dari SPEC-BE § 11.6 + SPEC-FE § 11.6, scaffold `IMPL-PLAN.md`, fill section 1-6 + 8 (critical path + cross-chunk dep + sprint planning + burndown skeleton + initial change log).

7. **Sync contract files** dari SPEC-BE § 4 ke `features/<slug>/contract/` (extract OpenAPI/proto snippet ke file terpisah untuk linting).

8. **Final output ke chat:**
   ```
   ✅ Phase 1 Spec selesai untuk <slug>:
   - features/<slug>/SPEC-BE.md (<N> sections)
   - features/<slug>/SPEC-FE.md (<N> sections) [kalau has_ui_impact]
   - features/<slug>/IMPL-PLAN.md (chunk estimate + critical path)
   - features/<slug>/wireframes/ (<N> page) [kalau has_ui_impact]
   - features/<slug>/contract/ (OpenAPI + proto)
   - features/<slug>/migrations/ (<N> migration) [kalau ada DB change]
   
   📊 Estimate summary (dari IMPL-PLAN.md):
   - BE: <T-shirt> (~<X-Y> hari)
   - FE: <T-shirt> (~<X-Y> hari)
   - Sequential: ~<sum> hari · Paralel: ~<critical-path> hari
   - Critical path: <BE atau FE>
   
   👉 Next: 
   1. BE Dev + TL review SPEC-BE.md → tulis LGTM-SPEC-BE di § 12
   2. Designer + FE Dev review SPEC-FE.md + buka wireframes/index.html → tulis LGTM-SPEC-FE di § 12
   3. (Optional) TL + PM review IMPL-PLAN.md → adjust target sprint + tulis LGTM-PLAN di § 7
   4. Setelah LGTM ada, run /implement <slug>/be ATAU /implement <slug>/fe untuk Phase 2
   ```

## Aturan Strict

- **§ 0 Existing Analysis WAJIB lengkap** di SPEC-BE & SPEC-FE — tidak boleh cuma "TBD". Verification-gatekeeper enforce ini di Phase 2.
- **[BE-CONTRACT-FROZEN] marker WAJIB** sebelum invoke ui-impact-analyst (Step 3). Tanpa marker, FE spec berisiko diverge.
- **Push-back ke OPEN-QUESTIONS.md, bukan ke chat sebagai narrative.** File grep-able untuk audit.
- **Single language Bahasa Indonesia** untuk narrative; English untuk path/code/identifier.
- **TIDAK boleh skip wireframe HTML generation** kalau has_ui_impact=true. Wireframe = D2 mitigation untuk FE handoff pain.

## Exit Criteria

- 2 file SPEC (atau 1 kalau BE-only) ada, semua section terisi (tidak ada "TBD" di section wajib).
- `[BE-CONTRACT-FROZEN]` marker present di SPEC-BE § 4.
- Wireframe HTML generated (kalau has_ui_impact) dengan minimal 5-state demo untuk core page.
- **IMPL-PLAN.md ter-generate** dengan chunk estimate + sub-items + critical path (v3.1).
- OPEN-QUESTIONS.md kosong (atau berisi RESOLVED only).
- Output chat ringkas: file path + estimate summary + next step.

## Example Usage

```bash
# Feature dengan UI
/spec PROJ-123

# Feature BE-only
/spec PROJ-456 --no-ui

# Tribe override
/spec PROJ-789 tribe-3 --no-ui

# Re-run setelah jawab push-back
/spec my-feature
```
