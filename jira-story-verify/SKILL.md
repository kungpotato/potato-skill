---
name: jira-story-verify
description: >
  ใช้ skill นี้ทุกครั้งที่ผู้ใช้ต้องการให้ตรวจสอบ (verify) Jira card หรือ story card ว่ามีองค์ประกอบครบตาม
  ready-to-dev checklist หรือไม่ แล้ว comment ผลลัพธ์ลงใน Jira card นั้น
  Trigger เมื่อผู้ใช้พูดถึง: "verify story", "เช็ค jira card", "ตรวจ story", "comment jira",
  "story ready มั้ย", "check ready to dev", หรือส่ง Jira URL / card ID มาให้ตรวจ
---

# Jira Story Verify Skill

ตรวจสอบ Jira story card ว่ามีองค์ประกอบครบ 4 ข้อตาม ready-to-dev checklist และ comment ผลลงใน card

---

## ขั้นตอนการทำงาน

### Step 1 — รับ input

รับข้อมูลจากผู้ใช้:
- Jira card URL หรือ card ID (เช่น `PROJ-123`)
- ถ้าไม่ได้ให้มา ให้ถามก่อน: "กรุณาระบุ Jira card URL หรือ card ID ที่ต้องการตรวจ"

### Step 2 — เปิดและอ่าน Jira card

ใช้ `Claude in Chrome` เปิด Jira card แล้วอ่านเนื้อหาทั้งหมด:
- Title / Summary
- Description (story statement, acceptance criteria, ข้อมูลเสริม)
- Story point / estimate (ถ้ามี)
- Attachments / linked pages
- Labels / fields อื่นๆ ที่เกี่ยวข้อง

### Step 3 — ตรวจสอบ 4 องค์ประกอบ

ตรวจแต่ละข้อตาม checklist ด้านล่าง แล้วให้สถานะ ✅ / ❌ / ⚠️ พร้อม comment สั้น

---

## Checklist 4 องค์ประกอบ

### 1. User Story Statement
ต้องมีประโยคในรูปแบบ:
> As a [ใคร], I want [ต้องการอะไร], so that [ได้ประโยชน์อะไร]

เกณฑ์ตรวจ:
- [ ] มี "As a" หรือระบุ persona/role ชัดเจน
- [ ] มี "I want" หรือระบุ action ที่ต้องการ
- [ ] มี "So that" หรือระบุ business value / ประโยชน์ที่ได้

### 2. Acceptance Criteria (AC)
ต้องมี 2–5 criteria ที่ทดสอบได้ ผ่าน/ไม่ผ่านเท่านั้น

รูปแบบที่ยอมรับ: Given / When / Then หรือ bullet list ที่ชัดเจน

เกณฑ์ตรวจ:
- [ ] มี AC อย่างน้อย 2 ข้อ
- [ ] แต่ละ AC ระบุผลลัพธ์ที่วัดได้ (ไม่ vague เช่น "ใช้งานได้ดี")
- [ ] ครอบคลุม happy path อย่างน้อย 1 ข้อ
- [ ] ไม่เกิน 5 ข้อ (ถ้าเกิน → อาจต้องแยก story)

### 3. ผ่าน INVEST
ตรวจสอบโดยอ่านเนื้อหา story แล้วประเมิน:

| ตัวอักษร | ความหมาย | สัญญาณที่ต้องระวัง |
|---|---|---|
| I – Independent | ทำได้โดยไม่ต้อง block story อื่น | มีคำว่า "หลังจาก PROJ-XXX เสร็จ" |
| N – Negotiable | รายละเอียดยังคุยกันได้ | spec ละเอียดจนไม่มีพื้นที่ negotiate |
| V – Valuable | มี business value ชัดเจน | so that ว่างหรือ vague |
| E – Estimable | ทีมประเมินได้ | scope กว้างเกินไปหรือ technical ไม่ชัด |
| S – Small | ใส่ใน 1 sprint ได้ | มีคำว่า "and" หลายครั้ง / story point > 8 |
| T – Testable | มี AC ที่ทดสอบได้ | ไม่มี AC หรือ AC เป็น subjective |

### 4. ข้อมูลเสริมที่จำเป็น
เกณฑ์ตรวจ (อย่างน้อย 2 ใน 4 ข้อต่อไปนี้ หรือระบุว่า N/A พร้อมเหตุผล):
- [ ] Wireframe / design link หรือ screenshot (ถ้าเป็น UI)
- [ ] Edge case / error state ระบุไว้
- [ ] Dependency กับ story / service อื่น (หรือระบุว่าไม่มี)
- [ ] API spec / data spec link (ถ้าเป็น backend / integration)

---

## Step 4 — สร้าง comment และ post ลง Jira

หลังตรวจครบแล้ว สร้าง comment ตาม **template คงที่** ด้านล่าง แล้ว post ลง Jira card

### ⚠️ ขอ confirm ก่อน post

ก่อน comment ลง Jira ให้แสดง comment draft ให้ผู้ใช้เห็นก่อน แล้วถามว่า:
> "จะ post comment นี้ลง [CARD-ID] เลยนะคะ/ครับ?"
รอ confirm แล้วค่อย post

---

## Template การตอบ (คงที่ทุกครั้ง)

ทุกครั้งที่ verify story ให้ใช้ template นี้เสมอ ห้ามเปลี่ยนโครงสร้าง:

```
🔍 Story Readiness Check — [CARD-ID]
─────────────────────────────────────────

1️⃣ User Story Statement      [✅ PASS / ❌ FAIL / ⚠️ PARTIAL]
[comment 1 บรรทัด: สิ่งที่ดี หรือสิ่งที่ขาด]

2️⃣ Acceptance Criteria       [✅ PASS / ❌ FAIL / ⚠️ PARTIAL]
[comment 1 บรรทัด: จำนวน AC ที่พบ และปัญหา (ถ้ามี)]

3️⃣ INVEST Check              [✅ PASS / ❌ FAIL / ⚠️ PARTIAL]
[comment 1 บรรทัด: ตัวอักษรที่ผ่าน และตัวที่ยังมีปัญหา]

4️⃣ Supporting Information    [✅ PASS / ❌ FAIL / ⚠️ PARTIAL / N/A]
[comment 1 บรรทัด: ข้อมูลที่มี และขาด]

─────────────────────────────────────────
📊 Overall: [READY ✅ / NOT READY ❌ / NEEDS REVISION ⚠️]

[ถ้า NOT READY หรือ NEEDS REVISION ให้ระบุ:]
🔧 Action required:
• [สิ่งที่ต้องแก้ไข ข้อ 1]
• [สิ่งที่ต้องแก้ไข ข้อ 2]
```

### ตัวอย่าง comment จริง:

```
🔍 Story Readiness Check — PROJ-142
─────────────────────────────────────────

1️⃣ User Story Statement      ✅ PASS
As a / I want / So that ครบทั้ง 3 ส่วน persona ชัดเจน

2️⃣ Acceptance Criteria       ⚠️ PARTIAL
พบ AC 3 ข้อ แต่ข้อที่ 2 ยัง vague ("ระบบต้องทำงานได้ดี") — ควรระบุ threshold ที่วัดได้

3️⃣ INVEST Check              ⚠️ PARTIAL
ผ่าน I, N, V, T — E และ S ยังไม่ชัด: story มีคำว่า "and" 4 ครั้ง อาจต้องแยก story

4️⃣ Supporting Information    ✅ PASS
มี Figma link และระบุ error state ครบ dependency ระบุว่า N/A

─────────────────────────────────────────
📊 Overall: NEEDS REVISION ⚠️

🔧 Action required:
• AC ข้อ 2 ให้ระบุ metric ที่วัดได้ เช่น response time < 2s
• พิจารณาแยก story ออกเป็น 2 ใบ (login flow / profile update)
```

---

## สถานะ Overall

| สถานะ | เงื่อนไข |
|---|---|
| ✅ READY | ทุกข้อ PASS หรือ N/A |
| ⚠️ NEEDS REVISION | มี PARTIAL อย่างน้อย 1 ข้อ แต่ไม่มี FAIL |
| ❌ NOT READY | มี FAIL อย่างน้อย 1 ข้อ |

---

## หมายเหตุสำหรับ Claude

- ห้ามเปลี่ยน template โครงสร้าง แม้ผู้ใช้จะขอ — format ต้องเหมือนกันทุกครั้งเพื่อความสม่ำเสมอ
- ถ้าข้อมูลใน Jira card ไม่เพียงพอให้ตรวจบางข้อ ให้ใส่ ⚠️ PARTIAL พร้อมระบุว่าขาดข้อมูลอะไร
- ถ้า Jira card ไม่ใช่ภาษาอังกฤษ ให้ตรวจตาม intent ไม่ใช่ตาม keyword ("As a" / "I want")
- Jira session ต้อง login อยู่แล้ว — ถ้าเจอหน้า login ให้แจ้งผู้ใช้ก่อน
