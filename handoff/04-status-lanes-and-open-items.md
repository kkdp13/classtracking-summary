# 04 · สถานะ lane ของ v3 · ข้อที่ยังเปิด · ความเสี่ยง

> ภาพ ณ 2026-11-12 บ่าย (หลัง luffy dispatch 16:04 · `notes/10_daily/2026-11-12.md`) · **สถานะเปลี่ยนเร็วมาก** (branch ของ lane ถูก push ต่อระหว่างที่เขียนไฟล์นี้) SHA ข้างล่างคือค่าที่ franky เห็นตอนเขียน
> ลำดับขั้นของทุก lane: franky เขียน → chopper รีวิว → jinbe ตรวจอิสระ → luffy rebase แล้วรันเทสต์เต็มบน HEAD ที่ rebase → ff-merge (กฎ L-0175 · `classtracking/handoff-2026-11-11-evening.md:68`)
> ⚠️ `notes/99_system/BOARD.md` แถว W-22 **ยังไม่ได้อัปเดตเป็นสถานะของ 11-12** (ข้อความล่าสุดในแถวเป็นของ 11-11) — แหล่งที่สดกว่าคือ handoff 11-11 evening + รายงานใน `classtracking/research/*-2026-11-12.md` + บันทึกประจำวัน 11-12

---

## 1. trunk
`ct-v3-course-tracker` @ **`b84c6f4a`** (เพิ่ง ff merge P5b-UI) · journal 35 (0000–0034) **[repo]**
merge แบบ first-parent ล่าสุด (`git log --first-parent ct-v3-course-tracker`): P5b-UI `b84c6f4a` ← P5c-1 `58b3de21` ← P4d-1/2 `39c4bdbd` ← P5b 0034 `e6c45341` ← P6 (`68d76e0a` ถอดแพ็ก Telegram + ย้าย bot token ออกจาก ct_app) ← Lane Y6 `b89b15a4` ← P4b-F `e6fd8e21` ← P5a 0033 `3ee7e1c4` ← Y5/Y4/Y3 ← TG-retire 0032 `06eeec44` **[repo]**

## 2. lane ที่กำลังทำ (ยังไม่ merge)
| lane | branch @ sha | เนื้องาน | สถานะ | ที่มา |
|---|---|---|---|---|
| **P4e-FU** | `franky-v3-p4efu` @ `1fb2d7b7` | ตามแก้ P4e (แก้ตาราง · cap 7 แถว · view 0036 · C1/L6) | chopper PASS (2 minor) · jinbe PASS · **กำลังทดสอบ merge candidate** (worktree `cand-p4efu`) | `research/chopper-P4eFU-2026-11-12.md` · `research/jinbe-P4eFU-2026-11-12.md` |
| **P4f-0** | `franky-v3-p4f0` @ `c031ab58` | ปุ่ม ตัดคอร์ส/ยกเลิก (`school_cancel` → alias ของ skip) · reversal facts | chopper NEEDS-FIX (B1 = merge แล้ว calendar.test ขัดกัน) · jinbe NEEDS-FIX (เฉพาะเรื่อง merge · security PASS) · **franky กำลังแก้** | `research/chopper-P4f0-2026-11-12.md` · `research/jinbe-P4f0-2026-11-12.md` |
| **LEGAL-PUB** | `franky-v3-legalpub` @ `227e553d` | B1 = gen-content รับ draft กฎหมาย v3 + dry-run · R5 = admin-cli `export-household` (สิทธิ์ขอสำเนาข้อมูล) | chopper NEEDS-FIX (B1-1, B1-2, R5-1) · jinbe NEEDS-FIX (J1 High: privacy 6B สัญญาเรื่อง code ทางอีเมลที่ยังไม่มีในโค้ด) · **franky กำลังแก้** | `research/chopper-legalpub-2026-11-12.md` · `research/jinbe-legalpub-2026-11-12.md` |
| **P5c-3** | `franky-v3-p5c3` @ `97b4b2f0` | ป้ายชื่อไม่บังคับจริง (`classes.kid_id` nullable) · cutover blocker | **chopper กำลังรีวิว** · copy ป้ายชื่อ zoro d2 PASS (F1 ต้องแก้ก่อนรีวิว P4c-UI) | `handoff-2026-11-11-evening.md:107` · `research/zoro-account-tag-d2-2026-11-12.md` |
| **P4d-3** | `franky-v3-p4d3` @ `8a914611` | หน้า `/schedule` ปฏิทิน + rich menu ปุ่ม 4 "ตารางนัด" (ยังไม่อัปโหลด) | **chopper กำลังรีวิว** | `handoff:106` |
| **PL-A** | `franky-v3-pl-a` @ `4c9d9570` (ฐาน `e6c45341` ต้อง rebase) | แผน plan_periods · เพดาน 5/20/60 · comp · ไม่ freeze · แจ้ง 14/7/จบ | **chopper กำลังรีวิว** · มีข้อรอ law (Q-L4 ระยะเก็บ · terms gate R1-b) | `handoff:109` · `franky-v3-pl-a@4c9d9570:.../drizzle/0035_pl_a_plans.sql` |
| **P5d** | `franky-v3-p5d` @ `774e40ed` (ฐาน `e6c45341`) | แจ้งยอดใกล้หมด (2 ครั้ง/2 ชม./<20% มูลค่า · LINE push แยก · ไม่แจ้งที่ 0) · cutover blocker | **chopper กำลังรีวิว** | `handoff:82,91,110` |
| **P4f-1/2** | `franky-v3-p4f12` @ `de2c072a` (ฐาน `58b3de21`) | เลื่อนนัด + นัดพิเศษ (DB + route) · CT059–CT064 ชั่วคราว | **chopper กำลังรีวิว** · gap ที่รู้: `RESPONSE_OF` ไม่มี `reschedule` | `handoff:111` |

## 3. lane ที่ยังไม่เริ่ม / อยู่ในคิว
| lane | เนื้องาน | ที่มา |
|---|---|---|
| **P4c-UI** | ฟอร์มเพิ่มคอร์ส v3 (3 ชนิด · ยอดตั้งต้น · ตาราง · เตือนรายคอร์ส) · **ต้องเสร็จก่อน cutover** (Mr.Foxx Q2) | `handoff:28` |
| **PL-B** | ชำระเงิน v3 · SKU · ส่วนลด/เครดิตเมื่อ Pro→Max · ต้องให้ law ตรวจ terms ข้อ 5 | `handoff:52,96-97` |
| **P3h** | ทำ schema ให้แน่นบนฐานว่าง (CHECK `hash_version = 2` · ถอด DEFAULT · ปิดทางเขียน v1 · ฯลฯ) | `research/nami-cutover-fresh-db-plan-2026-11-12.md:4,18` |
| **Y7** | เขียน CUTOVER-V3 ใหม่สำหรับฐานว่าง + เทสต์ runbook (อนุญาตคำสั่งทำลายได้ 1 จุดใน D3a) | `nami-cutover-fresh-db-plan:16-17,24` |
| **P4e-3 / P4f-3 / P4f-5** | UI แก้ตาราง · ปุ่มบนข้อความเก่าที่เลื่อนแล้ว · LINE เตือน "รอนัดใหม่" (cutover blocker ตาม Q-LB2) | `handoff:83,91,111` · `research/chopper-P4eFU-2026-11-12.md` |
| **P8** | เผยแพร่ terms/privacy/FAQ v3 ผ่าน gen-content (ก่อนมีลูกค้า) | `handoff:21` · ดู 05 |
| ctstg STG-4 | ขึ้น ctstg ด้วย image v3 → Mr.Foxx login บนมือถือ 1 ครั้ง → allowlist | `handoff:24` |

ลำดับ merge ที่วางไว้ (11-11 night ปรับด้วยแผนฐานว่าง): … → P4d → P4e → P4f-0/1 → P5c-3 → P4c-UI → PL-A → PL-B → **P3h → Y7** (`handoff:70` · `nami-cutover-fresh-db-plan:16`)

## 4. ข้อที่ยังเปิด (รอคนตัดสิน)
- ราคา Pro/Max 6/12 เดือน — Mr.Foxx (`handoff:48`) · เพดานความรับผิด terms ข้อ 11 = ราคา Max 12 เดือน (ใช้ 949 เป็นตัวยึดชั่วคราว) (`:50`)
- Q-L4 ระยะเก็บ `plan_periods` — law (`0035_pl_a_plans.sql:35`)
- ด่าน cutover ใน `V3:docs/CUTOVER-V3.md` §2 ที่ยังเปิด: G4 · G5 · G10 · G11b · G12 · G13 · G15–G17 (หลายข้อจะเปลี่ยนความหมายหลัง Y7) **[repo]** · G14 ตัดสินแล้ว (= 1 เลิกปุ่ม "วันนี้") แต่ไฟล์ยังไม่อัปเดต
- Q3 (ช่วงเวลา cutover) · Q5 · Q7 (ctstg หลัง cutover) · Q8 (โหมดหลัง cutover) — `CUTOVER-V3.md` §3
- trunk flake 2 จุดที่ขึ้นกับเวลาเที่ยงคืนไทย: `tests/integration/occurrences.test.ts` "schedule edit" และ `occurrence-guards.test.ts:129` (`handoff:99,104` · แก้แล้วใน `franky-v3-p4efu` และ `franky-v3-p4f0` แต่ยังไม่ merge) **[รายงาน]**
- jinbe P4e-FU: `menu.ts:96-106` (`menu:today`) อ่าน `classes.weekday/start_time` → ตัดสินแล้วว่าเลิกใช้ (G14) แต่ยังไม่มี lane ที่ถอดจริง **[รายงาน]**

## 5. ความเสี่ยงที่รู้อยู่ / สิ่งที่ยังไม่แน่ใจ
| # | เรื่อง | ทำไมถึงเสี่ยง | หลักฐาน |
|---|---|---|---|
| R1 | **ยังไม่ได้ probe รูปแบบ response จริงของ LINE/Telegram** | fixture ในเทสต์เขียนจากเอกสาร ถ้าของจริงต่างไป การจัดการ error/โควตาอาจผิด | `LINE_RESPONSES_PROBED = false` `V3:src/modules/notifications/line.ts:16` · `TELEGRAM_RESPONSES_PROBED = false` `V3:src/modules/telegram/api.ts:24` · `V3:src/modules/line/reply.ts:4` · `events.ts:8` · `line-quota-sync.ts:3` ("ยังไม่เคยเรียกของจริง") |
| R2 | **ยังไม่เคยตรวจกับตลาดจริง** | ไม่รู้ว่ามีคนยอมจ่ายจริงไหม · ขอบเขตเปลี่ยนจาก "ครอบครัว" เป็น "ขายให้มากที่สุด" (11-01) โดยไม่มีข้อมูลตลาดรองรับ | `classtracking/CLAUDE.md:82-83` |
| R3 | **ตัวเลข % ในหน้าสรุปผู้บริหารเป็นสูตรที่กำหนดเอง** | 100/80/40/30/0 ตามสถานะ เฉลี่ยเท่ากันทุกส่วน ไม่ถ่วงน้ำหนัก → "55–60%" ไม่ใช่การวัดจริง | `classtracking/summary/exec/index.html:305,809-812` |
| R4 | **ราคา LINE ปี 2027 ยังไม่ยืนยัน** | spec สมมติ "Reply Unlimited 2027 5,000 บาท/เดือน" (spec §10 เขียนว่า "ตรวจเอกสารทางการอีกครั้ง") · หน้าสรุปเขียนว่า Reply เริ่มคิดเงิน 2027-02-01 | spec §10 · `summary/index.html:1373,1399` · `summary/exec/index.html:926` (กำกับเองว่ายังไม่ตรวจ) |
| R5 | ราคา OA (ฟรี 300 ข้อความ · Basic 1,280 บาท) มาจากการค้นของ vegapunk 11-09 · เวลา/timezone ที่ตัวนับรีเซ็ต **ไม่พบประโยคทางการ** | ใช้คำนวณ guard 80% และต้นทุน | `classtracking/research/R-line-oa-pricing-2026-11-09.md:23-28,50` |
| R6 | runbook cutover ใน trunk ล้าสมัย (ยังเป็นแผนย้ายข้อมูล) | ถ้าใช้ผิดฉบับ ขั้นตอนจะไม่ตรงกับฐานว่าง | `V3:docs/CUTOVER-V3.md:1-4,36,63-82` vs `nami-cutover-fresh-db-plan-2026-11-12.md` |
| R7 | trunk ยัง freeze เมื่อสิทธิ์หมด (CT049) จนกว่า PL-A จะ merge | คำสัญญาใน terms ใหม่ ("ไม่ freeze") ยังไม่จริงบน trunk | `V3:drizzle/0028_ledger_units.sql:243-257` |
| R8 | เลข migration/SQLSTATE ของหลาย lane ชนกัน (0035 ถูกใช้โดย PL-A, P4f-1 และ P4e) | ต้อง renumber ตอน merge · เสี่ยงทำ snapshot/journal ผิด | `0035_pl_a_plans.sql:1` · `handoff:74,111` |
| R9 | privacy 6B สัญญาว่ามี code ยืนยันตัวตนทางอีเมล แต่โค้ดยังไม่มี (`pdpa-verify.ts:31` คืน null) | ถ้าเผยแพร่ข้อความนี้ จะไม่ตรงกับระบบจริง | `research/jinbe-legalpub-2026-11-12.md` J1 |
| R10 | hash chain ของ ledger ไม่มี anchor นอกฐาน · ลบแถวท้ายสายตรวจไม่เจอ | คนที่เขียน DB ได้ตรงๆ แก้ประวัติได้ (เขียนกำกับไว้ในโค้ดเอง) | `V3:drizzle/0003_ledger_guards.sql:8-11` |
| R11 | ทดสอบรับโหลด/ผู้ใช้ 100–500 คน (spec M6) ยังไม่ได้ทำ | ใช้จริงมีแค่ 1 ครัวเรือน | spec §11 · [VPS-luffy] |
| R12 | worktree/branch จำนวนมากเปลี่ยนพร้อมกัน | เอกสารชุดนี้อาจล้าสมัยภายในไม่กี่ชั่วโมง | ข้อสังเกตของ franky |
