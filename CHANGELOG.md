# Changelog — Agent Playbook

## v3.0 — 2026-05-24 ⚠️ BREAKING CHANGE

Workflow restructure berdasarkan feedback adopter: **proses pra-dev terlalu lama, artefak terlalu banyak, FE handoff masih suffer**. v3.0 collapse 3 gate pra-dev (PRD, TechDesign, Plan) jadi 1 Phase dengan single checkpoint.

> **Breaking change.** Feature folder yang sudah pakai v2.x (`G1-PRD.md`, `G2-TECH-DESIGN.md`, dll) tetap valid — finish dengan struktur lama. Feature baru pakai v3.0 default. Lihat `MIGRATION-v2-to-v3.md`.

### Why v3.0

3 pain points dari pilot v2.1:

1. **Proses terlalu lama** — 3 approval gate pra-dev (LGTM-PRD, LGTM-TD, LGTM-PLAN) jadi bottleneck. Tim harus run satu-persatu, koordinasi berat.
2. **Plan buram, scan 5+ file** — artefak tersebar di `G1-PRD.md`, `G1-UI-IMPACT.md`, `G2-TECH-DESIGN.md`, `decisions/`, `G3-IMPL-PLAN.md`. Developer harus grep banyak file untuk grasp full picture.
3. **FE handoff suffer** — UI vision di markdown table + Figma link. Designer + FE Dev masih tebak intent, Figma-MCP output meleset.

### Workflow Change

| | v2.x (7-gate) | v3.0 (3-phase) |
|---|---|---|
| Pra-dev approvals | 3× (PRD, TD, Plan) | **1× checkpoint** (split BE+FE LGTM) |
| Artefak pra-dev | 5+ file | **2 file** (SPEC-BE + SPEC-FE) + wireframe HTML |
| UI vision format | Markdown + Figma link | **+ Wireframe HTML interactive** (Tailwind CDN) |
| Task breakdown | Hierarchical task→subtask | **2 chunk** (BE PR + FE PR, soft cap 500 lines) |
| Push-back | Implicit (Open Questions) | **Explicit** (OPEN-QUESTIONS.md, cap 2 round) |
| BE-FE paralel work | Manual coordination | **`[BE-CONTRACT-FROZEN]` marker** = explicit signal |
| Phase 3 (Test/Defect/Live) | Gate 5-7 separate | **Retain Gate 5-7** (QA + SRE own this) |

### Added

- **`commands/spec.md`** — single command yang invoke `pm` + `solution-architect` + `ui-impact-analyst` sequential. Output consolidated.
- **`templates/SPEC-BE-template.md`** — consolidated BE spec (12 section: existing analysis, problem, AC, DB, contract, logic, observability, security, rollout, risk, open Q, approval).
- **`templates/SPEC-FE-template.md`** — consolidated FE spec (12 section: existing analysis, problem, wireframe preview, DIFF, state mgmt, API integration, 5-state per page, komponen, A11y, release guard, test, approval).
- **`templates/wireframe-template.html`** — Tailwind CDN starter dengan 3-page demo + 5-state interactive button.
- **`[BE-CONTRACT-FROZEN]` marker convention** — di SPEC-BE.md § 4. Signal eksplisit untuk FE Dev paralel work.
- **OPEN-QUESTIONS.md push-back protocol** — agent STOP + tulis pertanyaan kalau ambiguity. Cap 2 round, round 3 escalate.
- **v3.0 Output Targets** section di 5 agent (pm, solution-architect, ui-impact-analyst, backend-engineer, frontend-engineer) — override legacy output destinations ke SPEC-BE/SPEC-FE consolidated.
- **v3.0 Additional Checks** di `verification-gatekeeper.md` — verify § 0 lengkap, `[BE-CONTRACT-FROZEN]` marker present, wireframe HTML exists, LGTM grep-able.

### Changed

- **`commands/implement.md`** — argument changed dari `<slug>/<task-id>` ke `<slug>/be` atau `<slug>/fe`. Output: 1 PR per chunk (soft cap 500 lines, split kalau lebih).
- **`commands/ui-impact-analysis.md`** — Pre-Dev mode WAJIB generate wireframe HTML (bukan optional). Post-Dev mode compare wireframe vs actual screenshots.
- **README.md** — workflow section restructure + new Quick Start dengan `/spec`.

### Deprecated (moved to `_archived-v2/`)

- `templates/PRD-template.md` → replaced by `SPEC-FE.md § 1-2` + `SPEC-BE.md § 1-2`
- `templates/TechDesign-template.md` → replaced by `SPEC-BE.md § 3-9`
- `templates/UI-Impact-template.md` → replaced by `SPEC-FE.md § 0-8` + `wireframe-template.html`
- `commands/groom.md` → replaced by `/spec`
- `commands/tech-design.md` → folded into `/spec`
- `commands/plan.md` → folded into `/spec` (no more separate planning phase)

### Retained

- Prinsip 19 (Analyze Existing First) — § 0 WAJIB di SPEC-BE/FE
- Prinsip 20 (Git Hygiene) — Phase 2 tetap apply 3-step
- AI Proposes, Human Approves — approval grep-able di file (`LGTM-SPEC-BE`, `LGTM-SPEC-FE`)
- Evidence Gate — 3 artefak (curl.txt, test.log, screenshots/) per PR
- Phase 3 (Test/Defect/Live) — retain Gate 5-7 dengan QA + SRE owner
- 11 subagent (file count sama)
- `PUSHBACK-TO-PRODUCT-template.md` — untuk adopt PRD eksternal (masih relevan)
- `code-review.md`, `test-plan.md` commands — unchanged

### Decision Log (D1-D8)

8 keputusan yang membentuk v3.0:

- **D1:** Phase 3 (Gate 5-7) tetap multi-gate, BUKAN di-simplify (test/defect/live punya stakeholder beda)
- **D2:** LGTM dipisah BE+FE, ada `[BE-CONTRACT-FROZEN]` marker untuk paralel work
- **D3:** Wireframe = AI generate baseline, Designer override boleh di Phase 2
- **D4:** Naming `SPEC-BE.md` + `SPEC-FE.md` (alphabetical, single-purpose)
- **D5:** Backward compat — feature folder existing tetap pakai v2.x scheme
- **D6:** Push-back max 2 round, round 3 escalate ke meeting
- **D7:** PR size soft cap 500 lines (warning + suggest split, bukan hard block)
- **D8:** Push-back trigger 4 condition (requirement vs ADR, magnitude ambiguous, AC contradictory, Figma missing)

---

## v2.1.1 — 2026-05-22

Sync release. Bring playbook up to date dengan adopter project yang sudah jalan v2.1 di pilot.

### Added

- **10 subagent file baru** di `agents/` (sebelumnya cuma `ui-impact-analyst`):
  - `pm` — Product Manager (Gate 1 grooming)
  - `solution-architect` — System design + ADR (Gate 2)
  - `backend-engineer` — Go backend impl (Gate 4)
  - `frontend-engineer` — Vue 3 / Nuxt impl (Gate 4)
  - `product-designer` — UX research + design system principles (cross-cutting)
  - `qa-engineer` — Test strategy + automated test + code review (Gate 5)
  - `security-engineer` — Threat modeling + IDOR + privacy (Gate 4 mandatory untuk PII/payment)
  - `sre` — Infrastructure + runbook + observability (Gate 7)
  - `verification-gatekeeper` — Strict check 3 artefak evidence (cross-cutting)
  - `domain-expert-elearning-payment` — **Worked example** untuk domain-expert pattern (Indonesian e-learning + payment). Copy + rename + adapt ke domain Anda.
- **Conceptual role → subagent file mapping** di `index.html` § 2 Operating Model (tabel baru).
- **Subagents tabel** di `README.md` § Struktur Repo Ini dengan peran + gate utama per agent.
- **Genericization placeholder convention** di subagent: `<your-shared-lib>`, `<your-fe-monorepo>`, `<your-app>` — replace saat adopt ke project.

### Changed

- **`agents/ui-impact-analyst.md`** di-upgrade dari versi short (132 lines) ke versi full (198 lines) dengan: detail Mode A/B/C output spec, 10 strict rules, 5-state plan template, pitfall awareness — sebelumnya skeleton, sekarang ready-to-use.
- **`commands/tech-design.md`**: tambah Prinsip 19 (v2.1) note di Prasyarat + `G1-UI-IMPACT.md` sebagai konteks wajib baca #2.
- **`templates/TechDesign-template.md`**: tambah magnitude-aware options guidance setelah `## 3. Options Considered` (EXTEND fokus extend variation, NEW-MODULE/REWRITE wajib include 1 "minimal extend" control).

### Notes

- Subagents `domain-expert-*` dipublish sebagai **worked example**, bukan template kosong. Pola: copy → rename ke domain Anda → ganti konten regulasi + istilah industri + anti-pattern komunikasi.
- Genericization tidak universal: agent yang generic by nature (`pm`, `product-designer`, `qa-engineer`, `sre`, `verification-gatekeeper`) di-copy verbatim. Agent yang heavy SID-specific di-strip ke placeholder.

---

## v2.1 — 2026-05-21

Major upgrade berdasarkan lesson dari tim yang adopt v2.0 di project besar dengan design system existing.

### Added

- **Prinsip 19** — Analyze Existing First, Propose Minimal Change. 4 magnitude: NO-CHANGE / EXTEND (default) / NEW-MODULE / REWRITE.
- **Prinsip 20** — Sync Repo & Branch dari Slug. Git hygiene 3-step sebelum sentuh code (clean check, pull `--ff-only`, checkout `feat/<slug>`).
- **UI-Impact workflow** — `UI-IMPACT.md` WAJIB di Gate 1 (Pre-Dev). Agent `ui-impact-analyst` baru dengan design-system catalog awareness.
- **Hierarchical task → subtask mode** di Gate 3-4 (opsional, heuristik > 5 unit atomic). Default tetap FLAT untuk backward compat.
- **`PUSHBACK-TO-PRODUCT-template.md`** untuk adopt PRD eksternal (mis. dari tim product korporat).
- **Gate-based file naming** — `G1-PRD.md`, `G2-TECH-DESIGN.md`, `G3-IMPL-PLAN.md`, `G4-CODE-MANIFEST.md`, `G5-TEST-PLAN.md`, `G7-POST-LAUNCH-RETRO.md`.
- **Project-level agents** convention — `<project>/.claude/agents/` (shareable via git, menang vs user-level kalau bentrok).
- New command: `/ui-impact-analysis <slug> [--mode=pre-dev|post-dev|quick-diff]`.

### Changed

- **Gate 1 approval**: PM + Tech Lead → **PM + Designer + FE Dev** (TL consulted, optional sign).
- **TechDesign-template**: tambah § 0 Existing Analysis (codebase audit table + magnitude pick + justification + reuse plan).
- **PRD-template**: tambah § 5 UI Vision (link ke G1-UI-IMPACT.md) + approval section 3-pihak.
- **`/groom`**: auto-invoke `ui-impact-analyst` Mode A untuk drafting Section 1 UI-Impact Pre-Dev.
- **`/tech-design`**: Step 2 wajib audit codebase sebelum brainstorm 3 options. Refuse generate kalau § 0 kosong.
- **`/implement`**: Step 1 wajib git hygiene + support hierarchical `<slug>/<task>/<subtask>` argument.
- **`/plan`**: heuristik FLAT vs HIERARCHICAL mode berdasarkan jumlah unit atomic.

### Why

Lesson dari trial pertama:

- `solution-architect` over-engineer di Gate 2 (default loncat ke REWRITE/NEW-MODULE) → **Prinsip 19** force audit existing first
- Git hygiene sering di-skip → branch stale, edit di main langsung → **Prinsip 20** wajibkan flow eksplisit
- UI vision jadi afterthought di Gate 2 → tech design blind ke UI shape → **Pre-Dev di Gate 1** fix this
- Flat `task-001..task-N` tidak informatif untuk feature besar (>5 unit) → **hierarchical task→subtask** untuk semantic grouping
- PRD korporat datang dari luar tim implementasi → **PUSHBACK template** dokumentasikan gap + feedback loop

---

## v2.0 — Initial Team Edition

Turunan dari [Agent Playbook v1](https://aldhirs.github.io/ai-agent-playbook/index.html) (solo-operator). v2.0 menambahkan:

### Added

- **Operating Model** — 5 human role × 5 AI agent counterpart
- **7-gate workflow** dengan approval eksplisit per gate
- **3-layer CLAUDE.md hierarchy** (org → tribe → service)
- **Memory federation** lintas tribe (lesson promotion bulanan)
- **4 prinsip operating** (15-18): AI Proposes Human Approves · Tribe Shares Service Owns · One Folder All Phases · Async by Default Sync on Conflict
- **6 slash command** baseline (groom, tech-design, plan, implement, test-plan, code-review)
- **Feature Folder Structure** — 1 feature = 1 folder = semua artefak di sana
- **Evidence Gate** — 3 artifact wajib (curl.txt + test.log + screenshots/) per task
- **`make feature-apply`** tooling untuk sync feature folder → standard locations

### Retained from v1

- 14 principles teknis (contract first, no over-claim, validation by default, dll.)
- Evidence Gate concept
- Lessons Loop (promotion bulanan)
- 14 service-level CLAUDE.md per service quirk
