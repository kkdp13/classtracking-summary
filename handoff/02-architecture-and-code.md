# 02 · สถาปัตยกรรมและโค้ด — v3 (trunk `ct-v3-course-tracker` @ `b84c6f4a`)

> `V3:` = `/Users/tummac/Claude_Project/x-files/.claude/worktrees/ct-v3-merge/classtracking/app/` · อ่านแบบตรึง commit ได้ด้วย `git -C /Users/tummac/Claude_Project/x-files show b84c6f4a:classtracking/app/<path>`
> ทุกข้อในไฟล์นี้ franky เปิดไฟล์ยืนยันเอง **[repo]** เว้นแต่ติดป้ายอื่น · ⚠️ `V3:README.md` ยังเขียนหัวว่า "classtracking v2 — app · สถานะ M2" ซึ่ง**ล้าสมัย** อย่าใช้หัวข้อสถานะในไฟล์นั้น

---

## 1. Stack
| ชั้น | ของจริง | ที่มา |
|---|---|---|
| เว็บ + API | Next.js `16.4.2` · React `19.3.1` · TypeScript `6.0.3` strict · Tailwind 4 | `V3:package.json` |
| DB | PostgreSQL 16 (image `ctnext-db:16` จาก `docker/Dockerfile.db`) · drizzle-orm `0.45.4` · `pg 8.23.1` | `V3:package.json` · `V3:docker/compose.yml:31` |
| LINE | `@line/liff 2.31.2` (ฝั่ง browser) · Messaging API เรียกตรงด้วย fetch | `V3:package.json` · `V3:src/modules/notifications/line.ts` |
| worker | process แยก (`src/worker/main.ts`) · image เดียวกับแอป | `V3:docker/compose.yml:121` |
| เทสต์ | vitest 5 (unit + integration บน Postgres จริง) · Playwright 1.64 (e2e บน `next build` จริง) | `V3:package.json` scripts |
| Node | `>=22.12.0` | `V3:package.json` engines |

ด่านรวม: `npm run check` = typecheck · lint · unit · db:drift · integration · audit:deps · `npm run check:bundle` = build + สแกน secret ใน bundle ฝั่ง browser (`V3:package.json` scripts)

## 2. โครงโค้ด
- `V3:src/app/` — หน้า (member: `/classes` `/classes/new` `/classes/[id]` `/classes/[id]/consume` `/history` `/settings` `/record` `/topup` `/billing` `/join` `/suspended` `/occurrences/[id]/value` · public: `/` `/liff` `/faq` `/privacy` `/terms` `/coming-soon` · admin: `/admin/**`) และ route API ใต้ `src/app/api/**/route.ts` (36 ไฟล์ · รายการเต็ม: `find V3:src/app -name route.ts`)
  - ⚠️ บน trunk **ยังไม่มีหน้า `/schedule`** (มีแค่ `src/app/api/schedule/route.ts`) · หน้าปฏิทินอยู่ใน `franky-v3-p4d3` (`21f7bc0e`) ที่ยังไม่ merge
- `V3:src/modules/` — โดเมน: `admin` `billing` `brand` `content` `entitlement` `household` `identity` `ledger` `line` `notifications` `privacy` `provider` `schedule` `staging` `telegram` (เหลือแค่ admin alert) `terms`
- `V3:src/worker/worker.ts` — loop ของ worker · `V3:src/proxy.ts` — ด่าน CSRF กลาง + staging gate + แยก surface admin
- `V3:drizzle/` — migration SQL 35 ไฟล์ (`0000_init` … `0034_p5b_course_expiry`) · `V3:drizzle/meta/_journal.json` มี 35 entries (ตรวจด้วย python: `len(entries)=35` · ตัวท้าย `0034_p5b_course_expiry`)
- `V3:docs/` — `DEPLOY.md` `CUTOVER-V3.md` `CUTOVER.md` (ของ v1→v2) `RESTORE.md` `RICH-MENU.md` `ADMIN-GUIDE.md` `PROBES-M3.md` `PROBES-DAY1.md`
- `V3:ops/` — script/systemd unit ของ backup, healthcheck, restore drill, tunnel, env template (ชื่อตัวแปรเท่านั้น), rich menu

## 3. ฐานข้อมูล

### 3.1 role (`V3:docker/initdb/01-roles.sh:18-20`)
| role | ใช้โดย | หมายเหตุ |
|---|---|---|
| `ct_migrator` | migrate + admin CLI · เป็นเจ้าของตาราง/ฟังก์ชัน DEFINER | NOSUPERUSER · NOBYPASSRLS |
| `ct_app` | แอปเว็บ | NOINHERIT · ได้ GRANT ทีละตาราง/ทีละคอลัมน์ · ตาราง M1 (บ้าน/คอร์ส/ledger ฯลฯ) ไม่มี DELETE/TRUNCATE (`V3:drizzle/0003_ledger_guards.sql:20`) · ⚠️ มี DELETE บางตาราง: `rate_limit_buckets` (`0015:24`) และตาราง Telegram เดิม (`0018:77,81` · ตารางเหล่านี้ถูกถอดใน `0032_tg_retire`) |
| `ct_worker` | worker | ต้องเป็น role นี้ ไม่งั้น worker exit 1 (`V3:src/worker/worker.ts:9-11`) |

ไม่ใช้ `ALTER DEFAULT PRIVILEGES` — ตารางใหม่ทุกตารางต้อง GRANT เองใน migration และ `tests/integration/roles.test.ts` จะแดงถ้าลืม (`01-roles.sh:9`)

### 3.2 ตาราง (30 `pgTable` ใน `V3:src/server/db/schema.ts`)
`members` `identities` `sessions` `notification_outbox` `audit_events` `line_messages_monthly` `worker_heartbeat` `households` `signup_waitlist` `household_members` `household_invites` `kids` (= ป้ายชื่อ) `classes` (= คอร์ส) `schedules` `class_sessions` (= occurrence เมื่อ source = scheduled) `ledger_entries` `line_friends` `line_webhook_events` `household_tombstones` `rate_limit_buckets` `entitlement_events` `billing_config` `line_messages_monthly_household` `occurrence_notifications` `course_expiry_notifications` `course_expiry_gate` `pdpa_verifications` `payment_attempts` `payment_events` `receipts`
View: `class_balances` · `course_balances` (`0028:445,457`) · `class_occurrences` (`0030:830`) — ทั้งหมด `security_invoker = true`

**การ map กับ spec §06:** v3 ไม่ rename ตาราง — `classes` = courses · `households` ที่มีสมาชิก 1 คน = user · `kids` = beneficiaries · `class_sessions` = class_occurrences (`V3:drizzle/0027_course_model.sql:5-7` · `0029_individual_accounts.sql:3` · `0030_occurrences.sql:6`)

### 3.3 migration (ไล่ตามช่วง)
- `0000`–`0025` = v2 ที่ LIVE (journal 26 แถวบน ctnext · **[VPS-luffy]**)
- `0026_extra_sessions` = ของรอบ W-22 ที่ไม่เคย LIVE (`0027:3`)
- v3: `0027_course_model` (ชนิดคอร์ส · expires_on · schedules) · `0028_ledger_units` (ledger v2 หน่วยจำนวนเต็ม · `ledger_consume`/`ledger_topup` · idempotency · CT040–CT049) · `0029_individual_accounts` (สมาชิก 1 คน/บ้าน · ปิดรหัสเชิญถาวร CT050) · `0030_occurrences` · `0031_identity_rebind` · `0032_tg_retire` · `0033_p5a_owner_push` · `0034_p5b_course_expiry`
- lane ที่ยังไม่ merge ใช้เลข **0035/0036/0037 ชั่วคราว** (luffy จัดเลขตอน merge): PL-A `0035_pl_a_plans` · P4f-1 `0035_p4f_reschedule` · P4e มี 0035+0036 (`research/chopper-P4eFU-2026-11-12.md`) **[รายงาน]**

### 3.4 ledger append-only (`V3:drizzle/0003_ledger_guards.sql:4-17`)
3 ชั้น แต่ละชั้นมีเทสต์แยกที่แดงเมื่อถอดชั้นนั้น (`tests/integration/ledger.test.ts`):
1. trigger `ledger_entries_no_update_delete` → UPDATE/DELETE/TRUNCATE = CT001 (`0003:46,51`)
2. สิทธิ์: `ct_app` ได้แค่ SELECT + INSERT
3. hash chain: `row_hash = sha256(prev_hash + คอลัมน์)` ต่อครัวเรือนตาม `chain_seq` · ข้อจำกัดที่เขียนไว้เอง: ลบแถวท้ายสายตรวจไม่เจอ และ chain ไม่มี anchor นอกฐาน (`0003:8-11`)
- v3 เพิ่ม `hash_version = 2` · `delta_units` จำนวนเต็ม (DURATION = นาที · SESSION = ร้อยส่วนของครั้ง เช่น −100 = 1 ครั้ง · VALUE = สตางค์) · `idempotency_key` (`0028` · `0030` ข้อ 4)
- แก้รายการผิด = reversal (`V3:src/app/api/ledger/[entryId]/reversal/route.ts` · `V3:src/modules/ledger/reversal.ts`)
- การลบเกิดได้ทางเดียวคือ `erase_household` (เปิดด้วย GUC + DEFINER · `0011_erase_household.sql:54-63`)

### 3.5 RLS และฟังก์ชัน DEFINER
- RLS ต่อครัวเรือนเริ่มที่ `0014_household_rls.sql` · มีคำสั่ง ENABLE/FORCE RLS 40 จุดใน migration ทั้งหมด (`grep -ci "enable row level security\|force row level" V3:drizzle/*.sql`)
- มีคำว่า `SECURITY DEFINER` 90 จุดใน migration (`grep -i "security definer" V3:drizzle/*.sql | wc -l`) — ตัวหลักของ v3:
  - `ledger_consume` (`0030:477` → แทนด้วย `0033:63`) · `ledger_topup` (`0028`) · `course_set_expiry` (`0027` → `0034`)
  - `household_detach_member` (`0029` ข้อ 3 · ไม่ให้ EXECUTE กับใคร = ผู้ดูแลเรียกผ่าน CLI)
  - `occurrences_generate` (`0030` · INSERT แถว scheduled ได้เฉพาะ ct_migrator)
  - `account_set_notify_prefs` · `household_set_remind_before` (`0034` ข้อ 3, 5)
- ด่านที่ใช้ GUC ต้องผูกกับ `current_user` ด้วย (บทเรียน L-0149) เช่น `households_remind_before_guard` (`0034:17`)
- ลำดับ lock มาตรฐาน: advisory lock ของครัวเรือน (CTLG) → แถวบ้าน → แถวลูก (`0029` ข้อ 3 · `V3:src/modules/line/postback.ts:5-14`)

## 4. LINE OA + LIFF

### 4.1 Login (LIFF)
- `liff.init({ liffId, withLoginOnExternalBrowser: true })` → ID token → `POST /api/auth/line` → แลกเป็น cookie `__Host-ct_session` (`V3:src/modules/identity/liff-client.ts:35-42` · `session.ts:16` · `auth-handlers.ts:1-20`)
- ตรวจ ID token กับ LINE (`line-verify.ts`) · userId เก็บเป็น HMAC (`LINE_SUBJECT_HMAC_KEY`) + เข้ารหัส (`LINE_SUBJECT_ENC_KEY`) (`V3:src/modules/identity/subject.ts`)
- mutation ทุกตัวต้องผ่าน CSRF (`x-ct-csrf: 1` + same-origin) ที่ `V3:src/proxy.ts:1-5` และ `guardMutation()` ซ้ำในแต่ละ route
- `LAUNCH_MODE=staging` → คนนอก `STAGING_ALLOWLIST` เห็นแค่ `/coming-soon` (`V3:src/modules/staging/gate.ts`)

### 4.2 Webhook (`POST /api/line/webhook`)
ลำดับด่าน (`V3:src/modules/line/webhook-handlers.ts:5-7`): config ครบ → rate limit ต่อ IP → body ดิบ ≤ 64KB → **ลายเซ็น HMAC** → JSON → **destination = `LINE_BOT_USER_ID`** (กัน OA ผิด stack) → เพดานรวม → ประมวลผล → 200
- งบเวลาต่อ request (`WEBHOOK_REQUEST_BUDGET_MS`) เพราะ LINE timeout 2 วิ · reply ส่งหลังตอบ (`after()`) · dedupe ด้วย `webhookEventId` (`line_webhook_events`) (`:12-24`)
- ล้มชั่วคราว → 503 ให้ LINE ส่งซ้ำ · ล้มถาวร → 200 + reply "⚠️ ยังไม่ได้บันทึก"
- ข้อความที่ผู้ใช้พิมพ์เอง: dedupe แล้ว **ไม่ตอบ** (`:28`)

### 4.3 Rich menu v3 (`V3:ops/richmenu/richmenu-v3.json` · 1 ปุ่มใหญ่ + 5)
| ปุ่ม | action |
|---|---|
| ตัดคอร์ส | postback `menu:consume` → Flow A (`V3:src/modules/line/course/postback.ts`) |
| คอร์สของฉัน · เพิ่มคอร์ส · ตารางเรียน · ประวัติ · บัญชี | LIFF URI `?next=/classes` `/classes/new` `/schedule` `/history` `/settings` |
⚠️ ชื่อปุ่ม 4 บน trunk = "ตารางเรียน" · คำตัดสิน = "ตารางนัด" (ใน branch `franky-v3-p4d3` · ยังไม่ได้อัปโหลด rich menu ขึ้น OA) · ⚠️ `menu:today` (v2) ยังอยู่ใน `V3:src/modules/line/menu.ts:49` แม้ G14 = เลิกใช้แล้ว

### 4.4 Flow
- **Flow A (ตัดคอร์สในแชท):** `menu:consume` → reply รายการคอร์ส → ปุ่ม `c2.*` (postback token มีลายเซ็น `LINE_POSTBACK_KEY` / `_PREV`) → ยืนยัน → `ledger_consume` → reply ยอด (`V3:src/modules/line/course/*` · `postback-token.ts`)
- **Flow B (ข้อความเตือนคาบ):** worker สร้าง occurrence → push 1 ครั้งต่อคาบถึงเจ้าของ → ปุ่มตอบเป็น postback (`V3:src/modules/line/occurrence/*`) · `POST /api/occurrences/[id]/respond` (ทางแอป)
- **Flow C (สร้างคอร์ส):** หน้า LIFF `/classes/new` → `POST /api/classes` (ฟอร์ม v3 เต็ม = lane P4c-UI ยังไม่ทำ · ดู 04)
- VALUE: กรอกมูลค่าในหน้า `/occurrences/[id]/value` หรือ `/classes/[id]/consume`

## 5. Worker (`V3:src/worker/worker.ts:50-156`)
ตัวที่ถือ advisory lock เท่านั้นที่ทำงาน (ตัวอื่นเป็น standby) · heartbeat ผ่าน connection ที่ถือ lock (`:5-8`)
| task | ทำอะไร |
|---|---|
| `generate-occurrences` | สร้าง occurrence จาก schedules (ทุก 10 นาที · `:45,55`) |
| `enqueue-occurrence-reminders` | คิวเตือนก่อนคลาส (ธง `PUSH_ENABLED`) |
| `enqueue-expiry-reminders` | คิวเตือนคอร์สใกล้หมดอายุ 14/3 วัน (ธง `EXPIRY_REMINDERS_ENABLED`) |
| `entitlement-notices` | แจ้งสิทธิ์ใช้งานแบบ v2 (PL-A จะแทนด้วย `plan-notices`) |
| `billing-expire-attempts` | รายการชำระที่หมดเวลา |
| `dormant-notice` | แจ้งบ้านไม่ใช้งาน 24 เดือน |
| `line-quota-sync` | ดึงโควตาข้อความของ OA (endpoint ยังไม่เคยเรียกของจริง · `V3:src/modules/notifications/line-quota-sync.ts:3`) |
| `notify-outbox` | ส่งข้อความจาก `notification_outbox` |
| `retention-purge` | ลบตามระยะเก็บ (`V3:src/modules/privacy/retention.ts`) |
| `billing-purge-slips` | ล้างสลิปตามระยะเก็บ |

กติกา push: เฉพาะเจ้าของบัญชี · โควตาเดือนปฏิทินไทย free 0 / pro 100 / max 300 (`V3:src/modules/entitlement/push-plan.ts:3-4,16`) · หยุดเมื่อโควตา OA ≥ 80% (`quota-guard.ts`) · `X-Line-Retry-Key` = id ของแถว outbox (`expiry-reminder.ts:24`)

## 6. Admin
- surface แยก `CT_SURFACE=admin` (container `ctnext-admin` · profile `admin` · `127.0.0.1:8010` · `V3:docker/compose.yml:196-226`) อยู่หลัง Cloudflare Access (JWT ตรวจใน `V3:src/modules/admin/gate.ts`) · **ตอนนี้ไม่ได้รัน** **[VPS-luffy]**
- admin CLI `npm run admin` ต่อเป็น `ct_migrator` (`V3:scripts/admin-cli.ts`)
- Telegram เหลือแค่ admin alert (`TELEGRAM_ADMIN_CHAT_IDS` / `TELEGRAM_BOT_TOKEN`) · ฝั่งผู้ใช้ถอดใน 0032 (`V3:drizzle/0032_tg_retire.sql`)

## 7. เทสต์
- `V3:tests/{unit,integration,e2e,fixtures,helpers}` (219 ไฟล์)
- franky **ไม่ได้รันเทสต์** สำหรับชุดเอกสารนี้ · ตัวเลขล่าสุดจาก reviewer: บน tree ที่ merge P4e-FU แล้ว — integration 1375 · unit 1975 · มี flake ช่วงเที่ยงคืนเวลาไทย (`research/jinbe-P4eFU-2026-11-12.md`) **[รายงาน]**
