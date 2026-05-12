# CLAUDE.md — Tribe <NAMA_TRIBE>

> Konteks tribe-level. Inherit dari `org/.claude/CLAUDE.md`. Override hanya kalau benar-benar tribe-spesifik. Kalau merasa override >50%, hentikan & bicara di forum tech lead.

## Tribe Mission

(1-2 paragraf. Apa yang dibangun tribe ini, untuk siapa, dan kenapa. No marketing speak.)

Contoh: *"Tribe 3 menangani seluruh alur pembayaran B2C — dari checkout sampai settlement. Tujuannya: payment success rate ≥99.5%, p99 latency ≤800ms, dan zero data leakage untuk PII pembayar."*

## Tribe Owners

- **Tech Lead:** <nama>
- **Product Lead:** <nama>
- **QA Lead:** <nama>
- **Playbook Owner (rotate per kuartal):** <nama>

## Sub-Tribes (kalau ada)

- **3a — <fokus>:** services <list>
- **3b — <fokus>:** services <list>
- **3c — <fokus>:** services <list>

> Catatan: sub-tribe share file ini sebagai base. Hanya tambahkan file `tribes/3/3a/CLAUDE.md` kalau ada fokus yang BENAR-BENAR berbeda (mis. compliance khusus, vendor berbeda).

## Domain Glossary

Istilah yang sering kepake di tribe ini. Tujuan: PM/Dev/QA pakai kata yang sama; agent tidak salah translate.

- **<istilah>** — definisi 1 kalimat
- **<istilah>** — definisi 1 kalimat
- ...

## 5 Do/Don't Tribe (Wajib Diisi)

### Do

1. ...
2. ...
3. ...
4. ...
5. ...

### Don't

1. ...
2. ...
3. ...
4. ...
5. ...

## API & Contract Conventions

- **REST naming:** (mis. `/v1/<resource>`, snake_case body)
- **gRPC package naming:** (mis. `tribe3.payment.v1`)
- **Versioning policy:** (mis. additive-only minor; major bump = new path/package)
- **Authentication:** (mis. JWT via X-Auth-Token; service-to-service pakai mTLS)
- **Error response shape:** lihat `templates/error-shape.md`

## Standar Observability

- **Log:** JSON, field wajib: `request_id`, `tenant_id`, `actor`, `level`, `event`
- **Metric:** prefix tribe: `tribe3_<service>_<metric>`
- **Trace:** propagate `X-Trace-Id` ke seluruh hop
- **Dashboard owner:** <link Grafana folder>

## Lessons (link)

Lessons aktif tribe ini ada di `tribes/<id>/LESSONS.md`. Yang sudah naik ke org-level dicabut dari sini.

## Cross-Tribe Reviewers

Daftar buddy untuk Gate 2 cross-tribe review:

- Tribe 1 → buddy: <nama>
- Tribe 2 → buddy: <nama>
- Tribe 3 internal cross-sub-tribe → buddy: <nama>

## Emergency Lane

Kapan boleh skip gate? Hanya kalau memenuhi SEMUA syarat:

1. Insiden P0/P1 di produksi yang impact langsung user
2. Approval verbal dari Tech Lead + Product Lead
3. **Wajib** retro pasca-fix (≤72 jam) yang menghasilkan lesson + (kalau bisa) automation supaya tidak terulang

Selain itu, gate tidak boleh di-skip.
