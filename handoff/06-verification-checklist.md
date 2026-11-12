# 06 · รายการให้ผู้ตรวจพิสูจน์ (verification checklist)

> ใช้ตั้งค่าใน terminal ก่อน:
> ```sh
> ROOT=/Users/tummac/Claude_Project/x-files
> T=b84c6f4a                      # trunk v3 ณ ตอนเขียน
> ```
> อ่านไฟล์ v3 แบบตรึง commit: `git -C "$ROOT" show "${T}:classtracking/app/<path>"` (ใช้ `${T}` เสมอเมื่อตามด้วย `:` — บทเรียน L-0125)
> ทุกคำสั่งเป็นแบบ **อ่านอย่างเดียว** · ห้ามรัน `git checkout/stash/reset` ใน repo นี้ (มีหลาย session ใช้ร่วมกัน · L-0166)
> ข้อที่ติด [VPS-luffy] ผู้ตรวจภายนอกพิสูจน์ด้วยคำสั่งไม่ได้ — ให้ตรวจแค่ว่าสอดคล้องกับไฟล์ใน repo
> ผลคาดหวังในคอลัมน์ "ควรเห็น" = สิ่งที่ franky เห็นตอนเขียน · ถ้าไม่ตรง = ข้อนั้นผิดหรือ repo ขยับไปแล้ว (ตรวจ `git log -1 ct-v3-course-tracker` ก่อน)

---

## A. ตัวตนของ release และ branch
| # | ข้ออ้าง | คำสั่ง / ไฟล์ | ควรเห็น |
|---|---|---|---|
| A1 | trunk v3 = `b84c6f4a` | `git -C "$ROOT" log --oneline -1 ct-v3-course-tracker` | `b84c6f4a merge(classtracking v3): P5b-UI …` (หรือใหม่กว่าถ้าขยับแล้ว) |
| A2 | tag v2 LIVE = commit `16ad44a5` | `git -C "$ROOT" rev-parse --short 'ct-v2-live-0025b^{commit}'` | `16ad44a5` |
| A3 | trunk มี 35 migration ถึง 0034 | `git -C "$ROOT" show "${T}:classtracking/app/drizzle/meta/_journal.json" \| python3 -c "import json,sys;j=json.load(sys.stdin);print(len(j['entries']),j['entries'][-1]['tag'])"` | `35 0034_p5b_course_expiry` |
| A4 | LIVE = image `944f273d30c1` · journal 26 | `V3:docs/DEPLOY.md:3831` + [VPS-luffy] | ข้อความ "image `ctnext-app:944f273d30c1` · REVIEWED_SHA `944f273d` · journal 26 = 0000–0025" |
| A5 | pivot ไป v3 + spec ชนะ | `git -C "$ROOT" show --stat 2d2f1e3d 0acd5257` | 2 commit วันที่ 2026-11-10 · "pivot to LINE Course Tracker spec" · "spec-is-authoritative" |
| A6 | v3 ใช้ฐานใหม่ ไม่ย้ายข้อมูล | `classtracking/handoff-2026-11-11-evening.md:102-103` · `classtracking/research/nami-cutover-fresh-db-plan-2026-11-12.md:1-9` | "legacy data not needed" · "P3 lane dropped" · F1 recommended |

## B. ledger / ความถูกต้องของยอด
| # | ข้ออ้าง | ไฟล์ | ควรเห็น |
|---|---|---|---|
| B1 | ledger append-only 3 ชั้น | `V3:drizzle/0003_ledger_guards.sql:4-11,46,51` | trigger CT001 · ct_app SELECT+INSERT · hash chain |
| B2 | ct_app ไม่มี DELETE บนตาราง M1 (ledger/บ้าน/คอร์ส) | `V3:drizzle/0003_ledger_guards.sql:20` และ `grep -n "GRANT[^;]*DELETE[^;]*ct_app" V3:drizzle/*.sql` | grep เจอ 3 บรรทัด: `0015:24` (rate_limit_buckets) · `0018:77,81` (telegram_links / telegram_link_codes · ถอดใน 0032) — **ไม่มีตาราง ledger/บ้าน/คอร์ส** |
| B3 | หน่วยเป็นจำนวนเต็ม (นาที · 1/100 ครั้ง · สตางค์) | `V3:drizzle/0030_occurrences.sql` ข้อ 4 (`DURATION = −duration_minutes · SESSION = −100`) · `V3:src/modules/ledger/units.ts` | ตรงตามนั้น |
| B4 | ไม่ตัดอัตโนมัติ · ไม่ตอบ = ไม่ตัด | `V3:src/worker/worker.ts:50-156` (ไม่มี task ใดเขียน ledger) | รายการ task ไม่มีการ consume **(ตรวจโดยอ่านแต่ละ task)** |
| B5 | idempotent ต่อ webhook ซ้ำ | `V3:src/modules/line/webhook-handlers.ts:22` · `postback.ts:4` (`line_webhook_events` unique) | dedupe ด้วย webhookEventId |
| B6 | ยอดไม่ติดลบ (CT040) | `V3:drizzle/0028_ledger_units.sql:26` | "CT040 ยอดไม่พอ" |

## C. วันหมดอายุ 2 แบบ (แยกกัน · รายละเอียด 01 §4)
**C-แผน (PL-A · branch `franky-v3-pl-a` @ `4c9d9570` · ยังไม่ merge)** — ใช้ `P=4c9d9570` และ `git -C "$ROOT" show "${P}:classtracking/app/<path>"`
| # | ข้ออ้าง | ไฟล์ | ควรเห็น |
|---|---|---|---|
| C1 | ช่วงแผนเก็บใน `plan_periods` (pro/max · payment/comp/card) | `drizzle/0035_pl_a_plans.sql:8` | ตามนั้น |
| C2 | ไม่มีช่วงครอบ = Free (แผนจบ → Free) | `0035_pl_a_plans.sql:10-12` | `ไม่มี → 'free'` |
| C3 | ไม่ freeze คอร์สเมื่อแผนจบ (ไม่มี CT049 อีก) | `0035_pl_a_plans.sql:16-18` | "ไม่ freeze … CT049 ไม่ถูกยกอีก" |
| C4 | **trunk ยัง freeze** | `V3:drizzle/0028_ledger_units.sql:243-257` (trunk) | RAISE CT049 / `entitled_until` |
| C5 | เพดาน 5/20/60 นับทุกสถานะยกเว้น archived · คอร์สเดิมที่เกินอยู่ต่อได้ | `0035_pl_a_plans.sql:13-15` · `src/modules/entitlement/plan.ts:6-7,19-22` | ตามนั้น · CT070 |
| C6 | comp ค่าเริ่ม Pro 3 เดือน · ล็อกวันจบเมื่อมีช่วงต่อท้าย/รายการชำระเปิด (CT072) | `0035_pl_a_plans.sql:21-26` · `git -C "$ROOT" show --stat b698a06e` | ตามนั้น |
| C7 | แจ้งแผน 14/7 วัน + จบแล้ว ≤ 7 วัน · ครั้งเดียว · ไม่นับโควตาเตือนคลาส · P0 | `src/modules/entitlement/plan-notices.ts:1-10,24-26` | ตามนั้น |
| C8 | ระยะเก็บ plan_periods ยังรอ law (Q-L4) | `0035_pl_a_plans.sql:35` | "Q-L4 … ยังเปิด" |

**C-คอร์ส (trunk `${T}` ยกเว้น C15)**
| # | ข้ออ้าง | ไฟล์ | ควรเห็น |
|---|---|---|---|
| C9 | `expires_on` ไม่บังคับ (null = ไม่หมดอายุ) · วันที่ไทย | `V3:drizzle/0027_course_model.sql:9` | ตามนั้น |
| C10 | แก้ได้ทาง `course_set_expiry` เท่านั้น · เจ้าของบัญชี · ห้ามวันที่ผ่านแล้ว (CT055) | `0027:19-21` · `V3:drizzle/0034_p5b_course_expiry.sql:14-15` | ตามนั้น |
| C11 | ตัดไม่ได้หลังหมดอายุ (CT043) | `0028:197,346` · `0030:358` · `V3:src/modules/household/consume-rules.ts:9` | `RAISE … CT043` · `"course_expired"` |
| C12 | คาบผูกตาราง: เทียบวันของคาบ · ยกเลิก/ลาได้แม้หมดอายุ | `0030:45,49` | ตามนั้น |
| C13 | เตือน 14/3 วัน · เฉพาะบัญชีที่มีสิทธิ์ (Free = skipped_plan) · ไม่นับโควตาคลาส · หยุดที่ OA ≥ 80% | `V3:src/server/db/enums.ts:82` · `V3:src/modules/notifications/expiry-reminder.ts:1-4,21-22` | `[14, 3]` · ข้อความตามนั้น |
| C14 | สวิตช์ปิด/เปิดของบัญชี (ค่าเริ่มเปิด) + ธง `EXPIRY_REMINDERS_ENABLED` | `0034:8,12-13` · `V3:src/worker/worker.ts:80-82` | ตามนั้น |
| C15 | เลื่อนนัดเกินวันหมดอายุไม่ได้ (CT059) | `git -C "$ROOT" show de2c072a:classtracking/app/drizzle/0035_p4f_reschedule.sql \| sed -n '20,28p'` | `CT059 วันใหม่หลังวันหมดอายุคอร์ส` (branch `franky-v3-p4f12` ยังไม่ merge) |
| C16 | ระยะเก็บ: ผลส่ง 90 วัน · gate 30 วัน | `V3:src/modules/privacy/retention.ts:19-20` | ตามนั้น |
| C17 | คอร์สที่หมดอายุไม่ถูกลบอัตโนมัติ | `git -C "$ROOT" show "${T}:classtracking/app/src/modules/privacy/retention.ts" \| grep -n classes` | ว่าง (**พิสูจน์ด้วยการไม่พบเท่านั้น**) |
| C18 | คอร์สหมดอายุที่ยังไม่ archive ยังนับเพดาน | — | **[ยังไม่ยืนยัน]** อนุมานจาก C5 · ไม่พบเทสต์ตรง |
| C19 | ทั้งสองแบบไม่ขึ้นต่อกัน (คนละตาราง/worker) | เทียบ `plan-notices.ts` + `plan_period_notices` (PL-A) กับ `expiry-reminder.ts` + `course_expiry_notifications` (0034) | ไม่มีการอ้างข้ามกัน **(ผู้ตรวจควร grep `plan_periods` ใน expiry-reminder.ts และ `expires_on` ใน plan-notices.ts เอง)** |

## D. LINE / ความปลอดภัย
| # | ข้ออ้าง | ไฟล์ | ควรเห็น |
|---|---|---|---|
| D1 | webhook ตรวจลายเซ็นก่อนแตะ DB · destination ต้องตรง `LINE_BOT_USER_ID` | `V3:src/modules/line/webhook-handlers.ts:1-10` | ตามนั้น |
| D2 | CSRF กลางใน proxy + webhook ไม่ผ่าน proxy | `V3:src/proxy.ts:1-5` | ตามนั้น |
| D3 | session cookie `__Host-ct_session` | `V3:src/modules/identity/session.ts:16` | ตามนั้น |
| D4 | 3 role ไม่ใช่ superuser ไม่ bypass RLS | `V3:docker/initdb/01-roles.sh:18-20` | `NOSUPERUSER … NOBYPASSRLS` |
| D5 | worker ต้องเป็น ct_worker | `V3:src/worker/worker.ts:9-11` | WorkerRoleError |
| D6 | response LINE/Telegram ยังไม่ได้ probe | `V3:src/modules/notifications/line.ts:16` · `V3:src/modules/telegram/api.ts:24` | `= false` ทั้งคู่ |
| D7 | rich menu ปุ่ม 4 บน trunk = "ตารางเรียน" (คำตัดสิน = "ตารางนัด" ยังไม่ merge) | `V3:ops/richmenu/richmenu-v3.json` · `git -C "$ROOT" show --stat 8a914611` | label `ตารางเรียน` บน trunk |
| D8 | `menu:today` ยังอยู่ใน trunk (G14 ตัดสินเลิกแล้ว) | `V3:src/modules/line/menu.ts:49` | `MENU_TODAY_DATA = "menu:today"` |

## E. infra (ส่วนใหญ่ [VPS-luffy] · ตรวจความสอดคล้องกับ repo)
| # | ข้ออ้าง | หลักฐานใน repo | ควรเห็น |
|---|---|---|---|
| E1 | app ฟัง `127.0.0.1:8006` · admin `127.0.0.1:8010` profile admin | `V3:docker/compose.yml:99,196,226` | ตามนั้น |
| E2 | backup 04:20 ไทย (ชื่อไฟล์ T212022Z) | `V3:ops/ctnext-backup.timer:9` | `OnCalendar=*-*-* 04:20:00 Asia/Bangkok` |
| E3 | healthcheck ทุก 5 นาที · restore drill วันที่ 2 | `V3:ops/ctnext-healthcheck.timer:9` · `ctnext-restore-drill.timer:8` | ตามนั้น |
| E4 | tunnel service ใช้ config `/home/fox/.cloudflared/classtracking.yml` | `V3:ops/classtracking-tunnel.service:14` | ตามนั้น |
| E5 | ID ของ LINE v2/v3 | `V3:docs/CUTOVER-V3.md:13-20` | ตารางตามที่ 03 §6 |
| E6 | ctstg infra พร้อม (Access · ingress :8012 · DNS) แต่ยังไม่ขึ้น | `classtracking/research/ctstg-infra-log-2026-11-11.md` + [VPS-luffy] | — |
| E7 | runbook ใน trunk ยังเป็นแผนย้ายข้อมูล | `V3:docs/CUTOVER-V3.md:36,63-82` | G1 = "migration 0035 (P3): convert_legacy_sessions…" · "pending 10" |

## F. สถานะรีวิว (อ่านรายงาน · ไม่ต้องรันเทสต์)
| # | ข้ออ้าง | ไฟล์ |
|---|---|---|
| F1 | P4e-FU chopper PASS + jinbe PASS | `classtracking/research/chopper-P4eFU-2026-11-12.md:1,7` · `jinbe-P4eFU-2026-11-12.md:1,6` |
| F2 | P4f-0 chopper NEEDS-FIX (B1) · jinbe NEEDS-FIX (เฉพาะเรื่อง merge · โค้ดปลอดภัย) | `classtracking/research/chopper-P4f0-2026-11-12.md:1-2` · `jinbe-P4f0-2026-11-12.md:1-3` |
| F3 | LEGAL-PUB chopper + jinbe NEEDS-FIX | `classtracking/research/chopper-legalpub-2026-11-12.md:1` · `jinbe-legalpub-2026-11-12.md:1` |
| F4 | law PASS terms d22 + privacy d21 · FAQ d8 = release-candidate | `classtracking/research/law-t22-p21-2026-11-11.md` |
| F5 | lane ที่อยู่ระหว่างรีวิว (P5c-3 · P4d-3 · PL-A · P5d · P4f-1/2) ยังไม่มีรายงาน chopper ของ 11-12 · มีแค่ zoro PASS ของ copy ป้ายชื่อ (account-tag d2) ซึ่งเกี่ยวกับ P5c-3 | `ls classtracking/research \| grep 2026-11-12` (ณ ตอนเขียนมี 8 ไฟล์: chopper P4eFU/P4f0/legalpub · jinbe P4eFU/P4f0/legalpub · nami fresh-db · zoro account-tag-d2) |

## G. ข้ออ้างที่ **ยังไม่ยืนยัน** (ผู้ตรวจควรหาแหล่งภายนอกเอง)
| # | ข้ออ้าง | อยู่ที่ไหน | ทำไมถึงยังไม่ยืนยัน |
|---|---|---|---|
| G1 | LINE Reply API เริ่มคิดเงิน 2027-02-01 / "Reply Unlimited 2027 5,000 บาท" | spec §10 · `classtracking/summary/index.html:1373,1399` · `summary/exec/index.html:926` | หน้าสรุปเขียนกำกับเองว่ายังไม่ได้ตรวจกับประกาศทางการฉบับล่าสุด |
| G2 | OA ฟรี 300 ข้อความ/เดือน · Basic 1,280 บาท 15,000 ข้อความ · ส่วนเกิน 0.10 บาท | `classtracking/research/R-line-oa-pricing-2026-11-09.md:23-28` | ค้นเมื่อ 11-09 · ราคาอาจเปลี่ยน · เวลารีเซ็ตโควตาไม่พบประโยคทางการ (`:50`) |
| G3 | "55–60% ของงานก่อนเปิดให้ลูกค้า" | `classtracking/summary/exec/index.html:305,809-812` | สูตรที่ทีมกำหนดเอง ไม่ได้วัดจริง |
| G4 | มีตลาดรองรับ | `classtracking/CLAUDE.md:82` | ยังไม่ได้คุยกับโรงเรียน/ร้านจริงสักแห่ง |
| G5 | ผู้ให้บริการ off-site backup จริง | `V3:ops/classtracking-backup.env.template:9` | template ยกตัวอย่าง B2 · ค่าจริงอยู่ใน secret ที่ไม่ได้เปิด |
| G6 | หน้า GitHub Pages ตรงกับไฟล์ `summary/` | — | ไม่ได้เทียบ byte |
| G7 | ตัวเลขเทสต์ (unit 1975 · integration 1375) | `research/jinbe-P4eFU-2026-11-12.md` | franky ไม่ได้รันซ้ำ |
