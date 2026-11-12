# 01 · ผลิตภัณฑ์และคำตัดสิน — classtracking v3 "ตัดคอร์ส"

> ป้าย: **[repo]** ตรวจในไฟล์เอง · **[VPS-luffy]** luffy ตรวจบน VPS 2026-11-12 · **[รายงาน]** จากรายงาน agent อื่น · **[ยังไม่ยืนยัน]**
> `V3:` = `/Users/tummac/Claude_Project/x-files/.claude/worktrees/ct-v3-merge/classtracking/app/` (branch `ct-v3-course-tracker` @ `b84c6f4a`)

---

## 1. แหล่งความจริง (ลำดับความสำคัญ)

1. **spec v3** = `classtracking/research/course_tracker_handoff-2026-11-10.html` ("PRODUCT + ENGINEERING HANDOFF · V1.0 · LINE Course Tracker" · ลงวันที่ 10 พ.ย. 2026) **[repo]**
2. กฎ "spec ชนะทุกข้อที่ขัดกับคำตัดสินเก่า" — Mr.Foxx 2026-11-10 · commit `0acd5257` (`classtracking/research/iceberg-v3-pivot-2026-11-10.md`) · memory `~/.claude/projects/-Users-tummac-Claude-Project-x-files/memory/classtracking-v3-spec-authoritative.md` **[repo]**
3. คำตัดสินของ Mr.Foxx ที่มาทีหลัง spec (บางข้อ **เบี่ยงจาก spec โดยตั้งใจ** ดู §6) = `classtracking/handoff-2026-11-11-evening.md` (บรรทัด 26–111) **[repo]**
4. เอกสาร v2 (`classtracking/PLAN-v2-line.md` · `classtracking/FIRST-VERSION-SUMMARY.md`) ใช้ได้เฉพาะจุดที่ spec ไม่ได้พูดถึง

---

## 2. ผลิตภัณฑ์ตาม spec (สรุป)

| หัวข้อ | spec พูดว่า | ที่มาใน spec |
|---|---|---|
| ผู้ใช้ | Individual: 1 LINE User ID = 1 บัญชี · ป้ายชื่อ "ลูก 1/ลูก 2" เป็นแค่ป้ายภายในบัญชีเดียว ไม่ใช่ผู้ใช้ร่วม | §01, §02 |
| หลัก | Reply-first (rich menu = postback + reply) · **Manual confirm ไม่มีการหักยอดอัตโนมัติ** | §01 |
| ชนิดคอร์ส | SESSION (ครั้ง) · DURATION (เก็บเป็นนาทีจำนวนเต็ม) · VALUE (สตางค์จำนวนเต็ม · แยกเงินจ่ายจริงกับเครดิตโบนัส) | §02 |
| ledger | ทุกการตัดเป็นรายการ immutable · แก้ = reversal + รายการใหม่ | §02 |
| Flow A | กด "ตัดคอร์ส" → postback → reply รายการคอร์ส → เลือกจำนวน → ยืนยัน → DB tx หักครั้งเดียว | §03 |
| Flow B | scheduler push 1 ครั้งต่อคลาส → ปุ่ม เข้าเรียน/ลา/งดสอน (v3 เปลี่ยนชื่อปุ่มแล้ว ดู §6) · ไม่กด = PENDING ไม่ตัด | §03, §04 |
| Flow C | สร้างคอร์สผ่าน LIFF (ชนิด · ยอดตั้งต้น · ผู้ใช้บริการ · วันหมดอายุ · ตาราง) | §03 |
| แผน | Free / Pro / Max · คอร์ส active 5/20/60 · push เตือนคลาส 0/100/300 ต่อเดือน | §05 |
| ราคาใน spec | Pro 49/269/469 · Max 99/539/949 (เดือน/6 เดือน/12 เดือน) — spec เขียนกำกับว่า "เป็นข้อเสนอ ยังไม่ผ่านการทดสอบตลาด" | §05 |
| ไม่ทำ | ระบบฝั่งร้าน/โรงเรียน · แชร์คอร์สหรือส่ง push ให้ผู้ปกครองคนอื่น · AI/OCR · ตัดอัตโนมัติตามเวลา | §01 |

ร่าง PRODUCT ของ v3 (positioning "แตะปุ๊บ ตัดปั๊บ") อยู่ใน `classtracking/research/PRODUCT-v3-draft.md` และเขียนกำกับว่า **"DRAFT ครบแล้ว · รอ Mr.Foxx ตอบ 'ยืนยัน'"** (บรรทัด 1) **[repo]** · ⚠️ ร่างนี้ยังใช้คำว่า "เข้าเรียน / ลา / งดสอน" และ "สี PickleWhale" ซึ่งคำตัดสินภายหลังแก้แล้ว (ปุ่ม = ตัดคอร์ส/ยกเลิก/เลื่อน · CI = Calm Indigo `#4352C4` · `handoff-2026-11-11-evening.md:31,61`) → ร่างนี้ **ล้าสมัยบางส่วน**

---

## 3. แผน Free / Pro / Max (ตามที่ตัดสินแล้ว ณ 11-11 ดึก)

| ประเด็น | ค่า | ที่มา |
|---|---|---|
| แผน | Free + Pro + Max เท่านั้น | `handoff-2026-11-11-evening.md:47` |
| Free | ฟรีตลอด · เพดาน 5 คอร์ส · **ไม่มี LINE push เลย** (ไม่มีเตือนคลาส ไม่มีเตือนคอร์สหมดอายุ) · ยังเห็นแถบหมดอายุในแอป | `:41-42` |
| ระยะเวลาแผนที่จ่ายเงิน | **6 เดือน และ 12 เดือนเท่านั้น** (แทนคำตัดสินก่อนหน้าที่มีรายเดือน 99 บาท) | `:48` |
| ราคา | **ยังไม่ตัดสิน** (ใช้ `{price}` ไว้ก่อน) | `:48` |
| เพดาน | Free 5 คอร์ส/0 push · Pro 20/100 · Max 60/300 | `:48` · โค้ด: `franky-v3-pl-a@4c9d9570:classtracking/app/src/modules/entitlement/plan.ts:19-22` · `V3:src/modules/entitlement/push-plan.ts:16` |
| นับเพดานคอร์ส | ทุกสถานะยกเว้น archived (active + paused + "จบแล้ว") | `handoff:52,87` · `franky-v3-pl-a@4c9d9570:.../drizzle/0035_pl_a_plans.sql:13-15` |
| ชำระเงิน | PromptPay ก่อน (จ่ายทีละรอบ + สลิป) · บัตรต่ออายุอัตโนมัติมาทีหลัง | `handoff:45` |
| สิทธิ์ฟรีทดสอบ (comp) | ค่าเริ่ม Pro 3 เดือน · มีวันสิ้นสุดเสมอ (ขยายด้วยมือ) · ได้เตือนหมดอายุด้วย · ซื้อ Pro/Max ได้ (ซ้อนต่อท้าย) | `handoff:51,58,60,62,89` |
| ซื้อซ้อน | ช่วงใหม่เริ่มวันถัดจากช่วงปัจจุบันจบ · นับจากเวลาที่เรายืนยันการชำระ | `handoff:57,86` |
| Pro→Max กลางรอบ | เปลี่ยนทันที ส่วนที่เหลือของ Pro เป็นเครดิต (lane PL-B ยังไม่เริ่ม) | `handoff:52` |
| ราคาเป็นของ LINE เท่านั้น | Telegram ถูกถอดแล้ว | `handoff:43` |

**[ยังไม่ยืนยัน]** โค้ดฝั่งชำระเงิน v3 (PL-B · SKU ใน `pricing.ts`) ยังไม่มี ตอนนี้ `V3:src/modules/billing/` ยังเป็นโค้ดของ v2 (checkout PromptPay ของครัวเรือน)

---

## 4. วันหมดอายุ 2 แบบ (แยกกันและไม่ขึ้นต่อกัน)

> ระบบมี "วันหมดอายุ" 2 ชนิด **ที่ไม่เกี่ยวกัน**: (1) **วันหมดอายุของแผน** = ช่วงที่บัญชีได้สิทธิ์ Pro/Max ซึ่งเราเป็นคนกำหนด (2) **วันหมดอายุของคอร์ส** = วันที่ลูกค้ากรอกเองตามที่ร้าน/โรงเรียนกำหนด
> แผนหมดอายุ **ไม่ทำให้** คอร์สหมดอายุ และคอร์สหมดอายุ **ไม่ทำให้** แผนเปลี่ยน แต่ละชนิดเก็บคนละตาราง ใช้ worker คนละตัว ข้อความคนละชุด และตัวนับคนละตัว
> ⚠️ ส่วนของแผนทั้งหมดอยู่ใน lane **PL-A ซึ่งยังไม่ merge** (`franky-v3-pl-a` @ `4c9d9570` · chopper กำลังรีวิว · ดู 04) ส่วนของคอร์สส่วนใหญ่ **merge แล้ว** (0034 · P5b · P5c-1)

### 4.1 วันหมดอายุของแผน (Pro/Max ที่จ่ายเงิน + สิทธิ์ฟรีทดสอบ)

| กติกา | สถานะในโค้ด | ที่มาในโค้ด |
|---|---|---|
| เก็บใน `plan_periods` (plan pro/max · `starts_at ≤ t < ends_at` · source `payment`/`comp`/`card`) | PL-A ยังไม่ merge | `franky-v3-pl-a@4c9d9570:classtracking/app/drizzle/0035_pl_a_plans.sql:8` |
| แผน ณ เวลา t = tier owner → max · มีช่วงที่ครอบ t → แผนของช่วง · ไม่มี → **free** (= แผนจบแล้วกลับเป็น Free) | PL-A | `0035_pl_a_plans.sql:10-12` |
| **ไม่ freeze คอร์ส** เมื่อแผนจบ: `ledger_assert_household_writable` เหลือแค่ CT004/CT048 · ไม่ยก CT049 อีก | PL-A | `0035_pl_a_plans.sql:16-18` · commit `2a745cd2` (PL-A3) |
| ⚠️ **trunk ตอนนี้ยัง freeze อยู่**: 0028 ยัง RAISE CT049 เมื่อหมดสิทธิ์ใช้งาน (`entitled_until`) จนกว่า PL-A จะ merge | trunk | `V3:drizzle/0028_ledger_units.sql:243-257` **[repo]** |
| เพดานคอร์ส: ถ้าเกินเพดานของแผน **คอร์สเดิมยังอยู่ต่อได้** แค่เพิ่มใหม่ไม่ได้ (CT070) | PL-A | `0035_pl_a_plans.sql:13-15` · `plan.ts:6-7` · commit `0e2a5e21` |
| comp: `plan_comp_grant` / `plan_comp_set_end` / `plan_comp_revoke` (DEFINER · ct_migrator · CLI `grant-comp` ค่าเริ่ม Pro 3 เดือน) · ห้ามแก้วันจบเมื่อมีช่วงต่อท้ายหรือมีรายการชำระที่เปิดอยู่ (CT072 · law R-c) | PL-A | `0035_pl_a_plans.sql:21-26` · commit `b698a06e` |
| ข้อความแจ้งเรื่องแผน: **14 และ 7 วันก่อนวันสุดท้าย + ตอนจบแล้ว (≤ 7 วัน)** · ส่งครั้งเดียวต่อ (บ้าน · ช่วง · ends_on · ชนิด) · `plan_period_notices` ไม่ถูก purge · ช่วง 09:00–21:00 | PL-A | `franky-v3-pl-a@4c9d9570:classtracking/app/src/modules/entitlement/plan-notices.ts:1-10,24-26` · `0035_pl_a_plans.sql:9` |
| **ไม่นับโควตาเตือนก่อนคลาส** · priority P0 (ส่งได้แม้โควตา OA ≥ 80%) | PL-A | `plan-notices.ts:10` |
| ระยะเก็บ `plan_periods` (Q-L4) = เก็บตลอดอายุบัญชี ลบตอน erase · **รอ law** | PL-A | `0035_pl_a_plans.sql:35` |
| ไม่มีการ backfill ช่วงจ่ายเดิมของ v2 (cutover ใช้ฐานใหม่อยู่แล้ว) | PL-A | `0035_pl_a_plans.sql:33-34` |
| ข้อความ "แพ็กหมดแล้ว" ถึงบัญชีที่กลับเป็น Free = ส่ง (เป็นประกาศของบัญชี ไม่ใช่การเตือน · ไม่นับโควตา) | คำตัดสิน | `handoff-2026-11-11-evening.md:62` |

### 4.2 วันหมดอายุของคอร์ส (`classes.expires_on`)

| กติกา | สถานะในโค้ด | ที่มาในโค้ด |
|---|---|---|
| ลูกค้าใส่เองได้ ไม่บังคับ (`expires_on` เป็นวันที่ไทย ใช้ได้ถึงสิ้นวัน · null = ไม่หมดอายุ) | trunk | `V3:drizzle/0027_course_model.sql:9` **[repo]** |
| แก้ผ่าน `course_set_expiry` เท่านั้น · เจ้าของบัญชีเท่านั้น · บันทึก audit · ห้ามตั้งเป็นวันที่ผ่านมาแล้ว (CT055) | trunk | `V3:drizzle/0027_course_model.sql:19-21` · `V3:drizzle/0034_p5b_course_expiry.sql:14-15` **[repo]** |
| **ตัดคอร์สไม่ได้หลังหมดอายุ (CT043)** ทั้งใน `ledger_consume` และ trigger ของ chain | trunk | `V3:drizzle/0028_ledger_units.sql:26,197,346` · `V3:drizzle/0030_occurrences.sql:358` · ฝั่ง TS `V3:src/modules/household/consume-rules.ts:2,9` **[repo]** |
| คาบที่ผูกกับตาราง: CT043 เทียบกับ **วันของคาบ** ไม่ใช่วันที่กด · ยกเลิก/ลาได้แม้คอร์สหมดอายุ | trunk | `V3:drizzle/0030_occurrences.sql:45,49` **[repo]** |
| `previewConsume` ไม่ตรวจวันหมดอายุ (law ทักไว้ใน copy §10.1) | trunk | `V3:src/modules/household/consume-rules.ts:2` **[repo]** |
| **เลื่อนนัดเกินวันหมดอายุไม่ได้ (CT059 · Q9=2)** · "ยังไม่รู้วัน" ยังเลือกได้แม้คอร์สหมดอายุ | P4f-1 ยังไม่ merge | `franky-v3-p4f12@de2c072a:classtracking/app/drizzle/0035_p4f_reschedule.sql:3,22,27` |
| เตือนก่อนหมดอายุ **14 และ 3 วัน** ทาง LINE ถึงเจ้าของบัญชี | trunk | `V3:src/server/db/enums.ts:82` · `V3:src/modules/notifications/expiry-reminder.ts:1` **[repo]** |
| **เฉพาะบัญชีที่มีสิทธิ์ข้อความเตือน** (Pro/Max/comp) · Free = `skipped_plan` | trunk | `V3:src/modules/notifications/expiry-reminder.ts:3-4,21` **[repo]** |
| ⚠️ ก่อน PL-A merge: แผนบน trunk = owner → max · ที่เหลือ free (ยังไม่มีแผนที่จ่ายได้) | trunk | `V3:src/modules/entitlement/push-plan.ts:5,18-20` **[repo]** |
| ปิด/เปิดได้: สวิตช์ `households.expiry_reminders_enabled` (ค่าเริ่ม = เปิด) ผ่าน `account_set_notify_prefs` · หน้าบัญชีมีสวิตช์ (P5b-UI merge แล้ว) | trunk | `V3:drizzle/0034_p5b_course_expiry.sql:8,12-13` · commit `d1d48f30`/`b84c6f4a` **[repo]** |
| ธงระบบ `EXPIRY_REMINDERS_ENABLED` ต้องเป็น `true` ถึงจะ enqueue · ช่วง 09:00–18:00 ไทย · worker ทุก 10 นาที | trunk | `V3:src/modules/notifications/expiry-reminder.ts:6` · `V3:src/worker/worker.ts:80-82` **[repo]** |
| ไม่นับโควตาเตือนก่อนคลาส · หยุดเมื่อโควตา OA ≥ 80% · จุดเดียวกันส่งไม่เกิน 1 ครั้งต่อ 30 วัน | trunk | `expiry-reminder.ts:4,22` · `0034:10` **[repo]** |
| ระยะเก็บ: `course_expiry_notifications` 90 วันหลังจบ · `course_expiry_gate` 30 วันนับจากวันครบกำหนดล่าสุด | trunk | `V3:src/modules/privacy/retention.ts:19-20` **[repo]** |
| ตัวคอร์สที่หมดอายุ **ไม่ถูกลบอัตโนมัติ** (ใน `retention.ts` ไม่มีการ purge `classes`) | trunk | ตรวจจากการ grep ว่าไม่มี (`grep -n classes V3:src/modules/privacy/retention.ts` = ว่าง) **[repo · ยืนยันจากการไม่พบเท่านั้น]** |
| คอร์สที่หมดอายุแต่ยังไม่ archive **ยังนับเพดานแผน** (เพดานนับทุกสถานะยกเว้น archived · วันหมดอายุไม่ใช่สถานะ) | อนุมานจาก PL-A | **[ยังไม่ยืนยัน]** ไม่พบเทสต์ที่ระบุกรณีนี้ตรงๆ |
| แอปแสดงแถบ/ป้ายหมดอายุ (Free เห็นด้วย) | trunk (P5c-1) | `V3:src/components/ExpiryBadge.tsx` · `V3:src/components/expiry.ts` · commit `58b3de21` **[repo]** |
| แจ้งยอดใกล้หมด (P5d) เตือนเฉพาะคอร์ส active/paused · ข้ามคอร์สที่จบแล้ว/archived/หมดอายุ | คำตัดสิน · P5d ยังไม่ merge | `handoff-2026-11-11-evening.md:95` (ข้อ 3) |

### 4.3 ทำไมถึงแยกกัน (ตัวอย่าง)
- บัญชี Pro ที่แผนจบแล้ว → กลับเป็น Free · คอร์สทุกคอร์สยังตัดได้ตามปกติ (หลัง PL-A) แต่จะไม่ได้รับ LINE เตือนหมดอายุอีก (Free = `skipped_plan`)
- คอร์สที่หมดอายุในบัญชี Max → ตัดคอร์สนั้นไม่ได้ (CT043) แต่แผน Max และคอร์สอื่นไม่ได้รับผลกระทบ
- ข้อความแจ้งเรื่องแผน (14/7/จบ) กับข้อความเตือนคอร์ส (14/3) **คนละ worker คนละตาราง**: `plan-notices` + `plan_period_notices` (PL-A) กับ `enqueue-expiry-reminders` + `course_expiry_notifications` (0034)

---

## 5. ปุ่มตอบในข้อความเตือนและปฏิทิน (คำตัดสิน 11-11)
- ปุ่มเปลี่ยนเป็น **"ตัดคอร์ส" / "ยกเลิก" / "เลื่อน"** ทุกที่ (แทน เข้าเรียน/ลา/งดสอน) · ยกเลิก = ไม่ไปรอบนี้ ไม่หัก · เลื่อน = ไม่หักตอนนี้ แล้วเลือกวันใหม่ หรือเลือก "ยังไม่รู้วัน" · ตัดคอร์สเปิดให้กด 24 ชม. ก่อนเริ่ม — `handoff-2026-11-11-evening.md:61` · ตรงกับโค้ด CT046 `V3:drizzle/0030_occurrences.sql` ข้อ 4 (`start − 24 ชม.`) **[repo]**
- P4f: เลื่อนไปได้ไม่เกิน 12 เดือน · ความยาวเท่าเดิม · LINE เตือน 1 ครั้งหลัง N วันสำหรับ "รอนัดใหม่" (luffy ตั้ง N = 7) · มี "เพิ่มนัดพิเศษ" · ห้ามวันใหม่หลังวันหมดอายุคอร์ส — `handoff:83,90`
- ตาราง: ไม่เกิน 7 แถวต่อคอร์ส (E6=2) — `handoff:84`
- rich menu ปุ่ม 4 = "ตารางนัด" → หน้า `/schedule` แบบปฏิทิน (สัปดาห์เริ่มวันจันทร์) — `handoff:56,59` · ⚠️ ไฟล์ `V3:ops/richmenu/richmenu-v3.json` บน trunk **ยังเขียนว่า "ตารางเรียน"** การเปลี่ยนชื่ออยู่ใน commit `8a914611` ของ `franky-v3-p4d3` ที่ยังไม่ merge และยังไม่ได้อัปโหลดขึ้น OA **[repo]**
- G14 = 1: เลิกใช้ปุ่ม "วันนี้" (`menu:today`) ของเมนูแชทเดิม — Mr.Foxx 2026-11-12 16:04 (`notes/10_daily/2026-11-12.md`) · ⚠️ บน trunk `V3:src/modules/line/menu.ts:49` ยังมี `menu:today` อยู่ และ `V3:docs/CUTOVER-V3.md` แถว G14 ยังเขียนว่า "⏳ รอตัดสิน" **[repo]**

## 6. จุดที่เบี่ยงจาก spec (ตั้งใจ · บันทึกไว้แล้ว)
| spec | ที่ทำจริง | ที่มา |
|---|---|---|
| ปุ่ม เข้าเรียน/ลา/งดสอน (§03/§04/§07) | ตัดคอร์ส/ยกเลิก/เลื่อน + นัดพิเศษ one-off (ขยายความ unique ของ occurrence ใน §06) | `handoff:65` |
| ราคามีรายเดือน (§05) | 6/12 เดือนเท่านั้น · ราคายังไม่ตัดสิน | `handoff:48` |
| Free เสนออัปเกรดเมื่อเกิน 5 คอร์ส (§11) | ข้อความเพดานแบบ "ไม่ขายของ" (`cap.free.prebilling` / `cap.paid.noupsell`) ช่วงก่อนเปิดรับเงิน | commit `5af96675` (PL-A6) |
| schema แยกตาราง users/courses/... (§06) | ใช้ตารางเดิมของ v2: `classes` = คอร์ส · `class_sessions` (source=scheduled) = occurrence · `households` (สมาชิก 1 คน) = บัญชี | `V3:drizzle/0027_course_model.sql:5-7` · `0029:3` · `0030:6` **[repo]** |
| `POST /webhooks/line` (§07) | `POST /api/line/webhook` | `V3:src/app/api/line/webhook/route.ts` **[repo]** |
| spec §08 เสนอ Fastify/NestJS หรือ FastAPI | Next.js 16 (route handlers) + worker แยก | `V3:package.json` **[repo]** |

## 7. คำตัดสินสำคัญล่าสุด (2026-11-12)
- **ไม่ใช้ข้อมูลเก่า**: "เรื่องข้อมูลเก่าไม่ต้องใช้ก็ได้เดี๋ยวกรอกใหม่เอง" → v3 เริ่มที่ฐานใหม่ว่างเปล่า · ตัด lane P3 (แปลงข้อมูล) — `handoff-2026-11-11-evening.md:102-103` · แผน nami `classtracking/research/nami-cutover-fresh-db-plan-2026-11-12.md` (ทางเลือก F1 = drop + create ฐานใน volume `ctnext_pgdata` เดิม · rollback = `pg_restore` V2_FINAL + image 0025b) **[repo]**
- Q-F1 = 1: สมาชิกคนที่ 2 ของ v2 ยังไม่ใช้ v3 — `handoff:105`
- G14 = 1 (เลิกปุ่ม "วันนี้") · อนุมัติ merge P5b-UI → trunk `b84c6f4a` — `notes/10_daily/2026-11-12.md` (16:04)
