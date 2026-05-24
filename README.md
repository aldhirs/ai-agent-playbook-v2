# Agent Playbook v3.0 — Team Edition

Workflow AI-augmented untuk tim engineering multi-tribe. **Pangkas lead time 60-70%** di phase pra-development — dengan review & approval tetap di tangan developer manusia di **1 checkpoint** (bukan 3 gate terpisah).

> **Tagline:** *Contract first. Evidence always. AI proposes, human approves. 2 spec files + wireframe HTML — bukan 5 artefak terpisah.*

---

## TL;DR

- **3 phase workflow** (Spec → Dev → Live), bukan 7 gate granular
- **1 checkpoint pra-dev** (review consolidated SPEC-BE + SPEC-FE), bukan 3 approval terpisah
- **Wireframe HTML preview** untuk FE (Tailwind CDN, no build) — eliminate handoff guesswork
- **2 PR per feature** (BE chunk + FE chunk dengan paralel work), bukan task-by-task granular
- **Push-back capability** — agent STOP + tanya kalau ambiguity (cap 2 round)
- **6 prinsip operating + quality** (15–20) tetap dipegang
- **AI proposes, human approves** — approval grep-able di file, bukan Slack

📖 Detail lengkap → **[index.html](index.html)** · 📅 Changelog → **[CHANGELOG.md](CHANGELOG.md)** · 🚚 Migrate v2.x → **[MIGRATION-v2-to-v3.md](MIGRATION-v2-to-v3.md)**

---

## Quick Start (~1 jam)

```bash
# 1. Clone artifact ke project Anda
cp -r commands/ <your-project>/.claude/commands/
cp -r agents/   <your-project>/.claude/agents/
cp -r templates/ <your-project>/templates/

# 2. Setup tribe-level CLAUDE.md (isi 5 do/don't + domain glossary + scope boundary)
cp templates/CLAUDE-tribe.md <your-project>/tribes/<X>/CLAUDE.md

# 3. Provide catalog design system project untuk agent `ui-impact-analyst`
# (sediakan sendiri sesuai stack: design-system-context.md atau equivalent)

# 4. (Optional) Customize agents/ untuk konteks project Anda
# - Replace `<your-shared-lib>` placeholder di backend-engineer.md / solution-architect.md
# - Replace `<your-fe-monorepo>` / `<your-app>` di frontend-engineer.md / ui-impact-analyst.md
# - Untuk domain-expert: copy domain-expert-elearning-payment.md → rename + ganti konten sesuai domain
```

Lalu run **`/spec <epic-id>`** di Claude Code untuk feature pertama. Output: `features/<slug>/SPEC-BE.md` + `SPEC-FE.md` + `wireframes/*.html`. Review 2 file + buka wireframe di browser → tulis LGTM-SPEC. Lalu `/implement <slug>/be` dan `/implement <slug>/fe`.

---

## Read by Role

Pilih persona Anda untuk path bacaan tercepat:

| Saya... | Baca dulu | Skip dulu |
|---|---|---|
| **PM / BA** | [Gate 1 Grooming](phases/1-grooming.html) · [PRD Template](templates/PRD-template.md) | git hygiene, UI catalog |
| **Tech Lead** | [Gate 2 Tech Design](phases/2-tech-design.html) · [Prinsip 19 Existing Analysis](index.html#principles) | Quick Start |
| **Developer** | [Gate 4 Development](phases/4-development.html) · [Prinsip 20 Git Hygiene](index.html#principles) · [/implement](commands/implement.md) | Quick Start |
| **Designer / FE Dev** | [UI-Impact Template](templates/UI-Impact-template.md) · [Gate 1 Pre-Dev](phases/1-grooming.html) | tech design, BE detail |
| **QA Engineer** | [Gate 5 Testing](phases/5-testing.html) · [Test Plan Template](templates/TestPlan-template.md) | grooming, git hygiene |
| **SRE** | [Gate 7 Live](phases/7-live.html) · runbook structure | UI-Impact catalog |

---

## Workflow v3.0 — 3 Phase

```
[Epic mentah]
    ↓
🤖 PHASE 1 — SPEC (AI autonomous, ZERO human approval per gate)
   /spec <epic>
    ├── pm: problem + JTBD + scope + AC + push-back
    ├── solution-architect: existing audit + BE approach + [BE-CONTRACT-FROZEN] marker
    └── ui-impact-analyst: UI DIFF + WIREFRAME HTML + 5-state plan
    
   Output: SPEC-BE.md + SPEC-FE.md + wireframes/*.html
    ↓
👤 CHECKPOINT (1x, bukan 3x)
   BE Dev + TL → LGTM-SPEC-BE
   Designer + FE Dev → LGTM-SPEC-FE (buka wireframe HTML di browser dulu)
    ↓
🤖 PHASE 2 — DEV (paralel BE & FE setelah BE-CONTRACT-FROZEN)
   /implement <slug>/be   (1 PR, soft cap 500 lines)
   /implement <slug>/fe   (1 PR, soft cap 500 lines)
    ↓
👤 PHASE 3 — TEST & LIVE (retain v2.x Gate 5-7)
   QA test plan + greenlight · Defect close + lesson · SRE runbook + retro
```

## Struktur Repo Ini

- **`commands/`** — slash command untuk Claude Code (5 file: `/spec`, `/implement`, `/code-review`, `/test-plan`, `/ui-impact-analysis`)
- **`agents/`** — subagent project-level (11 file) — lihat tabel di bawah
- **`templates/`** — skeleton SPEC-BE, SPEC-FE, wireframe HTML, TestPlan, PUSHBACK, CLAUDE-tribe
- **`templates/_archived-v2/`** — v2.x templates (PRD, TechDesign, UI-Impact) — backward compat reference
- **`commands/_archived-v2/`** — v2.x commands (`/groom`, `/tech-design`, `/plan`) — backward compat reference
- **`phases/`** — detail per phase dalam HTML
- **`index.html`** — playbook lengkap (single-page reference)
- **`MIGRATION-v2-to-v3.md`** — guide untuk migrate dari v2.x ke v3.0

### Subagents Tersedia (11 file di `agents/`)

| Agent | Peran | Gate utama |
|---|---|---|
| `pm` | Product Manager — PRD + tech spec + user stories + JTBD framework | Gate 1 |
| `solution-architect` | System design, ADR, service boundary, integration, magnitude analysis | Gate 2 |
| `backend-engineer` | Go backend impl (REST/gRPC, DB, observability, security) | Gate 4 |
| `frontend-engineer` | Vue 3 / Nuxt impl (komponen, state, performance, a11y) | Gate 4 |
| `product-designer` | UX research, interaction design, prinsip kognitif, design system | Cross-cutting |
| `ui-impact-analyst` | DIFF analyzer untuk UI — catalog-first reuse + 5-state plan | Gate 1 Pre-Dev + Gate 4 Post-Dev |
| `qa-engineer` | Test strategy + automated test + code review + security testing | Gate 5 |
| `security-engineer` | Threat modeling, IDOR detect, secrets, privacy compliance | Gate 4 (wajib payment/PII/legal) |
| `sre` | Infrastructure, observability, runbook, capacity planning, IR | Gate 7 |
| `verification-gatekeeper` | Strict check 3 artefak evidence sebelum klaim "done" | Cross-cutting |
| `domain-expert-elearning-payment` | **Worked example** — domain-expert pattern untuk Indonesian e-learning marketplace + payment aggregator. **Copy + rename + adapt** ke domain project Anda. | Optional, project-specific |

> **Konvensi naming**: `<role>` untuk role generic (`pm`, `qa-engineer`), `<role>-<specialty>` kalau perlu spesialisasi (`backend-engineer` spesifik Go). Untuk **domain-expert**, gunakan suffix domain (mis. `domain-expert-fintech-lending`, `domain-expert-logistics`).
>
> **Genericization note**: 4 agent (`backend-engineer`, `frontend-engineer`, `solution-architect`, `security-engineer`) pakai placeholder `<your-shared-lib>` / `<your-fe-monorepo>` / `<your-app>` — replace dengan path actual project saat adopt. Lihat note di tiap file.

---

## Maintenance

- Owner playbook rotate per kuartal
- Quarterly review 1 jam (TL + PM + QA + Designer)
- Issue / improvement → buka issue di [GitHub repo](https://github.com/aldhirs/ai-agent-playbook-v2)

## Lisensi

Boleh di-copy + di-adapt ke project / organisasi lain. Acknowledgement ke [v1 (SafeTech Agent Playbook)](https://aldhirs.github.io/ai-agent-playbook/) dihargai.
