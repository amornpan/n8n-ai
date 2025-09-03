# การตั้งค่า Gmail OAuth2 API ใน n8n

## ข้อมูลจากการตั้งค่าปัจจุบัน

### รายละเอียด Credential
- **ชื่อ**: Gmail account
- **ประเภท**: Gmail OAuth2 API
- **สถานะ**: Account connected ✅
- **วันที่สร้าง**: 2 September
- **อัปเดตล่าสุด**: 16 hours ago

## การตั้งค่าที่ใช้

### 1. OAuth Redirect URL
```
http://localhost:5678/rest/oauth2-credential/callback
```
**หมายเหตุ**: URL นี้ต้องใช้เมื่อถูกขอให้ใส่ OAuth callback หรือ redirect URL ใน Gmail

### 2. Client ID
```
4340295116-pfc7qt7lo1a1njssofi7h6eta4rucdrk.apps.googleusercontent.com
```

### 3. Client Secret
```
••••••••••••••••••••••••••••••••••••••••••••••••••••••••
```
(ถูกซ่อนด้วยเหตุผลด้านความปลอดภัย)

## วิธีการตั้งค่า Gmail OAuth2 API

### Step 1: สร้าง Google Cloud Project และเปิดใช้งาน APIs
1. ไปที่ [Google Cloud Console](https://console.cloud.google.com/)
2. สร้าง Project ใหม่หรือเลือก Project ที่มีอยู่
3. เปิดใช้งาน **Gmail API**:
   - ไปที่ **APIs & Services** > **Library**
   - ค้นหา "Gmail API"
   - คลิก **Enable**

### Step 2: สร้าง OAuth 2.0 Credentials
1. ไปที่ **APIs & Services** > **Credentials**
2. คลิก **Create Credentials** > **OAuth 2.0 Client IDs**
3. เลือก Application type: **Web application**
4. ตั้งชื่อ Client (เช่น "n8n Gmail Integration")

### Step 3: กำหนด Authorized redirect URIs
1. ในส่วน **Authorized redirect URIs**
2. เพิ่ม URL: 
   ```
   http://localhost:5678/rest/oauth2-credential/callback
   ```
   **สำคัญ**: URL นี้ต้องตรงกับที่แสดงใน n8n เป็นอย่างยิ่ง

### Step 4: รับ Client ID และ Client Secret
1. หลังจากสร้างเสร็จ จะได้รับ:
   - **Client ID**: (รูปแบบ: xxxxxx-xxxxx.apps.googleusercontent.com)
   - **Client Secret**: (string แบบสุ่ม)
2. Download JSON file สำหรับเก็บไว้สำรอง

### Step 5: ตั้งค่าใน n8n
1. ไปที่ **Credentials** ใน n8n
2. เลือก **Gmail OAuth2 API**
3. กรอกข้อมูล:
   - **Client ID**: paste จาก Google Cloud Console
   - **Client Secret**: paste จาก Google Cloud Console
4. คลิก **Sign in with Google**
5. อนุญาตสิทธิ์ที่จำเป็น

## OAuth Redirect URL สำหรับสภาพแวดล้อมต่างๆ

### Development (Local)
```
http://localhost:5678/rest/oauth2-credential/callback
```

### Production (ตัวอย่าง)
```
https://your-n8n-domain.com/rest/oauth2-credential/callback
```
**หมายเหตุ**: ต้องเพิ่ม URL นี้ใน Google Cloud Console ด้วย

## การใช้งาน

Gmail OAuth2 API นี้สามารถใช้กับ Gmail node สำหรับ:
- **ส่งอีเมล** (Send Email)
- **อ่านอีเมล** (Get Email)
- **ค้นหาอีเมล** (Search Email)
- **จัดการ Labels**
- **อ่าน Attachments**

## ตัวอย่างการใช้งานใน Workflow

### 1. ส่งอีเมลอัตโนมัติ
```
Trigger → Gmail (Send Email)
```

### 2. ตอบกลับอีเมลเมื่อได้รับ
```
Gmail Trigger → Gmail (Reply)
```

### 3. ประมวลผล Attachments
```
Gmail (Get Email) → Extract Attachments → Process File
```

## Scopes ที่ใช้งาน
Gmail OAuth2 API ใน n8n โดยปกติจะขออนุญาต:
- `https://www.googleapis.com/auth/gmail.readonly` - อ่านอีเมล
- `https://www.googleapis.com/auth/gmail.send` - ส่งอีเมล
- `https://www.googleapis.com/auth/gmail.modify` - แก้ไขอีเมล

## Troubleshooting

### ปัญหาที่พบบ่อย
1. **"redirect_uri_mismatch"**
   - ตรวจสอบว่า OAuth Redirect URL ใน Google Cloud Console ตรงกับใน n8n
   
2. **"invalid_client"**
   - ตรวจสอบ Client ID และ Client Secret
   
3. **"access_denied"**
   - ผู้ใช้ไม่อนุญาตสิทธิ์ หรือ Gmail API ไม่ได้เปิดใช้งาน

### การแก้ไข
- ตรวจสอบว่า Gmail API เปิดใช้งานใน Google Cloud Project
- ตรวจสอบ OAuth consent screen ได้ตั้งค่าถูกต้อง
- ลอง Reconnect อีกครั้งหากมีปัญหา

## ความปลอดภัย
- เก็บ Client Secret ไว้เป็นความลับ
- ใช้ HTTPS ใน production environment
- ตรวจสอบ redirect URI ให้ถูกต้องเสมอ
- พิจารณาใช้ Service Account หากเป็น server-to-server communication

## หมายเหตุ
- Client ID: `4340295116-pfc7qt7lo1a1njssofi7h6eta4rucdrk.apps.googleusercontent.com` คือ ID เฉพาะสำหรับ Project นี้
- การเชื่อมต่อสำเร็จแล้วตามที่แสดงใน Account connected status
- URL callback ใช้ localhost:5678 แสดงว่าเป็น development environment
