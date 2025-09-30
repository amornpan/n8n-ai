# ขั้นตอนการสร้าง Lead Management Workflow ด้วย n8n

## ส่วนที่ 1: การเตรียมการและการเริ่มต้น (Prerequisites & Setup)

### 1. การติดตั้ง n8n (กรณีที่ยังไม่ได้ติดตั้ง)
- Workshop นี้เป็นส่วนต่อเนื่องจากการเรียนรู้พื้นฐาน n8n
- หากยังไม่ได้ติดตั้ง สามารถติดตั้งผ่าน npm ด้วยคำสั่ง `npm install n8n -g`

### 2. รัน n8n
- เปิด Terminal ขึ้นมา
- ใช้คำสั่ง `n8n` (สำหรับผู้ที่ติดตั้งผ่าน npm)
- n8n จะสามารถเข้าถึงได้ผ่าน `localhost:5678`

### 3. เตรียม Canvas
- เข้าสู่หน้า Workflow Canvas
- สร้าง Workflow ใหม่สำหรับการจัดการ Leads

---

## ส่วนที่ 2: การรับข้อมูล Lead ผ่าน Webhook

### 1. เพิ่ม Webhook Node
- กดเครื่องหมายบวก (+) เพื่อ Add First Step
- ค้นหาและเลือก **Webhook** Node

### 2. ตั้งค่า Webhook
- **HTTP Method**: เลือก `POST`
- **Path**: กำหนดเป็น `lead-form` หรือชื่อที่ต้องการ
- **Authentication**: เลือก `None` (สำหรับ demo) หรือตั้งค่า authentication ตามต้องการ
- **Response Mode**: เลือก `When Last Node Finishes`

### 3. คัดลอก Webhook URL
- หลังจากตั้งค่าเสร็จ ระบบจะสร้าง Webhook URL ให้
- คัดลอก URL นี้ไว้ใช้ในการทดสอบ (เช่น `http://localhost:5678/webhook/lead-form`)

### 4. ทดสอบ Webhook
- กด **Listen for Test Event**
- ส่งข้อมูลทดสอบผ่าน Postman หรือ cURL:
```bash
curl -X POST http://localhost:5678/webhook/lead-form \
  -H "Content-Type: application/json" \
  -d '{
    "name": "สมชาย ใจดี",
    "email": "somchai@example.com",
    "phone": "0812345678",
    "company": "ABC Co., Ltd.",
    "interest": "Product Demo",
    "budget": "50000-100000"
  }'
```

---

## ส่วนที่ 3: การตรวจสอบและกรองข้อมูล (Validation & Filtering)

### 1. เพิ่ม IF Node
- เพิ่ม **IF** Node เพื่อตรวจสอบเงื่อนไข
- เชื่อมต่อจาก Webhook Node

### 2. ตั้งค่าเงื่อนไขการกรอง Lead
- **Condition 1**: ตรวจสอบว่ามี Email
  - Value 1: `{{ $json.body.email }}`
  - Operation: `Is Not Empty`

- **Condition 2**: ตรวจสอบงบประมาณ (Budget)
  - Value 1: `{{ $json.body.budget }}`
  - Operation: `Contains`
  - Value 2: `50000` (Lead คุณภาพสูง)

### 3. กำหนด Routing
- **True Branch**: Lead คุณภาพสูง (High Quality Lead)
- **False Branch**: Lead ทั่วไป (Standard Lead)

---

## ส่วนที่ 4: การจัดรูปแบบข้อมูล (Data Formatting)

### 1. เพิ่ม Set Node สำหรับ High Quality Lead
- เพิ่ม **Set** Node ที่ True Branch
- เลือก **Manual Mapping**

### 2. กำหนด Fields ที่ต้องการ
- **Field 1 - Lead Status**: 
  - Name: `lead_status`
  - Value: `Hot Lead`

- **Field 2 - Full Name**:
  - Name: `full_name`
  - Value: `{{ $json.body.name }}`

- **Field 3 - Email**:
  - Name: `email`
  - Value: `{{ $json.body.email }}`

- **Field 4 - Phone**:
  - Name: `phone`
  - Value: `{{ $json.body.phone }}`

- **Field 5 - Company**:
  - Name: `company`
  - Value: `{{ $json.body.company }}`

- **Field 6 - Interest**:
  - Name: `interest`
  - Value: `{{ $json.body.interest }}`

- **Field 7 - Budget Range**:
  - Name: `budget`
  - Value: `{{ $json.body.budget }}`

- **Field 8 - Created Date**:
  - Name: `created_at`
  - Value: `{{ $now.toISO() }}`

- **Field 9 - Priority**:
  - Name: `priority`
  - Value: `High`

### 3. ทำซ้ำสำหรับ Standard Lead
- เพิ่ม **Set** Node ที่ False Branch
- ตั้งค่าเหมือนข้างบน แต่เปลี่ยน:
  - `lead_status`: `Warm Lead`
  - `priority`: `Medium`

---

## ส่วนที่ 5: การบันทึกข้อมูลลง Google Sheets

### 1. เตรียม Google Sheets
- สร้าง Google Spreadsheet ใหม่ชื่อ "Lead Management"
- สร้างแถวแรกเป็น Headers:
  - A1: Lead Status
  - B1: Full Name
  - C1: Email
  - D1: Phone
  - E1: Company
  - F1: Interest
  - G1: Budget
  - H1: Created Date
  - I1: Priority

### 2. เพิ่ม Google Sheets Node
- เพิ่ม **Google Sheets** Node
- เชื่อมต่อจาก Set Node ทั้ง 2 แยก

### 3. ตั้งค่า Google Sheets Credential
- เลือก **Create New Credential**
- เลือก **Google Service Account**
- ทำตามขั้นตอน:
  1. ไปที่ Google Cloud Console
  2. สร้าง Service Account
  3. Download JSON Key
  4. แชร์ Google Sheet ให้กับ Service Account Email
  5. อัปโหลด JSON Key ใน n8n

### 4. ตั้งค่า Operation
- **Resource**: Sheet
- **Operation**: Append or Update Row
- **Document ID**: คัดลอกจาก URL ของ Google Sheet
- **Sheet Name**: Sheet1
- **Mapping Column Mode**: Map Each Column Manually

### 5. Map ข้อมูล
- Column A: `{{ $json.lead_status }}`
- Column B: `{{ $json.full_name }}`
- Column C: `{{ $json.email }}`
- Column D: `{{ $json.phone }}`
- Column E: `{{ $json.company }}`
- Column F: `{{ $json.interest }}`
- Column G: `{{ $json.budget }}`
- Column H: `{{ $json.created_at }}`
- Column I: `{{ $json.priority }}`

---

## ส่วนที่ 6: การส่งการแจ้งเตือนไปยัง Slack

### 1. เตรียม Slack Webhook
- เปิด Slack Workspace
- ไปที่ **Apps** > ค้นหา **Incoming Webhooks**
- คลิก **Add to Slack**
- เลือก Channel ที่ต้องการ (เช่น #leads)
- คัดลอก **Webhook URL**

### 2. เพิ่ม Slack Node สำหรับ Hot Lead
- เพิ่ม **Slack** Node หลังจาก Set Node (True Branch)
- เลือก **Message** > **Post**

### 3. ตั้งค่า Slack Credential
- เลือก **Create New Credential**
- เลือก **Webhook**
- วาง Webhook URL ที่คัดลอกไว้
- กด **Save**

### 4. สร้างข้อความแจ้งเตือน
```
🔥 **Hot Lead Alert!**

👤 ชื่อ: {{ $json.full_name }}
📧 Email: {{ $json.email }}
📱 Phone: {{ $json.phone }}
🏢 บริษัท: {{ $json.company }}
💼 สนใจ: {{ $json.interest }}
💰 งบประมาณ: {{ $json.budget }}
⏰ เวลา: {{ $json.created_at }}

✅ Priority: HIGH - ติดต่อกลับภายใน 1 ชั่วโมง!
```

### 5. เพิ่ม Slack Node สำหรับ Standard Lead (ทางเลือก)
- ทำซ้ำข้างบนสำหรับ False Branch
- เปลี่ยนข้อความเป็น "Warm Lead" และ Priority เป็น Medium

---

## ส่วนที่ 7: การส่งอีเมลแจ้งเตือนทีมขาย

### 1. เพิ่ม Gmail Node (สำหรับ Hot Lead)
- เพิ่ม **Gmail** Node หลังจาก Slack Node (True Branch)
- เลือก **Message** > **Send**

### 2. ตั้งค่า Gmail Credential
- เลือก **Create New Credential**
- เลือก **OAuth2**
- ทำการ Authorize กับ Google Account

### 3. กำหนดรายละเอียดอีเมล
- **To**: `sales-team@yourcompany.com`
- **Subject**: `🔥 Hot Lead Alert: {{ $json.full_name }}`
- **Email Type**: HTML
- **Message**: 
```html
<h2>Hot Lead ใหม่เข้ามาแล้ว!</h2>

<table style="border-collapse: collapse; width: 100%;">
  <tr>
    <td style="padding: 8px; border: 1px solid #ddd;"><strong>ชื่อ:</strong></td>
    <td style="padding: 8px; border: 1px solid #ddd;">{{ $json.full_name }}</td>
  </tr>
  <tr>
    <td style="padding: 8px; border: 1px solid #ddd;"><strong>Email:</strong></td>
    <td style="padding: 8px; border: 1px solid #ddd;">{{ $json.email }}</td>
  </tr>
  <tr>
    <td style="padding: 8px; border: 1px solid #ddd;"><strong>เบอร์โทร:</strong></td>
    <td style="padding: 8px; border: 1px solid #ddd;">{{ $json.phone }}</td>
  </tr>
  <tr>
    <td style="padding: 8px; border: 1px solid #ddd;"><strong>บริษัท:</strong></td>
    <td style="padding: 8px; border: 1px solid #ddd;">{{ $json.company }}</td>
  </tr>
  <tr>
    <td style="padding: 8px; border: 1px solid #ddd;"><strong>สนใจ:</strong></td>
    <td style="padding: 8px; border: 1px solid #ddd;">{{ $json.interest }}</td>
  </tr>
  <tr>
    <td style="padding: 8px; border: 1px solid #ddd;"><strong>งบประมาณ:</strong></td>
    <td style="padding: 8px; border: 1px solid #ddd;">{{ $json.budget }}</td>
  </tr>
</table>

<p style="color: red; font-weight: bold;">⚠️ Priority: HIGH - กรุณาติดต่อกลับภายใน 1 ชั่วโมง</p>
```

---

## ส่วนที่ 8: การส่งอีเมลตอบกลับอัตโนมัติ (Auto-reply)

### 1. เพิ่ม Gmail Node สำหรับ Auto-reply
- เพิ่ม **Gmail** Node หลังจาก Google Sheets Node
- เชื่อมต่อจากทั้ง 2 Branch (Hot และ Standard)

### 2. กำหนดรายละเอียดอีเมลตอบกลับ
- **To**: `{{ $json.email }}`
- **Subject**: `ขอบคุณที่ติดต่อเรา - {{ $json.company }}`
- **Email Type**: HTML
- **Message**:
```html
<div style="font-family: Arial, sans-serif; max-width: 600px; margin: 0 auto;">
  <h2 style="color: #2c3e50;">สวัสดีคุณ {{ $json.full_name }}</h2>
  
  <p>ขอบคุณที่ให้ความสนใจในผลิตภัณฑ์/บริการของเรา</p>
  
  <p>เราได้รับข้อมูลของคุณเรียบร้อยแล้ว:</p>
  <ul>
    <li>ความสนใจ: {{ $json.interest }}</li>
    <li>บริษัท: {{ $json.company }}</li>
  </ul>
  
  <p>ทีมงานของเราจะติดต่อกลับภายใน 24 ชั่วโมง</p>
  
  <p>หากมีคำถามเพิ่มเติม สามารถติดต่อเราได้ที่:</p>
  <ul>
    <li>📧 Email: info@yourcompany.com</li>
    <li>📱 Tel: 02-xxx-xxxx</li>
  </ul>
  
  <p style="color: #7f8c8d; font-size: 12px; margin-top: 30px;">
    <em>This is an automated email. Please do not reply directly to this message.</em>
  </p>
</div>
```

---

## ส่วนที่ 9: การเพิ่ม Lead ลงใน CRM (ทางเลือก)

### 1. เพิ่ม HTTP Request Node
- เพิ่ม **HTTP Request** Node
- เชื่อมต่อจาก Set Node

### 2. ตั้งค่าการเชื่อมต่อ CRM
- **Method**: POST
- **URL**: `https://your-crm-api.com/api/leads`
- **Authentication**: Bearer Token (ใส่ API Token ของ CRM)
- **Body Content Type**: JSON

### 3. กำหนด JSON Body
```json
{
  "name": "{{ $json.full_name }}",
  "email": "{{ $json.email }}",
  "phone": "{{ $json.phone }}",
  "company": "{{ $json.company }}",
  "status": "{{ $json.lead_status }}",
  "interest": "{{ $json.interest }}",
  "budget": "{{ $json.budget }}",
  "priority": "{{ $json.priority }}",
  "source": "Website Form",
  "created_at": "{{ $json.created_at }}"
}
```

---

## ส่วนที่ 10: การสิ้นสุดและการเปิดใช้งาน Workflow

### 1. ตรวจสอบ Workflow
- ตรวจสอบว่าทุก Node เชื่อมต่อกันถูกต้อง
- ตรวจสอบว่าไม่มี Node ที่แสดง Error

### 2. ทดสอบ Workflow แบบเต็มรูปแบบ
- กด **Execute Workflow** button
- ส่งข้อมูลทดสอบผ่าน Webhook
- ตรวจสอบผลลัพธ์:
  - ข้อมูลบันทึกใน Google Sheets
  - การแจ้งเตือนใน Slack
  - อีเมลส่งถึงทีมขายและลูกค้า

### 3. บันทึก Workflow
- ตั้งชื่อ Workflow: "Lead Management System"
- กด **Save**

### 4. เปิดใช้งาน Workflow
- เปลี่ยนสถานะจาก **Inactive** เป็น **Active**
- Webhook จะพร้อมรับข้อมูลตลอด 24 ชั่วโมง

---

## ส่วนที่ 11: การผสานรวมกับฟอร์มเว็บไซต์

### ตัวอย่างโค้ด HTML Form
```html
<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ติดต่อเรา - Lead Form</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 600px;
            margin: 50px auto;
            padding: 20px;
        }
        .form-group {
            margin-bottom: 15px;
        }
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }
        input, select, textarea {
            width: 100%;
            padding: 8px;
            border: 1px solid #ddd;
            border-radius: 4px;
        }
        button {
            background-color: #4CAF50;
            color: white;
            padding: 10px 20px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
        }
        button:hover {
            background-color: #45a049;
        }
        .success-message {
            display: none;
            background-color: #d4edda;
            color: #155724;
            padding: 15px;
            border-radius: 4px;
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <h1>ติดต่อเรา</h1>
    <form id="leadForm">
        <div class="form-group">
            <label for="name">ชื่อ-นามสกุล *</label>
            <input type="text" id="name" name="name" required>
        </div>

        <div class="form-group">
            <label for="email">อีเมล *</label>
            <input type="email" id="email" name="email" required>
        </div>

        <div class="form-group">
            <label for="phone">เบอร์โทรศัพท์ *</label>
            <input type="tel" id="phone" name="phone" required>
        </div>

        <div class="form-group">
            <label for="company">บริษัท/องค์กร</label>
            <input type="text" id="company" name="company">
        </div>

        <div class="form-group">
            <label for="interest">สนใจบริการ *</label>
            <select id="interest" name="interest" required>
                <option value="">-- เลือกบริการ --</option>
                <option value="Product Demo">ขอทดลองใช้ผลิตภัณฑ์</option>
                <option value="Consultation">ปรึกษาโซลูชัน</option>
                <option value="Quote">ขอใบเสนอราคา</option>
                <option value="Support">บริการหลังการขาย</option>
            </select>
        </div>

        <div class="form-group">
            <label for="budget">งบประมาณโดยประมาณ (บาท)</label>
            <select id="budget" name="budget">
                <option value="">-- เลือกช่วงงบประมาณ --</option>
                <option value="0-20000">น้อยกว่า 20,000</option>
                <option value="20000-50000">20,000 - 50,000</option>
                <option value="50000-100000">50,000 - 100,000</option>
                <option value="100000-500000">100,000 - 500,000</option>
                <option value="500000+">มากกว่า 500,000</option>
            </select>
        </div>

        <button type="submit">ส่งข้อมูล</button>
    </form>

    <div class="success-message" id="successMessage">
        ✅ ขอบคุณครับ! เราได้รับข้อมูลของคุณเรียบร้อยแล้ว ทีมงานจะติดต่อกลับเร็วๆ นี้
    </div>

    <script>
        document.getElementById('leadForm').addEventListener('submit', async function(e) {
            e.preventDefault();

            const formData = {
                name: document.getElementById('name').value,
                email: document.getElementById('email').value,
                phone: document.getElementById('phone').value,
                company: document.getElementById('company').value,
                interest: document.getElementById('interest').value,
                budget: document.getElementById('budget').value
            };

            try {
                const response = await fetch('http://localhost:5678/webhook/lead-form', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify(formData)
                });

                if (response.ok) {
                    document.getElementById('leadForm').style.display = 'none';
                    document.getElementById('successMessage').style.display = 'block';
                } else {
                    alert('เกิดข้อผิดพลาด กรุณาลองใหม่อีกครั้ง');
                }
            } catch (error) {
                console.error('Error:', error);
                alert('เกิดข้อผิดพลาดในการส่งข้อมูล');
            }
        });
    </script>
</body>
</html>
```

---

## ตัวอย่างผลลัพธ์ที่ได้

### 1. Google Sheets
Lead ทุกตัวจะถูกบันทึกอัตโนมัติพร้อมสถานะ Priority

### 2. Slack Notification
```
🔥 Hot Lead Alert!

👤 ชื่อ: สมชาย ใจดี
📧 Email: somchai@example.com
📱 Phone: 0812345678
🏢 บริษัท: ABC Co., Ltd.
💼 สนใจ: Product Demo
💰 งบประมาณ: 50000-100000
⏰ เวลา: 2025-09-30T10:30:00Z

✅ Priority: HIGH - ติดต่อกลับภายใน 1 ชั่วโมง!
```

### 3. Email ถึงทีมขาย
อีเมลแจ้งเตือนพร้อมรายละเอียด Lead แบบสมบูรณ์

### 4. Email Auto-reply ถึงลูกค้า
อีเมลขอบคุณและยืนยันการรับข้อมูลส่งถึงลูกค้าทันที

---

## เคล็ดลับและข้อแนะนำ

### 1. การปรับปรุง Workflow
- เพิ่มการตรวจสอบข้อมูลซ้ำด้วย Email
- เพิ่ม Score ให้กับ Lead ตามเกณฑ์ต่างๆ
- เพิ่มการส่ง SMS สำหรับ Hot Lead

### 2. การรักษาความปลอดภัย
- ใช้ Authentication สำหรับ Webhook
- เก็บ API Keys และ Credentials อย่างปลอดภัย
- ตั้งค่า Rate Limiting

### 3. การติดตามผล
- เพิ่ม Node สำหรับ Analytics
- บันทึก Response Time
- ติดตามอัตราการแปลง Lead เป็นลูกค้า

### 4. Error Handling
- เพิ่ม Error Trigger Node
- ตั้งค่า Retry Mechanism
- ส่งแจ้งเตือนเมื่อมี Error

---

## สรุป

Workflow นี้ช่วยให้คุณ:
- ✅ รับ Lead จากเว็บไซต์แบบอัตโนมัติ
- ✅ จัดประเภทและจัดลำดับความสำคัญของ Lead
- ✅ บันทึกข้อมูลลง Google Sheets
- ✅ แจ้งเตือนทีมขายทันที
- ✅ ส่งอีเมลตอบกลับลูกค้าอัตโนมัติ
- ✅ ผสานรวมกับ CRM ได้

ด้วย n8n คุณสามารถสร้างระบบจัดการ Lead ที่มีประสิทธิภาพได้เองโดยไม่ต้องเขียนโค้ดซับซ้อน!
