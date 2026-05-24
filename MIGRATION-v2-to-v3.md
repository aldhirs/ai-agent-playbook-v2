# Migration Guide — v2.x → v3.0

> **TL;DR:** v3.0 collapse 3 gate pra-dev jadi 1 phase. 5+ artefak jadi 2 spec file + wireframe HTML. Feature folder existing tetap valid — finish dengan struktur lama. Feature baru pakai v3.0.

---

## Apakah Saya Harus Migrate?

**TIDAK kalau:**
- Feature aktif Anda sudah lewat Gate 1 (PRD signed) — finish saja dengan v2.x struktur
- Tim Anda baru kuat 1-2 bulan di v2.x — biarkan flow stabil dulu

**YA kalau:**
- Anda mulai feature baru
- Pain points yang trigger v3.0 (proses lama, artefak banyak, FE suffer) match dengan apa yang Anda alami
- Tim Anda sudah comfortable dengan v2.x flow (paham gate, paham evidence)

---

## Quick Mapping v2.x → v3.0

### Workflow

| v2.x | v3.0 |
|---|---|
| Gate 1 Grooming (LGTM-PRD by PM + Designer + FE Dev) | Phase 1 Spec — autonomous, output SPEC-BE.md § 1-2 + SPEC-FE.md § 1 |
| Gate 2 Tech Discussion (LGTM-TD by TL + cross-tribe) | Phase 1 Spec — folded into SPEC-BE.md § 0, 3-11 |
| Gate 3 Planning (TL merge) | Phase 1 Spec — folded (no more separate planning) |
| Gate 4 Development | Phase 2 Dev — `/implement <slug>/be` + `/implement <slug>/fe` |
| Gate 5-7 (Test, Defect, Live) | Phase 3 — **retain unchanged** |

### Approval

| v2.x | v3.0 |
|---|---|
| 3× LGTM (PRD, TD, Plan) | **1× checkpoint** (LGTM-SPEC-BE + LGTM-SPEC-FE) |
| Reviewer per gate berubah | Reviewer per spec consistent (BE: BE Dev + TL; FE: Designer + FE Dev) |

### Artefak

| v2.x | v3.0 |
|---|---|
| `G1-PRD.md` | `SPEC-FE.md § 1-2` + `SPEC-BE.md § 1-2` |
| `G1-UI-IMPACT.md` | `SPEC-FE.md § 0, 3-11` + `wireframes/*.html` |
| `G2-TECH-DESIGN.md` | `SPEC-BE.md § 0, 3-11` |
| `decisions/ADR-NNN.md` | `SPEC-BE.md § 0.3 Justifikasi` (untuk magnitude > EXTEND) |
| `G3-IMPL-PLAN.md` | (no equivalent — task breakdown otomatis 2 chunk BE + FE) |
| `G4-CODE-MANIFEST.md` | PR description (link ke SPEC section per perubahan) |
| `G5-TEST-PLAN.md` | (retain — Phase 3 unchanged) |
| `G7-POST-LAUNCH-RETRO.md` | (retain — Phase 3 unchanged) |
| `contract/openapi.yaml` | (retain — auto-extracted dari SPEC-BE § 4 oleh `/spec`) |
| `migrations/*.sql` | (retain — auto-extracted dari SPEC-BE § 3) |
| `evidence/<task-id>/` | `evidence/be/` atau `evidence/fe/` (per chunk, bukan per task) |

### Commands

| v2.x | v3.0 |
|---|---|
| `/groom <epic> [tribe] [--no-ui]` | `/spec <epic> [tribe] [--no-ui]` (invoke 3 agent sequential) |
| `/tech-design <slug>` | (folded into `/spec`) |
| `/plan <slug>` | (folded into `/spec`) |
| `/implement <slug>/<task-id>` | `/implement <slug>/be` atau `/implement <slug>/fe` |
| `/ui-impact-analysis <slug>` | `/ui-impact-analysis <slug>` (sama, + generate wireframe HTML di Pre-Dev mode) |
| `/code-review`, `/test-plan` | unchanged |

---

## Step-by-Step Migration (untuk Tim yang Mau Adopt v3.0)

### Step 1: Update Project Templates

```bash
cd <your-project>

# Backup v2.x templates (kalau ada feature aktif yang masih pakai)
mv .claude/templates .claude/templates_v2_backup

# Copy v3.0 templates dari playbook
cp -r /path/to/playbook-v2/templates .claude/templates

# Atau dari GitHub release:
# curl -L https://github.com/aldhirs/ai-agent-playbook-v2/archive/v3.0.tar.gz | tar xz
```

### Step 2: Update Commands

```bash
# Replace commands (v2.x archived auto di playbook _archived-v2/)
cp -r /path/to/playbook-v2/commands .claude/commands

# Old commands (groom, tech-design, plan) tetap available di _archived-v2/
# kalau Anda butuh untuk feature lama
```

### Step 3: Update Agents

Agent files sudah backward-compatible — v3.0 Output Targets section di prepend di top, legacy v2.x content tetap di-document untuk reference. Update agents/ folder:

```bash
cp -r /path/to/playbook-v2/agents .claude/agents
```

Customize placeholder (`<your-shared-lib>`, `<your-fe-monorepo>`, dll) sesuai project.

### Step 4: Setup Catalog Design System (kalau belum)

`ui-impact-analyst` butuh catalog komponen di `design-system-context.md` (atau equivalent path):

```bash
# Buat catalog kalau belum ada (sesuaikan dengan design system project Anda)
touch design-system-context.md
```

Format catalog: list komponen (Atomic/Molecule/Organism), props, slots, pitfall mapping (mis. "Figma 'outlined' → variant='secondary'"). Lihat README playbook untuk detail.

### Step 5: Smoke Test dengan Feature Dummy

```bash
# Generate spec untuk feature dummy
/spec my-test-feature

# Verify output:
# - features/my-test-feature/SPEC-BE.md ada, § 0 lengkap, § 4 punya [BE-CONTRACT-FROZEN]
# - features/my-test-feature/SPEC-FE.md ada
# - features/my-test-feature/wireframes/index.html ada
# - Double-click wireframes/index.html, preview di browser

# Kalau ada push-back, lihat features/my-test-feature/OPEN-QUESTIONS.md
```

### Step 6: Update CLAUDE.md Hierarchy

Update `org/.claude/CLAUDE.md` (atau equivalent) § Operating Model + § Workflow:

```markdown
## 1. Operating Model (v3.0)

| Phase | Human Owner | AI Agent | Output |
|---|---|---|---|
| 1. Spec | PM (consulted) | `pm` + `solution-architect` + `ui-impact-analyst` | SPEC-BE.md + SPEC-FE.md + wireframes/*.html |
| 1.5 Checkpoint | BE Dev + TL · Designer + FE Dev | — | LGTM-SPEC-BE + LGTM-SPEC-FE (grep-able) |
| 2. Dev | BE Dev + FE Dev (paralel) | `backend-engineer` + `frontend-engineer` | 2 PR (BE chunk + FE chunk) + evidence |
| 3. Test | QA | `qa-engineer` | Test plan + greenlight |
| 3. Defect | QA + TL | triage | Lesson candidate |
| 3. Live | SRE | `sre` | Runbook + retro |
```

---

## Feature Folder Structure Comparison

### v2.x

```
features/<slug>/
├── G1-PRD.md
├── G1-UI-IMPACT.md
├── G2-TECH-DESIGN.md
├── G3-IMPL-PLAN.md
├── G4-CODE-MANIFEST.md
├── G5-TEST-PLAN.md
├── G7-POST-LAUNCH-RETRO.md
├── decisions/ADR-NNN-<title>.md
├── contract/openapi.yaml
├── migrations/NNN-<name>.{up,down}.sql
├── tests/
├── evidence/<task-id>/{curl.txt, test.log, screenshots/}
└── runbook/
```

### v3.0

```
features/<slug>/
├── SPEC-BE.md           ← consolidates G1-PRD + G2-TECH-DESIGN + G3-IMPL-PLAN (BE parts)
├── SPEC-FE.md           ← consolidates G1-PRD + G1-UI-IMPACT + G3-IMPL-PLAN (FE parts)
├── wireframes/
│   ├── index.html       ← NEW: navigation hub
│   └── *.html           ← NEW: per-page wireframe with 5-state interactive demo
├── OPEN-QUESTIONS.md    ← NEW: push-back signal (created on-demand)
├── G5-TEST-PLAN.md      ← retain (Phase 3 unchanged)
├── G7-POST-LAUNCH-RETRO.md ← retain
├── contract/openapi.yaml ← auto-extracted dari SPEC-BE § 4
├── migrations/*.sql     ← auto-extracted dari SPEC-BE § 3
├── tests/               ← retain
├── evidence/
│   ├── be/{curl.txt, test.log}    ← per chunk (bukan per task)
│   └── fe/{screenshots/, test.log}
└── runbook/             ← retain (Phase 3)
```

---

## Mapping Persona Workflow

### PM
- **v2.x:** Lead Gate 1 (drafting PRD, jawab open questions), consult Gate 2-3
- **v3.0:** Consult Phase 1 (kasih epic input, review push-back). Tidak perlu drafting manual — `pm` agent generate SPEC sections.

### Tech Lead
- **v2.x:** Lead Gate 2 (review tech design), Lead Gate 3 (merge plan)
- **v3.0:** Co-approve SPEC-BE.md (LGTM-SPEC-BE dengan BE Dev). Push-back kalau ada concern di chat → `solution-architect` revise.

### Designer
- **v2.x:** Review G1-UI-IMPACT.md Pre-Dev Section 1
- **v3.0:** Buka `wireframes/index.html` di browser, review preview interactive. Override dengan Figma export kalau perlu. Sign LGTM-SPEC-FE dengan FE Dev.

### BE Dev
- **v2.x:** Implement dari G2-TECH-DESIGN.md + G3-IMPL-PLAN.md per task
- **v3.0:** Implement 1 chunk besar dari SPEC-BE.md. `/implement <slug>/be`.

### FE Dev
- **v2.x:** Implement dari G1-UI-IMPACT.md + G3-IMPL-PLAN.md per task. Wait BE finish dulu untuk integrate.
- **v3.0:** Implement 1 chunk besar dari SPEC-FE.md + wireframe HTML. **Paralel work** allowed begitu SPEC-BE § 4 punya `[BE-CONTRACT-FROZEN]` marker. `/implement <slug>/fe`.

### QA
- **v2.x:** Generate G5-TEST-PLAN.md di Gate 5
- **v3.0:** **Unchanged** — Phase 3 tetap pakai v2.x flow

### SRE
- **v2.x:** Runbook + alert + retro di Gate 7
- **v3.0:** **Unchanged** — Phase 3 tetap pakai v2.x flow

---

## FAQ

**Q1: Apakah `[BE-CONTRACT-FROZEN]` mengunci contract selamanya?**

Tidak. Marker = signal bahwa contract sah untuk paralel work. Kalau ada perubahan setelah frozen, BE Dev harus update SPEC-BE.md § 4 + minta re-LGTM dari FE Dev (re-sign LGTM-SPEC-FE di § 12). FE Dev akan re-evaluate integration.

**Q2: PR size 500 lines hard atau soft?**

Soft. Kalau lebih, `/implement` warning + suggest split. User decide. Untuk feature kompleks, BE chunk bisa di-split jadi 2 PR (mis. schema + DB migration di PR 1, handler + usecase di PR 2).

**Q3: Wireframe HTML jelek, gimana?**

3 opsi:
1. Re-run `/ui-impact-analysis <slug>` (Pre-Dev mode) untuk regenerate dengan instruction lebih spesifik
2. Designer manual edit HTML (Tailwind CDN — straightforward)
3. Designer replace dengan Figma export (PNG) + update SPEC-FE § 2 link

**Q4: Tim saya pakai stack non-Vue/Go. Bisa adopt v3.0?**

Bisa. Template SPEC-BE/SPEC-FE bahasa-agnostic. Customize agent (`backend-engineer.md`, `frontend-engineer.md`) sesuai stack — replace `Go` reference dengan stack Anda, replace `Vue 3 / Nuxt` reference dengan framework FE Anda. Pattern (existing analysis, 5-state, push-back) tetap apply.

**Q5: Feature folder pakai v2.x bisa dilanjut di v3.0?**

Bisa, dengan caveat: jangan campur. Kalau feature sudah lewat Gate 1 di v2.x, finish dengan struktur lama. Untuk feature baru, mulai fresh di v3.0.

**Q6: Push-back round 3 escalate ke mana?**

Sync meeting dengan stakeholder relevant. AI tidak akan auto-revise spec lagi setelah 2 round. Output AI: list pertanyaan yang masih open, suggest agenda meeting.

---

## Rollback ke v2.x

Kalau v3.0 tidak cocok untuk tim Anda, rollback gampang:

```bash
# Revert ke v2.x templates
rm -rf .claude/templates
cp -r .claude/templates_v2_backup .claude/templates

# Revert commands
git checkout HEAD~1 -- .claude/commands/

# Agents sudah backward-compatible (v3.0 prepend doesn't break v2.x usage)
# Tinggal abaikan "v3.0 Output Targets" section di top, ikuti legacy section di bawah
```

---

## Help

- Issue: https://github.com/aldhirs/ai-agent-playbook-v2/issues
- Migration question: open issue dengan label `migration-v2-to-v3`
- Feedback v3.0: open issue dengan label `feedback-v3`
