---
name: subtask-creator
description: >
  ใช้ skill นี้ทุกครั้งที่ผู้ใช้ต้องการแตก Jira story ออกเป็น subtask ตามโครงสร้าง
  UI → Validate → Integrate ให้โดยอัตโนมัติ
  Trigger เมื่อผู้ใช้พูดถึง: "แตก subtask", "create subtask", "แบ่ง task", "breakdown story",
  "สร้าง subtask จาก jira", หรือส่ง Jira URL / card ID มาพร้อมขอให้แตก subtask
---

# Subtask Creator Skill

อ่าน Jira story แล้ววิเคราะห์และสร้าง subtask ตามโครงสร้าง 3 กลุ่ม: **UI → Validate → Integrate**

---

## โครงสร้าง Subtask ที่ต้องสร้าง

```
┌─────────────────────────────────────────────────────────────────┐
│                         Feature Story                           │
├──────────────────┬────────────────┬────────────────────────────┤
│       UI         │    Validate    │         Integrate           │
├──────────┬───────┼────────────────┼──────┬──────┬──────┬───────┤
│Subtask 1 │Subtask│   Subtask 3    │Sub 4 │Sub 5 │Sub 6 │Sub 7  │
│entry     │2 [UI] │form validate   │check │form  │form  │handle │
│point+    │widget │before submit   │alert │valid │valid │success│
│layout    │compo- │without api     │box   │w/api │after │dialog │
│          │nents  │check           │      │check │submit│       │
└──────────┴───────┴────────────────┴──────┴──────┴──────┴───────┘
```

### กลุ่ม 1: UI (Subtask 1–2)
- **Subtask 1** — Entry point + Layout: หน้าจอหลัก, routing, layout โครงสร้าง
- **Subtask 2** — [UI] Component: widget, card, form, alert box, error dialog, success dialog

### กลุ่ม 2: Validate (Subtask 3)
- **Subtask 3** — Form validate before submit **without** API check: validation rules ฝั่ง client เท่านั้น (required, format, length ฯลฯ)

### กลุ่ม 3: Integrate (Subtask 4–7)
- **Subtask 4** — Check to show alert box: logic ตรวจสอบ condition (kyc, permission ฯลฯ) เพื่อแสดง/ซ่อน alert
- **Subtask 5** — Form validate before submit **with** API check: validation ที่ต้องเรียก API (เช่น check duplicate, verify token)
- **Subtask 6** — Form validate after submit (inline + handle all error dialog): handle error response จาก API แสดง inline error และ error dialog
- **Subtask 7** — Handle success dialog: handle success response, แสดง success dialog, redirect หรือ callback

---

## ขั้นตอนการทำงาน

### Step 1 — รับ input

รับข้อมูลจากผู้ใช้:
- Jira story URL หรือ card ID (เช่น `PROJ-123`)
- ถ้าไม่ได้ให้มา ให้ถามก่อน: "กรุณาระบุ Jira story URL หรือ card ID ที่ต้องการแตก subtask"

### Step 2 — เปิดและอ่าน Jira story

ใช้ `Claude in Chrome` เปิด Jira card แล้วอ่านเนื้อหาทั้งหมด:
- Title / Summary
- Description: user story statement, acceptance criteria, รายละเอียด flow
- Attachments: wireframe, design link, spec
- Labels / fields ที่เกี่ยวข้อง (เช่น feature type, platform)

**Figma — บังคับตรวจทุกครั้ง:**
- ถ้ามี Figma link ใน Jira → ใช้ **Figma MCP** อ่าน design แล้วไป Step 2b
- ถ้าไม่มี Figma link → **ต้องถามผู้ใช้ก่อนเสมอ**: "มี Figma link สำหรับ story นี้ไหมคะ/ครับ?" รอคำตอบก่อนดำเนินการต่อ
  - ถ้าผู้ใช้ให้ link → ใช้ Figma MCP อ่านแล้วไป Step 2b
  - ถ้าผู้ใช้ยืนยันว่าไม่มี → ข้าม Step 2b และระบุ assumption ใน draft ว่า "ไม่มี Figma — component และ layout อ้างอิงจาก AC และ codebase pattern"

### Step 2b — Reconcile Figma กับ AC

ใช้ Figma MCP อ่าน design ทุก screen/state ที่เกี่ยวข้อง แล้วทำ **Figma ↔ AC Reconcile**:

1. **list screen/state ทั้งหมดใน Figma** (เช่น empty state, filled form, error state, success dialog)
2. **map แต่ละ screen → AC ข้อที่ cover**
3. ระบุ gap 2 ทิศทาง:

```
🎨 Figma ↔ AC Reconcile
──────────────────────────────────────────────
[Screen/State ใน Figma]         → AC ที่ cover
─────────────────────────────────────────────
Register form (empty)           → AC 1, AC 3
Register form (error — required)→ AC 4
KYC warning alert               → AC 2
Success dialog                  → AC 5

⚠️  Gap ที่พบ:
• Figma มี "loading skeleton" แต่ไม่มี AC ครอบคลุม → ควรเพิ่ม AC หรือแจ้ง PO?
• AC 6 (error กรณี network timeout) ไม่มี screen ใน Figma → design ยังไม่ครบ ต้องถาม designer
──────────────────────────────────────────────
```

4. **ถ้าพบ gap** ให้ตั้งคำถามถามผู้ใช้ในรอบถัดไปก่อนดำเนิน Step 3

### Step 3 — Scan Codebase เพื่อ Reconcile

ก่อนวิเคราะห์ subtask ให้ scan codebase เพื่อทำความเข้าใจบริบทของ project จริง:

**สิ่งที่ต้อง scan:**
- โครงสร้าง folder / module ที่เกี่ยวข้องกับ feature นี้
- Component ที่มีอยู่แล้วที่ reuse ได้ (form, dialog, alert, layout)
- Pattern การ validate ที่ใช้อยู่ใน project (เช่น zod, yup, react-hook-form)
- Pattern การ handle API error (error boundary, toast, dialog)
- Routing pattern ที่ใช้ (เช่น file-based, config-based)
- API client / service layer ที่มีอยู่ (เช่น axios instance, react-query, swr)
- Type / interface ที่เกี่ยวข้อง (เช่น User, KycStatus)
- State management pattern (redux, zustand, context)

**สิ่งที่ต้อง reconcile กับ Jira story:**
- Component ใน story ที่มีอยู่แล้ว vs ต้องสร้างใหม่
- Validation logic ที่คล้ายกันใน codebase ที่อาจ reuse ได้
- API endpoint ที่ story กล่าวถึง — มี type definition อยู่แล้วไหม?
- Success/error flow ที่ทำแล้วใน feature อื่น — pattern เหมือนกันไหม?

### Step 4 — วิเคราะห์และตั้งคำถามจนมั่นใจ 100%

หลัง scan codebase แล้ว ให้ map งานและระบุ **gap** ที่ยังไม่ชัดในแต่ละ subtask จากนั้น**ตั้งคำถามถามผู้ใช้เป็น round** จนกว่าทุก subtask จะมีข้อมูลเพียงพอ

**กฎการตั้งคำถาม:**
- ตั้งคำถามเฉพาะสิ่งที่ไม่สามารถอนุมานได้จาก Jira หรือ codebase
- รวม gap ทั้งหมดในแต่ละ round ให้เป็น **batch เดียว** ห้ามถามทีละข้อ
- ระบุให้ชัดว่าคำถามนั้นเกี่ยวกับ subtask ใด และ gap คืออะไร
- ถ้าคำตอบเปิด gap ใหม่ ให้ถาม round ถัดไปต่อทันที
- **หยุดถามเมื่อ**: ทุก subtask มี context ครบตามเกณฑ์ด้านล่าง

**หลัก Clean Architecture ที่ codebase ใช้อยู่ — ต้องเข้าใจก่อนตั้งคำถาม:**

codebase นี้ใช้ Clean Architecture ซึ่งมี separation of concerns ชัดเจน:
```
UI Layer → Entity (Domain) → Data Source (API/mock)
```
ดังนั้น:
- **ถ้ายังไม่มี API spec** → ห้ามถามหา ให้ define entity ตาม field ที่เห็นจาก UI ได้เลย
- เมื่อ API พร้อม developer จะ swap data source + data model แล้ว map กลับมาที่ entity เอง
- subtask ที่เกี่ยวกับ integration (5, 6, 7) ให้ระบุ **entity shape** และ **expected behavior** ไว้แทน endpoint จริง

**เกณฑ์ "มั่นใจ 100%" ของแต่ละ subtask:**

| Subtask | ต้องรู้อะไรก่อนจบงานได้ |
|---|---|
| 1 — Entry point + Layout | route path, layout ที่ใช้, navigation pattern, มีหน้าที่คล้ายกันใน codebase ไหม |
| 2 — UI Components | รายการ component ทั้งหมด, ของที่ reuse ได้ vs สร้างใหม่, design/Figma link |
| 3 — Client Validate | ทุก field + rule ครบ (required/format/length/match), validation library ที่ใช้ |
| 4 — Alert Box Logic | condition ทุกกรณีที่ trigger alert, field ใน entity ที่ใช้ตรวจ, behavior เมื่อ block vs warn |
| 5 — API Validate | **ถ้ามี API spec**: endpoint + response schema — **ถ้าไม่มี**: entity field ที่ต้อง validate + timing (onBlur/onSubmit) ก็เพียงพอ |
| 6 — Error Handling | **ถ้ามี API spec**: HTTP status + error shape — **ถ้าไม่มี**: error cases ที่ต้อง handle ตาม business logic + inline vs dialog |
| 7 — Success Flow | success condition, dialog content, action หลัง dismiss (redirect path / callback / refetch) |

**Format การถาม (คงที่ทุก round):**

```
🔍 ต้องการข้อมูลเพิ่มเติมเพื่อให้ subtask สมบูรณ์ — Round [N]
──────────────────────────────────────────────────────────────

[Subtask X — ชื่อ subtask]
❓ [คำถาม] เพราะ [gap ที่พบ / สิ่งที่ scan codebase ไม่พบ]

[Subtask Y — ชื่อ subtask]
❓ [คำถาม] เพราะ [gap ที่พบ]

──────────────────────────────────────────────────────────────
(กรุณาตอบให้ครบทุกข้อก่อนไปขั้นต่อไป)
```

### Step 5 — ตรวจ AC Coverage ก่อนสร้าง draft

ก่อนสร้าง draft ให้ทำ **AC Coverage Check** — ตรวจว่า subtask ทั้งหมดครอบคลุม Acceptance Criteria ทุกข้อจาก Jira story ครบ 100%

**วิธีตรวจ:**
1. list AC ทุกข้อจาก Jira story
2. map แต่ละ AC → subtask ที่รับผิดชอบ
3. ถ้า AC ข้อไหนไม่มี subtask รับ → ต้องแก้ไขก่อน (เพิ่มงานใน subtask ที่เหมาะสม หรือตั้งคำถาม round ใหม่)
4. **ห้ามสร้าง draft จนกว่า AC ทุกข้อจะมี subtask รับผิดชอบครบ**

**Format AC Coverage (แสดงก่อน draft เสมอ):**
```
✅ AC Coverage Check
──────────────────────────────────────────────
AC 1: [ข้อความ AC] → Subtask [X]
AC 2: [ข้อความ AC] → Subtask [X, Y]
AC 3: [ข้อความ AC] → ❌ ยังไม่มี subtask รับ → [วิธีแก้]
──────────────────────────────────────────────
Coverage: [N/N] AC ✅
```

ถ้า Coverage ไม่ครบ 100% ให้กลับไปถาม round ใหม่ก่อน ห้ามข้ามไป draft

### Step 6 — สร้าง subtask draft

เมื่อ AC Coverage = 100% แล้ว สร้างรายการ subtask ตาม template ด้านล่าง แสดงให้ผู้ใช้ review ก่อน
แต่ละ subtask ต้องมี:
- **งานที่ต้องทำ** (what to build)
- **AC ที่ subtask นี้รับผิดชอบ**
- **ของที่ reuse ได้จาก codebase** (ถ้ามี)
- **ของที่ต้องสร้างใหม่**
- **dependency** กับ subtask อื่น (ถ้ามี)

### Step 6 — ขอ confirm แล้วสร้างใน Jira

ก่อนสร้าง subtask ใน Jira ให้แสดง AC Coverage Check + draft และถามว่า:
> "จะสร้าง subtask เหล่านี้ใน [CARD-ID] เลยนะคะ/ครับ?"
รอ confirm แล้วค่อยสร้าง subtask ผ่าน Chrome โดย link เป็น child issue ของ story card

**หมายเหตุ**: ถ้าผู้ใช้แก้คำตอบหรือเพิ่มข้อมูลหลัง draft แล้ว ให้กลับไป update draft ก่อน ห้าม create Jira ทันที

---

## Template การตอบ (คงที่ทุกครั้ง)

```
📋 Subtask Breakdown — [CARD-ID]: [Story Title]
═══════════════════════════════════════════════

🎨 UI
──────────────────────────────────────────────
Subtask 1 — Entry point + Layout
  [อธิบายสั้น: หน้าที่ต้องสร้าง, routing path, layout structure]

Subtask 2 — [UI] Components
  [อธิบายสั้น: รายการ component ที่ต้องสร้าง เช่น form, card, alert box, dialog]

✅ Validate
──────────────────────────────────────────────
Subtask 3 — Form validate before submit (no API)
  [อธิบายสั้น: รายการ validation rules ฝั่ง client เช่น required, email format, min/max]

🔗 Integrate
──────────────────────────────────────────────
Subtask 4 — Check & show alert box
  [อธิบายสั้น: condition ที่ trigger alert เช่น kyc pending → แสดง warning]
  [หรือ N/A: ไม่มี condition alert ใน story นี้]

Subtask 5 — Form validate before submit (with API)
  [อธิบายสั้น: validation ที่ต้อง call API เช่น check duplicate email]
  [หรือ N/A: ไม่มี API validation ก่อน submit]

Subtask 6 — Form validate after submit (inline + error dialog)
  [อธิบายสั้น: error cases จาก API response เช่น 400 field error, 409 conflict]

Subtask 7 — Handle success dialog
  [อธิบายสั้น: success flow เช่น แสดง modal "บันทึกสำเร็จ" แล้ว redirect ไป /list]

═══════════════════════════════════════════════
📊 รวม [N] subtask [รายการที่ N/A ถ้ามี]
```

### ตัวอย่าง output จริง:

```
📋 Subtask Breakdown — PROJ-142: สร้างฟอร์มสมัครสมาชิก
═══════════════════════════════════════════════

🎨 UI
──────────────────────────────────────────────
Subtask 1 — Entry point + Layout
  สร้างหน้า /register, layout 2-column (form ซ้าย, summary ขวา), breadcrumb navigation

Subtask 2 — [UI] Components
  RegisterForm (card), InputField, PhoneInput, PasswordStrengthMeter,
  KycWarningAlert, TermsErrorDialog, SuccessDialog

✅ Validate
──────────────────────────────────────────────
Subtask 3 — Form validate before submit (no API)
  required: ชื่อ, นามสกุล, เบอร์, รหัสผ่าน
  format: เบอร์ 10 หลัก, password min 8 ตัว + uppercase + number
  match: password === confirmPassword

🔗 Integrate
──────────────────────────────────────────────
Subtask 4 — Check & show alert box
  ถ้า user มี kyc_status = "pending" → แสดง KycWarningAlert ก่อน submit ได้
  ถ้า kyc_status = "rejected" → block submit, แสดง dialog ให้ติดต่อ support

Subtask 5 — Form validate before submit (with API)
  POST /api/check-phone — ตรวจว่าเบอร์ซ้ำในระบบไหม ก่อนกด submit

Subtask 6 — Form validate after submit (inline + error dialog)
  400 field errors → แสดง inline error ใต้แต่ละ field
  409 duplicate → ErrorDialog "เบอร์นี้มีบัญชีอยู่แล้ว"
  500 → ErrorDialog "เกิดข้อผิดพลาด กรุณาลองใหม่"

Subtask 7 — Handle success dialog
  201 response → แสดง SuccessDialog "สมัครสำเร็จ!" พร้อมปุ่ม "ไปหน้าแรก"
  คลิกปุ่ม → redirect ไป /dashboard

═══════════════════════════════════════════════
📊 รวม 7 subtask (ครบทุกกลุ่ม)
```

---

## กฎการวิเคราะห์

| สถานการณ์ | วิธีจัดการ |
|---|---|
| Story ไม่มี form | Subtask 3, 5, 6 → N/A |
| ไม่มี alert condition | Subtask 4 → N/A |
| ไม่มี API validate ก่อน submit | Subtask 5 → N/A |
| Story ขนาดใหญ่มี flow หลายแบบ | ให้แนะนำแยก story ก่อนแตก subtask |
| Story เป็น backend only | แจ้งว่า skill นี้ออกแบบสำหรับ feature ที่มี UI |

---

## หมายเหตุสำหรับ Claude

- **ลำดับ subtask คงที่เสมอ**: UI (1→2) → Validate (3) → Integrate (4→7) ห้ามสลับ
- **ทุก subtask ต้องมีชื่อตรงตาม template** — ห้ามเปลี่ยนชื่อหัวข้อ เพิ่มแค่รายละเอียดใน body
- **Clean Architecture**: ถ้าไม่มี API spec ให้ define entity จาก UI field ไปก่อน — ห้ามถามหา API spec ถ้ายังไม่มี เพราะ data source swap ได้ภายหลัง
- **ห้ามสมมุติ** business logic หรือ UI behavior ที่ไม่มีใน Jira หรือ codebase — ให้ตั้งคำถามถามผู้ใช้แทนเสมอ
- **ห้ามข้าม Step 3 (scan codebase)** แม้ story ดูชัดเจน — gap มักซ่อนอยู่ใน pattern จริงของ project
- **ห้ามรวม gap หลาย round เป็น round เดียว** — ถามเฉพาะสิ่งที่รู้แล้วว่าไม่รู้ในแต่ละ round
- **AC Coverage ต้องครบ 100% ก่อนสร้าง draft** — ถ้า AC ข้อไหนยังไม่มี subtask รับ ให้กลับไปถามก่อนเสมอ
- **Jira session ต้อง login อยู่แล้ว** — ถ้าเจอหน้า login ให้แจ้งผู้ใช้ก่อน
- **ห้ามสร้าง subtask ใน Jira โดยไม่ขอ confirm** จากผู้ใช้ก่อนทุกครั้ง
