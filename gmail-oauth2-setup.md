# การตั้งค่า Gmail OAuth2 สำหรับ n8n

คู่มือฉบับสมบูรณ์สำหรับการสร้างและตั้งค่า Gmail OAuth2 Credentials เพื่อใช้งานกับ n8n

## ขั้นตอนที่ 1: เข้า Google Cloud Console

1. ไปที่ [Google Cloud Console](https://console.cloud.google.com)
2. เข้าสู่ระบบด้วย Google Account ของคุณ
3. เลือกโปรเจค หรือสร้างโปรเจคใหม่

## ขั้นตอนที่ 2: เปิดใช้งาน Gmail API

1. ไปที่ **"APIs & Services"** > **"Library"**
2. ค้นหา **"Gmail API"**
3. คลิก **"ENABLE"** เพื่อเปิดใช้งาน

## ขั้นตอนที่ 3: ตั้งค่า OAuth Consent Screen

1. ไปที่ **"APIs & Services"** > **"OAuth consent screen"**
2. เลือก User Type:
   - **External**: สำหรับใช้งานจริง
   - **Internal**: สำหรับองค์กรเท่านั้น (ถ้ามี Google Workspace)

### 3.1 App Information
- **App name**: ใส่ชื่อแอป (เช่น "n8n Gmail Integration")
- **User support email**: เลือกอีเมลของคุณ
- **Developer contact information**: ใส่อีเมลของคุณ
- กด **"SAVE AND CONTINUE"**

### 3.2 Scopes (ข้าม)
- กด **"SAVE AND CONTINUE"**

### 3.3 Test Users (สำคัญ!)
- คลิก **"+ ADD USERS"**
- เพิ่มอีเมลที่จะใช้งาน Gmail API
- กด **"SAVE AND CONTINUE"**

### 3.4 Summary
- ตรวจสอบข้อมูลและกด **"BACK TO DASHBOARD"**

## ขั้นตอนที่ 4: สร้าง OAuth2 Credentials

1. ไปที่ **"APIs & Services"** > **"Credentials"**
2. คลิก **"+ CREATE CREDENTIALS"**
3. เลือก **"OAuth 2.0 Client IDs"**

### 4.1 ตั้งค่า OAuth Client
- **Application type**: **Web application**
- **Name**: ใส่ชื่อที่เข้าใจง่าย (เช่น "n8n Gmail Client")

### 4.2 Authorized redirect URIs
- คลิก **"+ ADD URI"**
- ใส่: `http://localhost:5678/rest/oauth2-credential/callback`
- กด **"CREATE"**

## ขั้นตอนที่ 5: เก็บ Credentials

หลังจากสร้าง OAuth Client แล้ว คุณจะได้:
- **Client ID**: เก็บไว้ในที่ปลอดภัย
- **Client Secret**: เก็บไว้ในที่ปลอดภัย

## ขั้นตอนที่ 6: ตั้งค่าใน n8n

1. ในหน้าตั้งค่า Gmail credentials ของ n8n
2. เลือก **"OAuth2 (recommended)"**
3. ใส่ข้อมูล:
   - **OAuth Redirect URL**: `http://localhost:5678/rest/oauth2-credential/callback`
   - **Client ID**: ที่ได้จาก Google Cloud Console
   - **Client Secret**: ที่ได้จาก Google Cloud Console
4. กด **"Save"**
5. กด **"Connect my account"** เพื่อ authenticate

## การแก้ไขข้อผิดพลาด

### ปัญหา: "access_denied" หรือ "ยังไม่ผ่านการยืนยัน"

**วิธีแก้:**
1. ไปที่ **OAuth consent screen**
2. เพิ่มอีเมลที่ใช้งานใน **Test users**
3. หรือ **"PUBLISH APP"** เพื่อใช้งานแบบ production

### ปัญหา: "redirect_uri_mismatch"

**วิธีแก้:**
1. ตรวจสอบ Authorized redirect URIs ใน OAuth Client
2. ต้องตรงกับ: `http://localhost:5678/rest/oauth2-credential/callback`

## Scopes ที่จำเป็นสำหรับ Gmail

n8n จะขอ permissions เหล่านี้โดยอัตโนมัติ:
- `https://www.googleapis.com/auth/gmail.readonly`
- `https://www.googleapis.com/auth/gmail.send`
- `https://www.googleapis.com/auth/gmail.compose`
- `https://www.googleapis.com/auth/gmail.modify`

## การรักษาความปลอดภัย

1. **เก็บ Client Secret ให้ปลอดภัย** - อย่าแชร์หรือ commit ลง version control
2. **ใช้ Test users เฉพาะคนที่จำเป็น**
3. **ตรวจสอบ OAuth consent screen ให้ถูกต้อง**
4. **ใช้ HTTPS ในการ production** (แทน http://localhost)

## หมายเหตุเพิ่มเติม

- การตั้งค่านี้ใช้สำหรับ development environment (localhost:5678)
- สำหรับ production ต้องเปลี่ยน redirect URI และใช้ HTTPS
- Google มี quota limits สำหรับ API calls
- ควรตรวจสอบ usage ใน Google Cloud Console เป็นประจำ

## ลิงก์ที่เป็นประโยชน์

- [Google Cloud Console](https://console.cloud.google.com)
- [Gmail API Documentation](https://developers.google.com/gmail/api)
- [n8n Gmail Node Documentation](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.gmail/)

---

สร้างเมื่อ: $(date)
สำหรับ: n8n Gmail OAuth2 Integration