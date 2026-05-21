# Agent Playbook v2.1 — Team Edition

Workflow AI-augmented untuk tim engineering multi-tribe. **Pangkas lead time 40-60%** di grooming, tech discussion, development, dan testing — dengan review & approval tetap di tangan developer manusia.

> **Tagline:** *Contract first. Evidence always. AI proposes, human approves. Tribe shares, service owns.*

---

## TL;DR

- **6 prinsip operating + quality** (15–20) — apa yang dipegang teguh
- **7 gate workflow** — setiap gate ditutup approval eksplisit di artefak
- **1 feature folder** — semua artefak dari PRD sampai retro di satu tempat
- **7 slash command** — `/groom`, `/tech-design`, `/plan`, `/implement`, `/test-plan`, `/code-review`, `/ui-impact-analysis`
- **AI proposes, human approves** — agent kerja background, manusia decide di gate

📖 Detail lengkap → **[index.html](index.html)** · 📅 Changelog → **[CHANGELOG.md](CHANGELOG.md)**

---

## Quick Start (~1 jam)

```bash
# 1. Clone artifact ke project Anda
cp -r commands/ <your-project>/.claude/commands/
cp -r agents/   <your-project>/.claude/agents/
cp -r templates/ <your-project>/templates/

# 2. Setup tribe-level CLAUDE.md (isi 5 do/don't + domain glossary + scope boundary)
cp templates/CLAUDE-tribe.md <your-project>/tribes/<X>/CLAUDE.md

# 3. Provide catalog design system project untuk agent ui-impact-analyst
# (sediakan sendiri sesuai stack: design-system-context.md atau equivalent)
```

Lalu run `/groom <epic-id>` di Claude Code untuk feature pertama.

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

## Struktur Repo Ini

- **`commands/`** — slash command untuk Claude Code (7 file)
- **`agents/`** — subagent project-level (`ui-impact-analyst`)
- **`templates/`** — skeleton PRD, TechDesign, TestPlan, UI-Impact, PUSHBACK, CLAUDE-tribe
- **`phases/`** — detail per gate (1-7) dalam HTML
- **`index.html`** — playbook lengkap (single-page reference)

---

## Maintenance

- Owner playbook rotate per kuartal
- Quarterly review 1 jam (TL + PM + QA + Designer)
- Issue / improvement → buka issue di [GitHub repo](https://github.com/aldhirs/ai-agent-playbook-v2)

## Lisensi

Boleh di-copy + di-adapt ke project / organisasi lain. Acknowledgement ke [v1 (SafeTech Agent Playbook)](https://aldhirs.github.io/ai-agent-playbook/) dihargai.
