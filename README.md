# Agent Playbook v2.1 — Team Edition

Workflow AI-augmented untuk tim engineering multi-tribe yang memangkas waktu di fase **grooming, tech discussion, development, dan testing** — dengan review & approval tetap di tangan developer.

> **Tagline:** *Contract first. Evidence always. AI proposes, human approves. Tribe shares, service owns. Analyze existing first. Sync repo & branch.*

---

## What's New in v2.1 (Changelog)

> Major upgrade berdasarkan lesson dari tim yang adopt v2.0 di project besar dengan design system existing.

| Perubahan | Why |
|---|---|
| **Prinsip 19 — Analyze Existing First** (4 magnitude: NO-CHANGE / EXTEND / NEW-MODULE / REWRITE) | Lesson: solution-architect cenderung over-engineer di Gate 2. Default ke EXTEND. |
| **Prinsip 20 — Sync Repo & Branch dari Slug** (git hygiene 3-step sebelum sentuh code) | Mencegah edit di branch stale + branch naming inkonsisten. |
| **Gate 1 approval 3-party** (PM + Designer + FE Dev, TL consulted) | UI vision harus jadi first-class citizen di Gate 1, bukan afterthought di Gate 2. |
| **UI-IMPACT.md workflow** (Pre-Dev WAJIB Gate 1, Post-Dev Gate 4) + agent `ui-impact-analyst` + command `/ui-impact-analysis` | Sumber feedback dari project frontend besar dengan design system existing. |
| **Gate-based file naming** (`G1-PRD.md`, `G2-TECH-DESIGN.md`, ..., `G7-POST-LAUNCH-RETRO.md`) | Folder feature langsung jelas urutan gate-nya. |
| **Section 0 Existing Analysis** di TechDesign-template + magnitude tag di header | Force solution-architect audit codebase sebelum brainstorm 3 options. |
| **Project-level agents** (`<project>/.claude/agents/`) + default agent `ui-impact-analyst` | Agent shareable lintas tim via git, bukan personal di user-level. |
| **PUSHBACK-TO-PRODUCT-template.md** baru | Pattern untuk adopt PRD eksternal (mis. dari tim product korporat) + push back gap. |

---

## Konteks

Playbook ini turunan dari [Agent Playbook v1](https://aldhirs.github.io/ai-agent-playbook/index.html) yang dirancang untuk *solo-operator*. v2 menambah lapisan untuk pemakaian tim:

1. **Operating Model** — pemetaan peran manusia × agent AI per-fase
2. **7-gate workflow** dengan approval eksplisit
3. **3-layer CLAUDE.md hierarchy** (org → tribe → service)
4. **Memory federation** lintas tribe (lesson promotion bulanan)
5. **(v2.1)** Catalog-aware UI workflow + magnitude-driven design + git hygiene

---

## Apa Isinya

```
playbook-v2/
├── index.html                      ← Playbook utama (single-page site, bisa di-host gh-pages)
├── README.md                       ← Dokumen ini
├── phases/                         ← Detail per gate (1-7)
├── templates/
│   ├── CLAUDE-tribe.md
│   ├── CLAUDE-service-golang.md
│   ├── PRD-template.md             ← v2.1: approval 3-pihak + § 5 UI Vision
│   ├── TechDesign-template.md      ← v2.1: § 0 Existing Analysis + magnitude
│   ├── TestPlan-template.md
│   ├── UI-Impact-template.md       ← NEW v2.1
│   └── PUSHBACK-TO-PRODUCT-template.md  ← NEW v2.1
├── commands/                       ← Slash command, copy ke <project>/.claude/commands/
│   ├── groom.md                    ← v2.1: integrasi ui-impact-analyst
│   ├── tech-design.md              ← v2.1: Step 0 audit codebase
│   ├── plan.md
│   ├── implement.md                ← v2.1: Step 1 git hygiene
│   ├── test-plan.md
│   ├── code-review.md
│   └── ui-impact-analysis.md       ← NEW v2.1
└── agents/                         ← NEW v2.1 — project-level subagents
    └── ui-impact-analyst.md        ← spesifik UI analysis dengan design system catalog
```

---

## 6 Prinsip v2 (Operating + Quality)

| # | Prinsip | Mode kegagalan yang dicegah |
|---|---|---|
| 15 | **AI Proposes, Human Approves** — setiap gate ditutup approval eksplisit di artefak | Keputusan ambigu / "katanya" |
| 16 | **Tribe Shares, Service Owns** — pola dipakai ≥2 service naik ke tribe; ≥2 tribe naik ke org | Reinventing the wheel |
| 17 | **One Folder, All Phases** — satu `features/<slug>/` membawa PRD → evidence | Context drift di Slack/DM |
| 18 | **Async by Default, Sync on Conflict** — agent jalan background, manusia review batched | Rapat tak perlu |
| **19** | **Analyze Existing First, Propose Minimal Change** ⭐ — audit codebase sebelum design, default magnitude EXTEND | Over-engineering di Gate 2 |
| **20** | **Sync Repo & Branch dari Slug** ⭐ — git hygiene 3-step sebelum sentuh code (clean check, pull, checkout `feat/<slug>`) | Edit branch stale + branch naming ad-hoc |

14 prinsip teknis dari v1 (contract-first, no over-claim, no silo, validation by default, dll.) **dipertahankan utuh**.

---

## 7-Gate Workflow

| Gate | Fase | Human Owner | AI Agent | Target durasi |
|---|---|---|---|---|
| 1 | Grooming & PRD + **UI-Impact Pre-Dev** | **PM + Designer + FE Dev** (TL consulted) | `groomer` + `ui-impact-analyst` | ~2-4 jam |
| 2 | Tech Discussion (+ Existing Analysis Section 0) | Tech Lead | `architect` | ~4-6 jam |
| 3 | Planning | Tech Lead + Dev | `planner` | ~1-2 jam |
| 4 | Development (+ git hygiene, **UI-Impact Post-Dev**) | Developer | `coder` + `reviewer` + `ui-impact-analyst` | 40-60% lebih cepat |
| 5 | Testing | QA | `tester` | Paralel dgn dev (bukan blocker) |
| 6 | Defects & Greenlight | QA + Tech Lead | `triage` | sesuai volume defect |
| 7 | Live & Post-launch | SRE / Tech Lead | `reliability` | +T7 retro otomatis |

Tiap gate punya **input → aksi AI → aksi human → exit criteria → artefak**. Tidak lulus = tidak boleh lanjut.

**Gate 1 v2.1:** Approval **3 pihak wajib** (PM + Designer + FE Dev). TL consulted, optional sign. **UI-IMPACT.md Section 1 (Pre-Dev) WAJIB lengkap** sebelum LGTM-PRD — kecuali feature 100% BE-only.

---

## Feature Folder — Struktur Output Kanonik (v2.1)

**Aturan emas:** 1 feature = 1 folder = semua output di sana. Tidak ada artefak tercecer.

```
features/<slug>/
├── README.md                    ← Auto-generated index
├── manifest.yaml                ← target_repos + sync map + has_ui_impact flag
├── G1-PRD.md                    ← Gate 1
├── G1-UI-IMPACT.md              ← Gate 1 (Pre-Dev) + Gate 4 (Post-Dev) + agent analysis
├── G2-TECH-DESIGN.md            ← Gate 2 (dengan Section 0 Existing Analysis)
├── G3-IMPL-PLAN.md              ← Gate 3
├── G4-CODE-MANIFEST.md          ← Gate 4 — daftar file code yang disentuh
├── G5-TEST-PLAN.md              ← Gate 5 (mulai paralel Gate 3)
├── G7-POST-LAUNCH-RETRO.md      ← Gate 7
├── PUSHBACK-TO-PRODUCT.md       ← Opsional — kalau adopt PRD eksternal
├── decisions/                   ← ADR per keputusan teknis
│   └── ADR-001-<name>.md
├── contract/                    ← API contract draft
│   ├── openapi.yaml
│   └── proto/<name>.proto
├── migrations/                  ← DB migration
│   ├── 001-<name>.up.sql
│   └── 001-<name>.down.sql
├── tests/                       ← Test files (Gate 5)
│   ├── integration/
│   ├── e2e/
│   └── exploratory.md
├── runbook/                     ← SRE artifacts (Gate 7)
│   ├── failure-modes.md
│   ├── alerts.yaml
│   └── dashboard.json
└── evidence/                    ← Bukti per task (Gate 4)
    └── <task-id>/
        ├── curl.txt
        ├── test.log
        └── screenshots/
```

**Mapping artefak per gate:**

| Gate | Output | Lokasi |
|---|---|---|
| 1 | PRD + UI Impact Pre-Dev | `G1-PRD.md` + `G1-UI-IMPACT.md` |
| 2 | Tech design + ADR + contract + migration plan | `G2-TECH-DESIGN.md` · `decisions/` · `contract/` · `migrations/` |
| 3 | Impl plan | `G3-IMPL-PLAN.md` |
| 4 | Code + Evidence per task + UI Impact Post-Dev | `evidence/<task-id>/` + `G4-CODE-MANIFEST.md` + `G1-UI-IMPACT.md` Section 2-3 |
| 5 | Test plan + tests | `G5-TEST-PLAN.md` · `tests/` |
| 6 | Regression test, lesson | `tests/` + lesson di `<tribe>/LESSONS.md` |
| 7 | Runbook, alerts, retro | `runbook/` · `G7-POST-LAUNCH-RETRO.md` |

**Code tetap di service layout** (Go butuh layout standar), tapi `G4-CODE-MANIFEST.md` mencatat semua file code yang disentuh — supaya dari 1 folder bisa trace ke semua artefak.

**Build tooling:** `make feature-apply FEATURE=<slug>` sync artefak feature folder ke standard locations (`api/`, `migrations/`, `runbook/`). Source of truth = feature folder. Standard location = view.

Detail lengkap: lihat [Section 5 di index.html](index.html#feature-folder).

---

## Agent Lokasi (v2.1)

| Lokasi | Scope | Pakai untuk |
|---|---|---|
| **Project-level** `<project>/.claude/agents/` | Shareable via git, project-specific knowledge | Tim adopt playbook ini |
| **User-level** `~/.claude/agents/` | Personal, lintas project | Solo experimentation |

Project-level **menang** kalau bentrok dengan user-level.

Default 1 agent ada di playbook ini:
- **`ui-impact-analyst`** — UI analysis dengan design system catalog awareness (Gate 1 Pre-Dev + Gate 4 Post-Dev)

Tim adopt boleh tambah agent sendiri sesuai stack mereka (mis. `backend-engineer`, `qa-engineer`, dll).

---

## Slash Commands

| Command | Gate | Tujuan |
|---|---|---|
| `/groom <input> [tribe] [--no-ui]` | 1 | PRD draft + UI-Impact Pre-Dev (auto via `ui-impact-analyst`) + 5-10 open questions |
| `/tech-design <slug>` | 2 | Section 0 audit existing + 3 options + matrix + ADR + contract |
| `/plan <slug>` | 3 | T-shirt estimate + dependency graph + critical path |
| `/implement <slug>/<task-id>` | 4 | **Git hygiene** + code + test + evidence + self-review |
| `/test-plan <slug>` | 5 | AC mapping + 5-state matrix + 10+ edge case |
| `/code-review <slug>/<task-id>` | 4 | Checklist sebelum PR open |
| **`/ui-impact-analysis <slug> [--mode=...]`** | 1/4/ad-hoc | DIFF existing vs proposed + reuse screening + plan FE |

---

## Rollout 90 Hari

| Minggu | Fase | Aksi | Sukses |
|---|---|---|---|
| 1-4 | **Pilot** (1 tribe) | Setup CLAUDE.md + 7 slash command + design system catalog. Jalan 2-3 feature end-to-end. | 1 feature lulus 7 gate. Lead time -25%. |
| 5-8 | **Replikasi** (3 tribe) | Rollout ke tribe lain (share context). Aktifkan cross-tribe review + lesson promotion bulanan. | Multi-tribe paralel pakai v2.1. 50% Gate 2 punya cross-tribe reviewer. |
| 9-12 | **Skala** (semua tribe) | Semua tribe adopt. Tambah MCP GitHub & Jira. Brownbag bulanan. | Lead time -40%+. Minimal 5 lesson naik ke org-level. |

---

## Quick Start (1 Hari untuk Tribe Baru)

**Pagi (2 jam)**
1. Clone playbook ini ke `<project>/.claude/` (commands + agents + templates).
2. Tech Lead isi `<tribe>/CLAUDE.md`: 5 do/don't + domain glossary + scope boundary.
3. Provide **catalog design system** project (mis. `design-system-context.md`) untuk agent `ui-impact-analyst`.
4. Pilih 2-3 skill yang paling relevan tribe.

**Siang (1 jam)**
5. Walkthrough ke dev tribe: 6 prinsip + 7 gate + demo `/groom` & `/tech-design` di feature antrean.

**Sore (2-3 jam)**
6. Tiap dev coba `/implement` di 1 task kecil (sambil verify Prinsip 20 git hygiene jalan). Catat friction → backlog improvement.

---

## 4 Metrik Wajib (Auto-tracked Weekly)

1. **Lead time** Gate 1→7 (median): target turun **40%** dalam 90 hari.
2. **Defect escape rate** ke produksi: target **stagnan atau turun**.
3. **PR review time** (median): target turun **30%**.
4. **Lesson velocity**: target **≥3 per tribe per bulan**.

> Jangan optimasi 1 metrik di dasar tukar dengan yang lain. Lead time turun + defect escape naik = bukan win.

---

## Approval Mekanisme Konkret

| Gate | Bentuk Approval |
|---|---|
| 1 PRD | Comment `LGTM-PRD` oleh **PM + Designer + FE Dev** (TL consulted, optional sign) di file `G1-PRD.md` di PR + Section 1 di `G1-UI-IMPACT.md` lengkap |
| 2 Tech Design | Comment `LGTM-TD` oleh Tech Lead + cross-tribe reviewer (Section 0 wajib terisi) |
| 3 Plan | Tech Lead merge plan PR; auto-create task Jira/Linear |
| 4 Dev PR | Branch `feat/<slug>` (Prinsip 20) + Peer review approve + CI hijau + folder `evidence/` tidak kosong |
| 5 Test | QA tag rilis `qa-greenlight` |
| 6 Defect | Status closed + lesson di-link kalau pattern |
| 7 Live | SRE checklist hijau + file post-launch retro committed |

Approval di Slack/DM **tidak terhitung**. Harus di artefak yang bisa di-grep.

---

## Anti-pattern Khusus Tim (Daftar Ringkas)

1. *"AI bilang begitu"* sebagai justifikasi — approval gate ada karena ini.
2. Skip gate karena buru-buru — utang dibayar 3× di gate berikutnya.
3. Fork CLAUDE.md tanpa diskusi — kalau override >50%, hentikan.
4. Cross-tribe reviewer hanya "LGTM" — wajib 1 pertanyaan/saran spesifik.
5. Slash command jadi black box — wajib 1-halaman doc per command.
6. Lesson loop mati — kalau 1 bulan tak ada lesson, retro mini wajib.
7. Approval di Slack — tidak terhitung.
8. *"Ini buat senior aja"* — justru junior paling diuntungkan.
9. **(v2.1)** Skip Section 0 Existing Analysis — design ditolak, audit dulu.
10. **(v2.1)** Default magnitude NEW-MODULE / REWRITE tanpa justify — over-engineer.
11. **(v2.1)** Edit langsung di `main` tanpa branch `feat/<slug>` — bypass code review.
12. **(v2.1)** Skip UI-IMPACT Section 1 di Gate 1 — tech design Gate 2 jadi blind ke UI shape.

---

## Cara Pakai Repo Ini

1. **Untuk dibaca:** buka `index.html` di browser atau host di gh-pages.
2. **Untuk diadopsi tim:**
   - Copy `commands/` ke `<project>/.claude/commands/`.
   - Copy `agents/` ke `<project>/.claude/agents/`.
   - Copy `templates/` ke `<project>/templates/`. Adapt seperlunya per tribe.
   - Setup `tribes/X/CLAUDE.md` dari `templates/CLAUDE-tribe.md`.
   - **Provide catalog design system project** (`design-system-context.md` atau equivalent) untuk `ui-impact-analyst`.
3. **Untuk maintenance:** owner playbook rotate per kuartal. Quarterly review 1 jam (tech lead + 1 PM + 1 QA + 1 Designer).

---

## Lisensi

Boleh di-copy + di-adapt ke project / organisasi lain. Acknowledgement ke v1 (SafeTech Agent Playbook) dihargai.
