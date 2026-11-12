> **ฉบับเว็บ (public):** ปิด IP ของ VPS · path ไฟล์ secret · tunnel id · ssh key ไว้ เนื้อหาอื่นตรงกับต้นฉบับในเครื่อง (`classtracking/handoff/2026-11-12/`) · ไม่มีค่า secret ใดๆ ในชุดนี้

# ชุดส่งต่อเพื่อให้ตรวจจากภายนอก — classtracking · 2026-11-12

> **ถึงผู้ตรวจ (ChatGPT desktop หรือคนอื่น):** ไฟล์นี้อ่านจบได้ในตัวเอง ใช้เป็นจุดเริ่ม แล้วค่อยเปิดไฟล์อื่นในโฟลเดอร์นี้
> เป้าหมายคือ **ตรวจเองแบบอิสระว่าข้อเท็จจริงในชุดนี้ถูกต้องไหม** โดยเปิดไฟล์ต้นทางที่อ้างไว้ทุกข้อ
> ผู้เขียน: franky (agent เขียนโค้ดของทีม x-team) · สั่งงานโดย luffy (เลขา/ผู้ประสานงานของ Mr.Foxx) · เขียน 2026-11-12
> ชุดนี้เป็น **เอกสารอย่างเดียว** ไม่มีการแก้โค้ด · ไม่มีค่า secret ใดๆ (มีแค่ชื่อไฟล์และชื่อตัวแปร env)

---

## 1. ผลิตภัณฑ์คืออะไร (สรุปย่อหน้าเดียว)

**"ตัดคอร์ส" (classtracking v3 · ชื่อเดิมของ spec: LINE Course Tracker)** เป็นระบบผ่าน LINE ให้คนที่ซื้อคอร์สแบบจ่ายล่วงหน้า (เรียนพิเศษลูก นวด ฟิตเนส ว่ายน้ำ เปียโน ล้างรถ ฯลฯ) ติดตามยอดคงเหลือของคอร์สตัวเอง แล้ว "ตัดยอด" ด้วยการกดยืนยันเอง โดยไม่มีการตัดอัตโนมัติ คอร์สมี 3 แบบ: นับครั้ง (SESSION) · นับชั่วโมง (DURATION) · เครดิตมูลค่าเงิน (VALUE) ใช้ 1 LINE user = 1 บัญชี ผู้ใช้ทำงานผ่าน rich menu, Flex message และแอป LIFF ส่วนแผนแบ่งเป็น Free / Pro / Max
ที่มา: spec `classtracking/research/course_tracker_handoff-2026-11-10.html` §01–§02 · ร่าง PRODUCT `classtracking/research/PRODUCT-v3-draft.md:13-18`

**ประวัติ:** ระบบนี้เริ่มเป็น v2 "บัตรตัดชั่วโมง" (Dance Class Hour Tracker · ครัวเรือนที่มีสมาชิกหลายคน · ใช้ LINE + Telegram) ซึ่ง **ตอนนี้ LIVE อยู่** บน VPS แล้ว Mr.Foxx สั่งเปลี่ยนทิศเมื่อ 2026-11-10 ให้ทำ v3 ตาม spec ใหม่ และกำหนดว่า **spec ใหม่ชนะทุกข้อที่ขัดกับคำตัดสินเก่า** (commit `2d2f1e3d` และ `0acd5257` · ไฟล์ memory `classtracking-v3-spec-authoritative.md`)
v3 **ยังไม่ deploy** และยังพัฒนาอยู่บน branch `ct-v3-course-tracker`

| ประเด็น | สถานะ ณ 2026-11-12 | ที่มา |
|---|---|---|
| ระบบที่ LIVE | v2 release 0025b · image `ctnext-app:944f273d30c1` · journal 26 migration (0000–0025) · `LAUNCH_MODE=staging` (ช่วงทดลองของแอดมิน 1 ครัวเรือน) | luffy ตรวจบน VPS 2026-11-12 · `classtracking/CLAUDE.md` §สถานะ |
| v3 | trunk `ct-v3-course-tracker` @ `b84c6f4a` · journal 35 migration (0000–0034) · ยังไม่ deploy · cutover จะใช้ **ฐานข้อมูลใหม่ว่างเปล่า** (ไม่ย้ายข้อมูลเก่า) | `git log -1 ct-v3-course-tracker` · `drizzle/meta/_journal.json` · `classtracking/research/nami-cutover-fresh-db-plan-2026-11-12.md` |
| รับชำระเงิน | ยังไม่เปิด · ราคา Pro/Max แบบ 6/12 เดือนยังไม่ได้ตัดสิน | `classtracking/handoff-2026-11-11-evening.md:48` |
| ลูกค้าจริง | ยังไม่มี · ยังไม่เคยไปคุยกับโรงเรียน/ร้านจริงสักแห่ง | `classtracking/CLAUDE.md:82` |

---

## 2. วิธีอ่านชุดนี้

### 2.1 ตำแหน่งไฟล์ (path ที่อ้างในชุดนี้)
- **ROOT** = `/Users/tummac/Claude_Project/x-files` (git repo หลัก · branch `main`)
- path ที่ขึ้นต้นด้วย `classtracking/...` หรือ `notes/...` = อยู่ใต้ ROOT บน branch `main` (เอกสาร แผน research เนื้อหากฎหมาย)
- **โค้ด v3** = `ROOT/.claude/worktrees/ct-v3-merge/classtracking/app/...` (worktree ของ branch `ct-v3-course-tracker`) ในชุดนี้ย่อเป็น **`V3:`** เช่น `V3:drizzle/0030_occurrences.sql`
  - ⚠️ worktree นี้ขยับตาม trunk ได้ ถ้าจะอ่านให้ตรงกับที่เขียนไว้ ใช้ `git -C ROOT show b84c6f4a:classtracking/app/<path>`
- โค้ดของ lane ที่ยังไม่ merge อ้างเป็น `branch@sha:path` เช่น `franky-v3-pl-a@4c9d9570:classtracking/app/drizzle/0035_pl_a_plans.sql` (อ่านด้วย `git show <sha>:<path>`)
- **โค้ด v2 ที่ LIVE** = tag `ct-v2-live-0025b` (= commit `16ad44a5`)

### 2.2 ป้ายความน่าเชื่อถือ
- **[repo]** = franky เปิดไฟล์นั้นใน repo แล้วยืนยันเองตอนเขียน (มี path:line)
- **[VPS-luffy]** = luffy ตรวจบน VPS แบบอ่านอย่างเดียวเมื่อ 2026-11-12 แล้วส่งมาให้ franky (franky ไม่ได้เข้า VPS เอง)
- **[รายงาน]** = มาจากรายงานของ agent อื่น (chopper/jinbe/law/zoro/nami) ที่เก็บใน `classtracking/research/` ซึ่ง franky ไม่ได้รันซ้ำ
- **[ยังไม่ยืนยัน]** = ยังไม่มีหลักฐานที่ตรวจได้ หรือเป็นข้อสันนิษฐาน **ผู้ตรวจควรสงสัยข้อเหล่านี้เป็นพิเศษ**

### 2.3 ลำดับการอ่านที่แนะนำ
1. README นี้
2. `01-product-and-decisions.md` (ผลิตภัณฑ์ · คำตัดสิน · **วันหมดอายุ 2 แบบ**)
3. `02-architecture-and-code.md` → `03-deploy-and-infra.md`
4. `04-status-lanes-and-open-items.md` → `05-legal-copy-status.md`
5. `06-verification-checklist.md` (รายการข้อเท็จจริงพร้อมคำสั่งพิสูจน์ · ใช้ข้อนี้ทำงานตรวจจริง)

---

## 3. ดัชนีไฟล์

| ไฟล์ | เนื้อหา |
|---|---|
| [01-product-and-decisions.md](01-product-and-decisions.md) | spec v3 · ชนิดคอร์ส · แผน Free/Pro/Max · ปุ่มตอบ · **วันหมดอายุ 2 แบบ (แผน กับ คอร์ส)** · คำตัดสินของ Mr.Foxx · จุดที่เบี่ยงจาก spec |
| [02-architecture-and-code.md](02-architecture-and-code.md) | stack · module · DB schema/migration · ledger append-only · RLS/DEFINER · LINE OA + LIFF flow · worker job |
| [03-deploy-and-infra.md](03-deploy-and-infra.md) | สิ่งที่ deploy อยู่และอยู่ที่ไหน (VPS · container · tunnel · backup · ของ v1 ที่ค้าง) |
| [04-status-lanes-and-open-items.md](04-status-lanes-and-open-items.md) | สถานะแต่ละ lane ของ v3 · ลำดับ merge · ข้อที่ยังเปิด · ความเสี่ยงที่รู้อยู่ / สิ่งที่ยังไม่แน่ใจ |
| [05-legal-copy-status.md](05-legal-copy-status.md) | สถานะ terms / privacy / FAQ / copy ของ v3 |
| [06-verification-checklist.md](06-verification-checklist.md) | ข้อเท็จจริงที่ผู้ตรวจควรตรวจ แต่ละข้อมีไฟล์หรือคำสั่งที่ใช้พิสูจน์ |

---

## 4. ความเสี่ยงหลักที่ควรรู้ก่อนอ่าน (รายละเอียดอยู่ใน 04 §5)
- ยังไม่ได้ probe รูปแบบ response จริงของ LINE และ Telegram: `LINE_RESPONSES_PROBED = false` (`V3:src/modules/notifications/line.ts:16`) · `TELEGRAM_RESPONSES_PROBED = false` (`V3:src/modules/telegram/api.ts:24`) **[repo]**
- ยังไม่เคยตรวจกับตลาดจริง (โรงเรียนหรือร้าน) แม้แต่แห่งเดียว **[repo: classtracking/CLAUDE.md]**
- ตัวเลข "55–60%" ในหน้าสรุปผู้บริหารเป็นสูตรที่ทีมกำหนดเอง ไม่ได้วัดจริง (`classtracking/summary/exec/index.html:809-812`) **[repo]**
- ข้ออ้างเรื่องราคา LINE ปี 2027 (Reply API เริ่มคิดเงิน) **[ยังไม่ยืนยัน]** กับประกาศทางการฉบับล่าสุด (`classtracking/summary/exec/index.html:926` เขียนกำกับไว้เองว่ายังไม่ได้ตรวจ)
- runbook cutover ใน trunk (`V3:docs/CUTOVER-V3.md`) **ยังเป็นฉบับก่อนตัดสินใจใช้ฐานใหม่** (ยังเขียนถึง P3/0035 convert) ฉบับแก้ (lane Y7) ยังไม่ได้ทำ
