# สร้าง Google Service Account สำหรับ n8n

## ขั้นตอนที่ 1: เข้าสู่ Google Cloud Console

### 1.1 เข้าเว็บไซต์
- ไปที่ [console.cloud.google.com](https://console.cloud.google.com)
- เข้าสู่ระบบด้วย Google Account

### 1.2 สร้าง Project ใหม่ (หรือเลือกที่มีอยู่)
1. **คลิกที่ Project Selector** (มุมบนซ้าย)
2. **คลิก "New Project"**
3. **ตั้งชื่อ Project:** เช่น "n8n-google-sheets"
4. **คลิก "Create"**
5. **รอให้สร้างเสร็จ** และเลือก Project นี้

---

## ขั้นตอนที่ 2: เปิดใช้งาน APIs

### 2.1 เข้าสู่ APIs & Services
1. **เปิด Navigation Menu** (☰ มุมบนซ้าย)
2. **คลิก "APIs & Services"** > **"Library"**

### 2.2 เปิดใช้ Google Sheets API
1. **ค้นหา:** "Google Sheets API"
2. **คลิกเลือก** Google Sheets API
3. **คลิก "Enable"**
4. **รอให้เปิดใช้งานเสร็จ**

### 2.3 เปิดใช้ Google Drive API
1. **กลับไปที่ Library**
2. **ค้นหา:** "Google Drive API"
3. **คลิกเลือก** Google Drive API
4. **คลิก "Enable"**

---

## ขั้นตอนที่ 3: สร้าง Service Account

### 3.1 ไปที่ Credentials
1. **เปิด Navigation Menu** (☰)
2. **คลิก "APIs & Services"** > **"Credentials"**

### 3.2 สร้าง Service Account
1. **คลิก "+ CREATE CREDENTIALS"**
2. **เลือก "Service Account"**

### 3.3 กรอกข้อมูล Service Account

#### Service Account Details:
```
Service account name: n8n-sheets
Service account ID: n8n-sheets (จะสร้างอัตโนมัติ)
Description: Service Account for n8n Google Sheets integration
```

4. **คลิก "CREATE AND CONTINUE"**

#### Grant this service account access to project:
**Role:** เลือก **"Editor"** หรือ **"Project > Editor"**

5. **คลิก "CONTINUE"**

#### Grant users access to this service account (Optional):
6. **ข้ามขั้นตอนนี้** → คลิก **"DONE"**

---

## ขั้นตอนที่ 4: สร้าง JSON Key

### 4.1 เข้าสู่ Service Account
1. **อยู่ในหน้า Credentials**
2. **เลื่อนลงไปที่ "Service Accounts"**
3. **คลิกที่ Service Account** ที่สร้าง (n8n-sheets@...)

### 4.2 สร้าง Key
1. **คลิกแท็บ "Keys"**
2. **คลิก "ADD KEY"** > **"Create new key"**
3. **เลือก "JSON"**
4. **คลิก "CREATE"**

### 4.3 ดาวน์โหลดไฟล์
- **ไฟล์ JSON จะดาวน์โหลดอัตโนมัติ**
- **เก็บไฟล์นี้ไว้ในที่ปลอดภัย**
- **ไม่แชร์ไฟล์นี้กับใคร**

---

## ขั้นตอนที่ 5: ตรวจสอบข้อมูล JSON

### 5.1 เปิดไฟล์ JSON
ไฟล์จะมีโครงสร้างแบบนี้:

```json
{
  "type": "service_account",
  "project_id": "n8n-google-sheets-123456",
  "private_key_id": "abc123def456...",
  "private_key": "-----BEGIN PRIVATE KEY-----\nMIIEvgIBADANBgkqhkiG9w0BAQEFAASCBKgwggSkAgEAAoIBAQC...\n-----END PRIVATE KEY-----\n",
  "client_email": "n8n-sheets@n8n-google-sheets-123456.iam.gserviceaccount.com",
  "client_id": "123456789012345678901",
  "auth_uri": "https://accounts.google.com/o/oauth2/auth",
  "token_uri": "https://oauth2.googleapis.com/token",
  "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
  "client_x509_cert_url": "https://www.googleapis.com/robot/v1/metadata/x509/n8n-sheets%40n8n-google-sheets-123456.iam.gserviceaccount.com"
}
```

### 5.2 ข้อมูลสำคัญ:
- **client_email**: อีเมลของ Service Account
- **private_key**: Private Key สำหรับ authentication

---

## ขั้นตอนที่ 6: ใช้กับ n8n

### 6.1 ใน n8n Credentials:
1. **Service Account Email**: คัดลอกจาก `client_email`
2. **Private Key**: คัดลอกจาก `private_key` (ทั้งหมดรวม BEGIN/END)

### 6.2 ตัวอย่างการใส่ข้อมูล:

**Service Account Email:**
```
n8n-sheets@n8n-google-sheets-123456.iam.gserviceaccount.com
```

**Private Key:**
```
-----BEGIN PRIVATE KEY-----
MIIEvgIBADANBgkqhkiG9w0BAQEFAASCBKgwggSkAgEAAoIBAQC...
...ข้อมูลยาวๆ...
-----END PRIVATE KEY-----
```

---

## ขั้นตอนที่ 7: แชร์ Google Sheet

### 7.1 เปิด Google Sheets
1. **ไปที่** [sheets.google.com](https://sheets.google.com)
2. **เปิด Sheet** ที่ต้องการใช้กับ n8n

### 7.2 แชร์กับ Service Account
1. **คลิก "Share"** (มุมบนขวา)
2. **ใส่ Service Account Email**: `n8n-sheets@n8n-google-sheets-123456.iam.gserviceaccount.com`
3. **เลือกสิทธิ์**: **"Editor"**
4. **ปิดการแจ้งเตือน**: ✅ **Uncheck "Notify people"**
5. **คลิก "Share"**

### 7.3 ตรวจสอบ
- **Service Account** จะปรากฏในรายชื่อผู้ที่มีสิทธิ์เข้าถึง
- **สิทธิ์**: Editor
- **ไม่ได้รับ Email แจ้งเตือน**

---

## การทดสอบการเชื่อมต่อ

### ใน n8n:
1. **สร้าง Google Sheets Credential** ใหม่
2. **ใส่ข้อมูล** Service Account Email และ Private Key
3. **Save Credential**
4. **สร้าง Google Sheets Node**
5. **เลือก Credential** ที่สร้าง
6. **เลือก Operation**: Get Row(s)
7. **เลือก Document**: Sheet ที่แชร์ไว้
8. **Test การเชื่อมต่อ**

---

## การแก้ไขปัญหาที่พบบ่อย

### 1. "Could not connect" Error
**สาเหตุ:**
- APIs ไม่เปิดใช้งาน
- Private Key ผิดรูปแบบ
- Service Account ไม่มีสิทธิ์

**วิธีแก้:**
- ตรวจสอบ Google Sheets API และ Google Drive API
- คัดลอก Private Key ใหม่ทั้งหมด
- ตรวจสอบ Role ของ Service Account

### 2. "Sheet not found" Error
**สาเหตุ:**
- Google Sheet ไม่ได้แชร์กับ Service Account

**วิธีแก้:**
- แชร์ Google Sheet กับ Service Account Email
- ให้สิทธิ์ Editor

### 3. "Access denied" Error
**สาเหตุ:**
- Service Account ไม่มีสิทธิ์เพียงพอ
- Project ไม่ได้เปิดใช้ API

**วิธีแก้:**
- ตั้ง Role เป็น Editor
- ตรวจสอบ APIs ที่เปิดใช้งาน

---

## เทคนิคเพิ่มเติม

### 1. การใช้หลาย Google Sheets
- **แชร์ทุก Sheet** กับ Service Account เดียวกัน
- **ใช้ Credential เดียว** สำหรับหลาย Sheets

### 2. การจัดการความปลอดภัย
- **เก็บไฟล์ JSON ไว้ในที่ปลอดภัย**
- **ไม่แชร์ Private Key กับใคร**
- **ใช้ Environment Variables** ใน n8n production

### 3. การตั้งสิทธิ์แบบ Minimal
สำหรับความปลอดภัยสูงสุด:
- **ใช้ Custom Role** แทน Editor
- **ให้สิทธิ์เฉพาะที่จำเป็น**:
  - `sheets.spreadsheets.get`
  - `sheets.spreadsheets.values.get`
  - `sheets.spreadsheets.values.update`
  - `drive.files.get`

---

## สรุป

✅ **สิ่งที่ต้องมี:**
1. Google Cloud Project
2. Google Sheets API + Google Drive API เปิดใช้งาน
3. Service Account พร้อม JSON Key
4. Google Sheet ที่แชร์กับ Service Account

✅ **ข้อมูลสำหรับ n8n:**
- Service Account Email จาก `client_email`
- Private Key จาก `private_key` (ทั้งหมด)

✅ **การทดสอบ:**
- สร้าง Credential ใน n8n
- สร้าง Google Sheets Node
- ทดสอบการอ่านข้อมูล

หลังจากทำตามขั้นตอนนี้แล้ว จะสามารถใช้ n8n เชื่อมต่อกับ Google Sheets ได้เลย!

---

## Checklist การทำ

- [ ] มี Google Account
- [ ] สร้าง Google Cloud Project
- [ ] เปิด Google Sheets API
- [ ] เปิด Google Drive API  
- [ ] สร้าง Service Account
- [ ] ดาวน์โหลด JSON Key
- [ ] แชร์ Google Sheet กับ Service Account
- [ ] ทดสอบใน n8n
- [ ] สร้าง Workflow แรก

---

**หมายเหตุ:** เก็บไฟล์ JSON Key ไว้ให้ดี และอย่าแชร์กับใครนะครับ!