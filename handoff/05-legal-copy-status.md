# 05 · สถานะเอกสารกฎหมายและ copy

> กระบวนการ: usopp ร่าง → law ตรวจข้อเท็จจริง/กฎหมาย → zoro ตรวจคุณภาพภาษา → luffy ใส่ธง publish → สร้างหน้าในแอปด้วย `V3:scripts/gen-content.ts` (FAQ: `content/faq/FAQ.th.v3-draft8.md` frontmatter `dest`)
> **ทุกไฟล์ร่าง v3 ยังเป็น `publish: draft`** **[repo]** · ยังไม่มีฉบับ v3 ใดที่ผู้ใช้เห็นจริง
> ⚠️ law ไม่ใช่ทนายความจริง เป็น agent ตรวจข้อเท็จจริงของทีม **ยังไม่มีทนายความมนุษย์ตรวจเอกสารชุดนี้** **[ยังไม่ยืนยันว่ามีหรือไม่ · ไม่พบหลักฐานใน repo]**

---

## 1. ที่ LIVE ตอนนี้ (v2)
| เอกสาร | ฉบับ | ที่มา |
|---|---|---|
| ประกาศความเป็นส่วนตัว | 1.1.0 · มีผล 2026-11-08 | `classtracking/FIRST-VERSION-SUMMARY.md:593` · `content/legal/privacy-notice.th.v1.md` |
| ข้อกำหนดการใช้งาน | 1.0.0 (ฉบับ TRIAL) · มีผล 2026-11-04 | `FIRST-VERSION-SUMMARY.md:594` · `content/legal/terms.th.v1.md` |
| Telegram consent | 1.1.0 | `FIRST-VERSION-SUMMARY.md:596` |
(ข้อมูล LIVE ชุดนี้มาจากสรุปของ 11-09 · franky ไม่ได้เปิดดูหน้าเว็บจริง **[ยังไม่ยืนยัน ณ 11-12]**)

## 2. ร่าง v3 (ล่าสุด)
| เอกสาร | ไฟล์ล่าสุด | สถานะ | ที่มาของสถานะ |
|---|---|---|---|
| ข้อกำหนด 2.0.0 | `classtracking/content/legal-v3/terms.th.v2.0.0-draft22.md` (488 บรรทัด) | **law PASS** (T22) · ในไฟล์เขียนว่า "ยังไม่ผ่าน law/zoro รอบนี้" (เขียนก่อน law ตรวจ) · ไม่พบรายงาน zoro ของ d22 | `research/law-t22-p21-2026-11-11.md` · frontmatter บรรทัด 7 ของไฟล์ |
| ประกาศความเป็นส่วนตัว 2.0.0 | `classtracking/content/legal-v3/privacy-notice.th.v2.0.0-draft22.md` (528 บรรทัด) | d21 law PASS (T22/P21) · d22 law PASS (account-tag d2 · ให้ลบ ❓ ที่ :490) · ไม่พบรายงาน zoro ของ d21/d22 | `research/law-t22-p21-2026-11-11.md` · `research/law-account-tag-d2-p22-2026-11-11.md` |
| FAQ v3 | `classtracking/content/faq/FAQ.th.v3-draft8.md` (805 บรรทัด) | law นับเป็น release-candidate · ในไฟล์เขียนว่า "ยังไม่ผ่าน law/zoro รอบนี้" | `law-t22-p21-2026-11-11.md` บรรทัด "Status" · frontmatter :7 |
| กล่องสมัคร v3 | `classtracking/content/legal-v3/signup-box.th.v2.0.0-draft1.md` | ร่างแรก | — |
| copy ใน LINE/แอป | `classtracking/content/v3-copy/line-flows-copy-draft18.md` + addendum (P4cUI-PL d10 · P4f-buttons d9 · P5d d3 · account-tag d2 · P4d-calendar d1 · P5c1-keys d1 · oa-migration d1) | d18 "ยังไม่ผ่าน law/zoro รอบนี้" (frontmatter) | ไฟล์เอง |

ข้อสรุปของ law (`research/law-t22-p21-2026-11-11.md`): **"terms 2.0.0 d22 + privacy 2.0.0 d21 + FAQ v3 d8 = release-candidate texts (pending R4 confirmation + gates)"** · R4 (terms ข้อ 13: บัญชี comp ที่ไม่มีช่วงจ่ายเงินต้องยอมรับฉบับใหม่แบบ Free) Mr.Foxx ตอบ = 1 แล้ว (`handoff-2026-11-11-evening.md:100`)

## 3. เงื่อนไขก่อนเผยแพร่ (release conditions) ที่ยังไม่ครบ
- ข้อความเรื่อง "แถบเตือนวันหมดอายุในแอป" และ "เตือนก่อนคอร์สหมดอายุ" ต้องมี P5b + P5c-1 LIVE ก่อน (terms d22 CHANGES :201 · L-0123) → **merge แล้วใน trunk แต่ยังไม่ LIVE**
- privacy :294 "คุณปิดได้ที่หน้าบัญชี" ต้องรอสวิตช์ P5b-UI live · :443 ตัวยึดชื่อปุ่ม (privacy d22 :39)
- privacy 6B (ขั้นยืนยันตัวตนเมื่อขอใช้สิทธิ์) สัญญาว่ามี code ทางอีเมล แต่โค้ดยังไม่มี → jinbe J1 (High) ให้เลือก (ก) ทำ flow code หรือ (ข) ให้ law เขียน 6B ใหม่ให้ตรงกับที่ระบบทำได้จริง (`research/jinbe-legalpub-2026-11-12.md`)
- ตัวสร้างหน้า (LEGAL-PUB B1) ยัง NEEDS-FIX: บล็อก team-note ที่ปิดไม่สนิทกินเนื้อหาเงียบ (B1-1) · regex ไม่จับ `BILLING_ONLY` → ข้อความ billing อาจหลุดขึ้นหน้า (B1-2) (`research/chopper-legalpub-2026-11-12.md`)
- `--strict` dry-run ยัง rc 1 เพราะมี label ค้าง (privacy 3, 6B, ลบคอร์สเดียว, ลบชื่อร้านหรือครู, 7 · terms 9, 10) (`chopper-legalpub-2026-11-12.md`)
- ต้องแก้ข้อความตามแผนฐานว่าง (ไม่มีบัญชีที่ย้ายมา): privacy d22 :407 ตัดวลี "ถูกแยกออกจากบัญชีที่ใช้ร่วมกันในระบบรุ่นก่อน" · terms d22 :483 "ผู้ใช้เดิมที่ยอมรับฉบับ 1.x…" ให้ law เลือกตัดหรือคงแบบทั่วไป · line-flows d18 ลบ key `migrate.negative.notice` (`research/nami-cutover-fresh-db-plan-2026-11-12.md:19-20`)
- Telegram: `TELEGRAM_ADMIN_CHAT_IDS` + `TELEGRAM_BOT_TOKEN` ตั้งไว้บน ctnext → privacy ต้องระบุ Telegram สำหรับ alert ของแอดมิน (`handoff-2026-11-11-evening.md:55`)
- ราคาเป็น `{price}` และวันมีผลเป็น `{EFFECTIVE_DATE}` (frontmatter terms/privacy :5) → ต้องเติมก่อนเผยแพร่

## 4. สิ่งที่ผู้ตรวจภายนอกควรดู
- เทียบสิ่งที่ terms/privacy สัญญากับโค้ดจริง โดยเฉพาะ: เตือน 14/3 วัน (โค้ด `V3:src/server/db/enums.ts:82`) · ระยะเก็บ 90/30 วัน (`V3:src/modules/privacy/retention.ts:17-20`) · Free ไม่มี LINE push (`V3:src/modules/entitlement/push-plan.ts:3-4`) · ไม่ freeze (PL-A ยังไม่ merge · ดู 01 §4.1)
- ข้อความเรื่อง Free ใน terms d22 CHANGES :15, :50 ตรงกับคำตัดสิน `handoff:42` หรือไม่
