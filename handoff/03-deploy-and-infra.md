# 03 · Deploy และ infra — อะไรขึ้นอยู่ที่ไหน (ณ 2026-11-12)

> **[VPS-luffy]** = luffy ตรวจบน VPS แบบอ่านอย่างเดียวเมื่อ 2026-11-12 (franky ไม่ได้เข้า VPS เอง · ผู้ตรวจภายนอกเข้า VPS ไม่ได้ ข้อเหล่านี้ตรวจได้แค่เทียบกับไฟล์ใน repo ว่าสอดคล้องกันหรือไม่)
> **ไม่มีค่า secret ในไฟล์นี้** — มีแค่ชื่อไฟล์และชื่อตัวแปร
> กติกา deploy ของโปรเจกต์นี้: VPS ของเราเองเท่านั้น ไม่ใช้ GitHub Pages/Vercel สำหรับตัวแอป (`classtracking/CLAUDE.md` §Deploy) · หน้าสรุปเป็นเอกสาร ไม่ใช่แอป จึงอยู่บน GitHub Pages ได้

---

## 1. เครื่อง
| ประเด็น | ค่า | ป้าย |
|---|---|---|
| host | Hostinger VPS `[IP ปิดไว้ในฉบับเว็บ]` | [VPS-luffy] |
| user ที่ deploy | `fox` (ไม่มี sudo · เข้า root ด้วย key แยก ตาม memory `vps-root-access.md`) | [VPS-luffy] |
| โฟลเดอร์โปรเจกต์ | `/home/fox/classtracking` — มี `app`, `stg/compose.stg.yml` + `stg/ctstg-env-check.sh`, `backups`, `richmenu`, `healthcheck`, `deploy-history.log` และของ v1 ที่ค้าง: `api` `web` `db` `infra` `schema.sql` | [VPS-luffy] |
| secret ของ prod | `[path ไฟล์ secret ปิดไว้ในฉบับเว็บ]` (แม่แบบ = `V3:ops/classtracking.env.template`) | [repo] `classtracking/CLAUDE.md` · [VPS-luffy] |
| secret ของ staging | `[path ไฟล์ secret ปิดไว้ในฉบับเว็บ]` (แม่แบบ `V3:ops/classtracking-stg.env.template`) | [repo] BOARD W-22 · `V3:docs/DEPLOY.md:505` |

ชื่อตัวแปรใน template ของ prod (ชื่อเท่านั้น · `V3:ops/classtracking.env.template`): `ADMIN_EMAILS` `ADMIN_PROMPTPAY_WEB_ENABLED` `ADMIN_STEPUP_MAX_AGE_SECONDS` `CF_ACCESS_AUD` `CF_ACCESS_STEPUP_AUD` `CF_ACCESS_TEAM_DOMAIN` `CT_DB_APP_PASSWORD` `CT_DB_MIGRATOR_PASSWORD` `CT_DB_NAME` `CT_DB_SUPERUSER_PASSWORD` `CT_DB_WORKER_PASSWORD` `EXPIRY_REMINDERS_ENABLED` `LAUNCH_MODE` `LIFF_ID` `LINE_ADMIN_USER_IDS` `LINE_BOT_USER_ID` `LINE_GUARD_P3_PCT` `LINE_GUARD_RESERVE` `LINE_LOGIN_CHANNEL_ID` `LINE_MESSAGING_CHANNEL_SECRET` `LINE_MESSAGING_TOKEN` `LINE_MONTHLY_QUOTA` `LINE_OA_ID` `LINE_POSTBACK_KEY` `LINE_POSTBACK_KEY_PREV` `LINE_REMINDER_STYLE` `LINE_SUBJECT_ENC_KEY` `LINE_SUBJECT_HMAC_KEY` `MAX_HOUSEHOLDS` `NOTIFY_PROVIDER` `PUBLIC_BASE_URL` `PUSH_ENABLED` `STAGING_ALLOWLIST` `TELEGRAM_ADMIN_CHAT_IDS` `TELEGRAM_BOT_TOKEN` **[repo]** (template นี้มาจาก trunk v3 · ไฟล์จริงบน VPS เป็นของ v2 0025b อาจมีชุดตัวแปรต่างไป **[ยังไม่ยืนยัน]**)

## 2. container ที่รันอยู่ (v2 = ระบบ LIVE)
| container | image | พอร์ต | สถานะ | ป้าย |
|---|---|---|---|---|
| `ctnext-app` | `ctnext-app:944f273d30c1` | `127.0.0.1:8006→3000` | healthy · up 3 วัน | [VPS-luffy] |
| `ctnext-worker` | `ctnext-app:944f273d30c1` | — | รัน | [VPS-luffy] |
| `ctnext-db` | `ctnext-db:16` | (ภายใน) | healthy · up 9 วัน | [VPS-luffy] |
| `ctnext-admin` | — | (`127.0.0.1:8010` ตาม compose) | **ไม่ได้รัน** | [VPS-luffy] · compose profile `admin` `V3:docker/compose.yml:196-226` [repo] |
| ctstg (staging) | — | `:8012` | **ไม่มี container รัน** | [VPS-luffy] |

- compose project `ctnext` · volume `ctnext_pgdata` · ชื่อ `classtracking*` สงวนให้ v1 (`classtracking/CLAUDE.md` §ระบบที่ LIVE)
- release ที่ LIVE = **0025b** · REVIEWED_SHA `944f273d` · tag git `ct-v2-live-0025b` = commit `16ad44a5` (`V3:docs/DEPLOY.md:3831` [repo] · `git rev-parse ct-v2-live-0025b` = `16ad44a5` [repo])
- ฐาน ctnext มี **26 migration (v2 0000–0025)** · v3 **ยังไม่ deploy** [VPS-luffy]
- `LAUNCH_MODE=staging` = ช่วงทดลองของแอดมิน มี 1 ครัวเรือน [VPS-luffy]
- `deploy-history.log` รายการล่าสุด: `944f273d30c1` 2026-11-08T22:26 / 22:28 ok [VPS-luffy]

## 3. Cloudflare tunnel
service `classtracking-tunnel.service` (user `fox`) · config `/home/fox/.cloudflared/classtracking.yml` · tunnel id `[tunnel id ปิดไว้]` (ตัดท้าย) [VPS-luffy] · แม่แบบ unit `V3:ops/classtracking-tunnel.service:14` / config `V3:ops/classtracking.yml.template` [repo]

| hostname | ไปที่ | หมายเหตุ |
|---|---|---|
| `class.foxxy.cloud` | `localhost:8006` | แอปจริง (v2) · cutover v1→v2 เมื่อ 2026-10-29 20:03 (`classtracking/CLAUDE.md`) |
| `ct-admin.foxxy.cloud` | `localhost:8010` | อยู่หลัง Cloudflare Access · container ไม่ได้รัน |
| `class-stg.foxxy.cloud` | `localhost:8012` | ctstg · Access app "only-kkdp13" · DNS + ingress พร้อมเมื่อ 11-11 (`classtracking/research/ctstg-infra-log-2026-11-11.md`) · container ยังไม่ขึ้น |
| (catch-all) | 404 | |

**tunnel อื่นบน VPS เดียวกัน (ไม่ใช่ของ classtracking):** `fox-tunnel` (ร้าน :8787) · picklewhale :8005 · sites :8007 · venitastock :8004 [VPS-luffy] · ใน config ของ `fox-tunnel` ยังมีกฎเก่า `class.foxxy.cloud → 8003` ค้างอยู่ (ไม่มี traffic เพราะ DNS ชี้ tunnel `classtracking` แล้ว · ถ้าจะล้างต้อง restart tunnel ของร้าน ซึ่งต้องขออนุมัติก่อน) [VPS-luffy] · `classtracking/CLAUDE.md` §ระบบเก่า v1

## 4. Backup / healthcheck / restore drill
| ของ | กำหนดเวลา | ที่มา |
|---|---|---|
| backup รายวัน `ctnext-backup.timer` → `/home/fox/bin/ctnext-backup.sh` | `04:20 Asia/Bangkok` (= 21:20 UTC · ตรงกับชื่อไฟล์ `ctnext-YYYYMMDDT212022Z`) | `V3:ops/ctnext-backup.timer:9` · `.service:11` [repo] |
| healthcheck | ทุก 5 นาที (`*:0/5`) | `V3:ops/ctnext-healthcheck.timer:9` [repo] |
| restore drill | วันที่ 2 ของเดือน 10:30 | `V3:ops/ctnext-restore-drill.timer:8` [repo] |
| off-site | restic ไป S3-compatible (template ยกตัวอย่าง Backblaze B2) | `V3:ops/classtracking-backup.env.template:9-16` [repo] · ผู้ให้บริการจริง **[ยังไม่ยืนยัน]** |

ใน `/home/fox/classtracking/backups` [VPS-luffy]: dump รายวัน `ctnext-YYYYMMDDT212022Z` (ล่าสุด 20261111) · `legacy-final-20261029-200314.dump` (dump สุดท้ายของ v1 · **มีข้อมูลส่วนบุคคล** · ต้องลบตามกำหนด retire) · `erasures.log` · `restic-LAST-SNAPSHOT`
⚠️ แผนฐานใหม่: หลังล้างฐาน backup จะเตือน ALERT เรื่อง `anchors.log`/`erasures.log` → ต้อง archive (เปลี่ยนชื่อ) ในวัน cutover (`classtracking/research/nami-cutover-fresh-db-plan-2026-11-12.md:5` · อ้าง `ops/ctnext-backup.sh:447-448`)

## 5. ของ v1 (หยุดให้บริการแล้ว แต่ยังไม่ retire)
- `classtracking_auth` / `_web` / `_api` = **Exited** มา 13 วัน · `classtracking_db` (supabase/postgres 15.8.1.060) **ยังรัน** ที่ `127.0.0.1:5433` [VPS-luffy]
- ไฟล์ v1 ใน repo: `classtracking/web/` `api/` `db/` `infra/` `tests/*.py` `schema.sql` — รอ retire ตาม PLAN D17 (เลยกำหนด 11-05 แล้ว) (`classtracking/CLAUDE.md` §ระบบเก่า v1) [repo]

## 6. ID ของ LINE (ไม่ใช่ secret · มาจาก `V3:docs/CUTOVER-V3.md:13-20`) [repo]
| ของ | v2 LIVE | v3 |
|---|---|---|
| OA basic ID | `@091ifdgb` | `@041pmpha` ("ตัดคอร์ส") |
| Messaging API channel | `2012275936` | `2012521875` |
| LINE Login channel | `2012276102` | `2012522168` (Developing) |
| LIFF | `2012276102-XWtmFsJd` | `2012522168-qvReUB68` (endpoint ตอนนี้ = class-stg) |
- ctstg ใช้ token/secret ของ channel v3 `2012521875` (token ถูก reissue หลังหลุดในแชท 11-11) · probe `GET /v2/bot/info` = 200 basicId `@041pmpha` (`classtracking/handoff-2026-11-11-evening.md:34`) [repo · ผล probe = รายงานของ luffy]

## 7. แผน cutover v3 (ยังไม่ทำ)
- ทางเลือกที่แนะนำ **F1**: drop + create ฐานใน volume `ctnext_pgdata` เดิม · dump V2_FINAL ก่อน (เก็บ ≤ 90 วัน) · rollback = `pg_restore` V2_FINAL + image 0025b · คีย์ HMAC/ENC ไม่เปลี่ยน (`nami-cutover-fresh-db-plan-2026-11-12.md:9-14`)
- ขั้นทำลายมีขั้นเดียว (D3a · drop/create แบบมีด่าน) (`:12`)
- ⚠️ runbook ที่อยู่ใน trunk ตอนนี้ (`V3:docs/CUTOVER-V3.md`) **ยังเป็นฉบับเก่าที่ย้ายข้อมูล v2** (G1 = 0035 P3 convert · §4 คาด 10 migration ค้าง) → lane **Y7** ต้องเขียนใหม่ (ยังไม่เริ่ม/ยังไม่ merge) · ฉบับก่อนหน้า `classtracking/research/P3-cutover-runbook-draft.md` ถูกแทนแล้ว
- กติกาหลักของ deploy: build บน Mac (VPS ไม่มี swap) · `check:bundle` + e2e บน clean extract ของ REVIEWED_SHA ก่อน (`V3:docs/DEPLOY.md:54-57,104`) · migrate รันใน `docker/start.sh` ก่อนเปิดรับ request (`DEPLOY.md:478`) · v3 = เดินหน้าอย่างเดียว ถอย = กู้ snapshot (`DEPLOY.md:3828-3842`)

## 8. หน้าสรุป (เอกสาร ไม่ใช่แอป)
- GitHub Pages repo `kkdp13/classtracking-summary` → `https://kkdp13.github.io/classtracking-summary/` และ `/exec/` · ต้นฉบับ `classtracking/summary/index.html` · `classtracking/summary/exec/index.html` [repo + VPS-luffy] · deploy ล่าสุด 2026-11-12 00:10 (`notes/10_daily/2026-11-12.md`)
- ⚠️ franky ไม่ได้เทียบว่าหน้าที่ deploy ตรงกับไฟล์ใน repo ทุก byte **[ยังไม่ยืนยัน]**
