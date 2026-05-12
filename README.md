# Agent Playbook v2 — Team Edition

Workflow AI-augmented untuk tim engineering multi-tribe yang memangkas waktu di fase **grooming, tech discussion, development, dan testing** — dengan review & approval tetap di tangan developer.

> **Tagline:** *Contract first. Evidence always. AI proposes, human approves. Tribe shares, service owns.*

---

## Konteks

Playbook ini turunan dari [Agent Playbook v1](https://aldhirs.github.io/ai-agent-playbook/index.html) yang dirancang untuk *solo-operator*. v2 menambah 4 lapisan untuk pemakaian tim:

1. **Operating Model** — pemetaan 5 peran manusia × 5 agent AI
2. **7-gate workflow** dengan approval eksplisit
3. **3-layer CLAUDE.md hierarchy** (org → tribe → service)
4. **Memory federation** lintas tribe (lesson promotion bulanan)

---

## Apa Isinya

```
playbook-v2/
├── index.html              ← Playbook utama (single-page site, bisa di-host gh-pages)
├── README.md               ← Dokumen ini
├── playbook.md             ← Versi markdown lengkap (sumber kebenaran)
├── templates/
│   ├── CLAUDE-tribe.md
│   ├── CLAUDE-service-golang.md
│   ├── PRD-template.md
│   ├── TechDesign-template.md
│   └── TestPlan-template.md
└── commands/               ← Slash command, copy ke org/.claude/commands/
    ├── groom.md
    ├── tech-design.md
    ├── plan.md
    ├── implement.md
    ├── test-plan.md
    └── code-review.md
```

---

## 4 Prinsip Baru di v2 (Tambahan)

| # | Prinsip | Mode kegagalan yang dicegah |
|---|---|---|
| 15 | **AI Proposes, Human Approves** — setiap gate ditutup approval eksplisit di artefak | Keputusan ambigu / "katanya" |
| 16 | **Tribe Shares, Service Owns** — pola dipakai ≥2 service naik ke tribe; ≥2 tribe naik ke org | Reinventing the wheel |
| 17 | **One Folder, All Phases** — satu `features/<slug>/` membawa PRD → evidence | Context drift di Slack/DM |
| 18 | **Async by Default, Sync on Conflict** — agent jalan background, manusia review batched | Rapat tak perlu |

14 prinsip teknis dari v1 (contract-first, no over-claim, no silo, validation by default, dll.) **dipertahankan utuh**.

---

## 7-Gate Workflow

| Gate | Fase | Human Owner | AI Agent | Target durasi |
|---|---|---|---|---|
| 1 | Grooming & PRD | PM / BA | Groomer | ~2-4 jam (vs 2-3 hari) |
| 2 | Tech Discussion | Tech Lead | Architect | ~4-6 jam (vs 2-3 hari) |
| 3 | Planning | Tech Lead + Dev | Planner | ~1-2 jam |
| 4 | Development | Developer | Coder + Reviewer | 40-60% lebih cepat |
| 5 | Testing | QA | Tester | Paralel dgn dev (bukan blocker) |
| 6 | Defects & Greenlight | QA + Tech Lead | Triage | sesuai volume defect |
| 7 | Live & Post-launch | SRE / Tech Lead | Reliability | +T7 retro otomatis |

Tiap gate punya **input → aksi AI → aksi human → exit criteria → artefak**. Tidak lulus = tidak boleh lanjut. Tidak ada "skip karena buru-buru" (ada *emergency lane* dengan retro wajib).

---

## Tribe Architecture (3-Layer)

```
org/.claude/CLAUDE.md          ← 14+4 principles, dipakai semua
   └─ tribes/3/CLAUDE.md       ← override domain (payment, regulasi), dipakai 3a/3b/3c
        └─ tribes/3/3a/CLAUDE.md     ← fokus spesifik 3a
              └─ services/X/CLAUDE.md ← quirk service
```

Claude Code otomatis akumulasi dari leaf → root. **Tribe 3a/3b/3c share `tribes/3/CLAUDE.md`** — satu sumber kebenaran, bukan tiga.

---

## Feature Folder — Struktur Output Kanonik

**Aturan emas:** 1 feature = 1 folder = semua output di sana. Tidak ada artefak yang tercecer.

```
features/<slug>/
├── README.md                  ← Auto-generated index
├── PRD.md                     ← Gate 1
├── TECH-DESIGN.md             ← Gate 2
├── IMPL-PLAN.md               ← Gate 3
├── TEST-PLAN.md               ← Gate 5 (mulai Gate 3)
├── POST-LAUNCH-RETRO.md       ← Gate 7 (T+7)
├── decisions/                 ← ADR per keputusan
│   └── ADR-001-<name>.md
├── contract/                  ← API contract
│   ├── openapi.yaml
│   └── proto/<name>.proto
├── migrations/                ← DB migration
│   ├── 001-<name>.up.sql
│   └── 001-<name>.down.sql
├── tests/                     ← Test files
│   ├── integration/
│   ├── e2e/
│   └── exploratory.md
├── runbook/                   ← SRE artifacts (Gate 7)
│   ├── failure-modes.md
│   ├── alerts.yaml
│   └── dashboard.json
├── evidence/                  ← Bukti per task (Gate 4)
│   └── <task-id>/
│       ├── curl.txt
│       ├── test.log
│       └── screenshots/
└── code-manifest.md           ← Pointer ke file CODE di service layout
```

**Mapping artefak per gate:**

| Gate | Output | Lokasi |
|---|---|---|
| 1 | PRD | `PRD.md` |
| 2 | Tech design + ADR + contract + migration plan | `TECH-DESIGN.md` · `decisions/` · `contract/` · `migrations/` |
| 3 | Impl plan | `IMPL-PLAN.md` |
| 4 | Evidence per task | `evidence/<task-id>/` + update `code-manifest.md` |
| 5 | Test plan + tests | `TEST-PLAN.md` · `tests/` |
| 6 | Regression test, lesson | `tests/` + lesson di `tribes/<id>/LESSONS.md` |
| 7 | Runbook, alerts, retro | `runbook/` · `POST-LAUNCH-RETRO.md` |

**Code tetap di service layout** (Go butuh layout standar), tapi `code-manifest.md` di feature folder mencatat semua file code yang disentuh — supaya dari 1 folder bisa trace ke semua artefak.

**Build tooling:** `make feature-apply FEATURE=<slug>` sync artefak feature folder ke standard locations yang dibutuhkan tooling generic (api/, migrations/, monitoring/, runbooks/). Source of truth = feature folder. Standard location = view.

Detail lengkap: lihat [Section 5 di index.html](index.html#feature-folder).

---

## Rollout 90 Hari

| Minggu | Fase | Aksi | Sukses |
|---|---|---|---|
| 1-4 | **Pilot** (1 tribe) | Mulai di **tribe 3a**. Setup CLAUDE.md + 6 slash command + 2-3 skill. Jalan 2-3 feature end-to-end. | 1 feature lulus 7 gate. Lead time -25%. |
| 5-8 | **Replikasi** (3 tribe) | Rollout ke 3b, 3c (share context). Aktifkan cross-tribe review + lesson promotion bulanan. | Tribe 3 paralel pakai v2. 50% Gate 2 punya cross-tribe reviewer. |
| 9-12 | **Skala** (semua tribe) | Tribe 1 & 2. Tambah MCP GitHub & Jira. Brownbag bulanan. | Lead time -40%+. Minimal 5 lesson naik ke org-level. |

---

## Quick Start (1 Hari untuk Tribe Baru)

**Pagi (2 jam)**
1. Clone repo template playbook ini.
2. Tech Lead isi `tribes/X/CLAUDE.md`: 5 do/don't tribe + domain glossary.
3. Copy `commands/` ke `org/.claude/commands/`.
4. Pilih 2-3 skill dari `org/.claude/skills/` yang paling relevan tribe.

**Siang (1 jam)**
5. Walkthrough ke seluruh dev tribe: 4 prinsip baru + 7 gate + demo `/groom` & `/tech-design` di feature antrean.

**Sore (2-3 jam)**
6. Tiap dev coba `/implement` di 1 task kecil. Catat friction → backlog improvement.

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
| 1 PRD | Comment `LGTM-PRD` oleh PM + Tech Lead di file PRD di PR |
| 2 Tech Design | Comment `LGTM-TD` oleh Tech Lead + cross-tribe reviewer |
| 3 Plan | Tech Lead merge plan PR; auto-create task Jira/Linear |
| 4 Dev PR | Peer review approve + CI hijau + folder `evidence/` tidak kosong |
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

---

## Cara Pakai Repo Ini

1. **Untuk dibaca:** buka `index.html` di browser atau host di gh-pages.
2. **Untuk diadopsi tim:**
   - Copy `commands/` ke `org/.claude/commands/` di monorepo / shared `.claude` repo.
   - Copy `templates/` ke `org/templates/`. Adapt seperlunya per tribe.
   - Setup `tribes/X/CLAUDE.md` dari `templates/CLAUDE-tribe.md`.
3. **Untuk maintenance:** owner playbook rotate per kuartal. Quarterly review 1 jam (tech lead + 1 PM + 1 QA).

---

## Lisensi

Boleh di-copy + di-adapt ke project / organisasi lain. Acknowledgement ke v1 (SafeTech Agent Playbook) dihargai.
