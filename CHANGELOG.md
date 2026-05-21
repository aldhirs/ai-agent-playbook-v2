# Changelog — Agent Playbook v2

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
