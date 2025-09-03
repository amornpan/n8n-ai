# การตั้งค่า Slack API ใน n8n

## ข้อมูลจากการตั้งค่าปัจจุบัน

### รายละเอียด Credential
- **ชื่อ**: Slack account
- **ประเภท**: Slack API
- **สถานะ**: Connection tested successfully ✅
- **วันที่สร้าง**: 2 September
- **อัปเดตล่าสุด**: 19 hours ago

## การตั้งค่าที่ใช้

### 1. Access Token
```
••••••••••••••••••••••••••••••••••••••••••••••••••••••••••••
```
(ถูกซ่อนด้วยเหตุผลด้านความปลอดภัย)

### 2. Signature Secret
```
••••••••••••••••••••••••••••••••••••••••••••••••••••••••••••
```
(ถูกซ่อนด้วยเหตุผลด้านความปลอดภัย)

## วิธีการตั้งค่า Slack API

### Step 1: สร้าง Slack App
1. ไปที่ [Slack API Portal](https://api.slack.com/apps)
2. คลิก **Create New App**
3. เลือก **From scratch**
4. ใส่ชื่อ App (เช่น "n8n Integration")
5. เลือก Workspace ที่ต้องการติดตั้ง

### Step 2: กำหนด OAuth & Permissions
1. ไปที่ **OAuth & Permissions** ในเมนูซ้าย
2. ในส่วน **Scopes**, เพิ่ม Bot Token Scopes ที่จำเป็น:

#### Bot Token Scopes ที่แนะนำ:
```
- channels:read        # อ่านข้อมูล channels
- channels:write       # เขียนใน channels  
- chat:write          # ส่งข้อความ
- chat:write.public   # ส่งข้อความใน public channels
- files:read          # อ่านไฟล์
- files:write         # อัปโหลดไฟล์
- groups:read         # อ่าน private channels
- groups:write        # เขียนใน private channels
- im:read            # อ่าน direct messages
- im:write           # เขียน direct messages
- mpim:read          # อ่าน group direct messages
- mpim:write         # เขียน group direct messages
- users:read         # อ่านข้อมูลผู้ใช้
- users:read.email   # อ่านอีเมลผู้ใช้
```

### Step 3: ติดตั้ง App ใน Workspace
1. ในหน้า **OAuth & Permissions**
2. คลิก **Install to Workspace**
3. อนุญาตสิทธิ์ที่ขอ
4. รับ **Bot User OAuth Token** (ขึ้นต้นด้วย `xoxb-`)

### Step 4: รับ Signing Secret
1. ไปที่ **Basic Information** ในเมนูซ้าย
2. ในส่วน **App Credentials**
3. คัดลอก **Signing Secret**

### Step 5: ตั้งค่าใน n8n
1. ไปที่ **Credentials** ใน n8n
2. เลือก **Slack API**
3. กรอกข้อมูล:
   - **Access Token**: paste Bot User OAuth Token (xoxb-...)
   - **Signature Secret**: paste Signing Secret จาก Basic Information

## ประเภท Token ใน Slack

### 1. Bot User OAuth Token (xoxb-...)
- ใช้สำหรับการทำงานของ Bot
- มี Scopes ที่กำหนดไว้
- **นี่คือ Access Token ที่ใช้ใน n8n**

### 2. User OAuth Token (xoxp-...)
- ใช้สำหรับการทำงานในนาม User
- มีสิทธิ์มากกว่า Bot Token
- ใช้เมื่อต้องการ User-level permissions

### 3. Webhook URL (สำหรับ Incoming Webhooks)
- ใช้สำหรับส่งข้อความแบบง่าย
- รูปแบบ: https://hooks.slack.com/services/...

## การใช้งาน Slack API ใน n8n

### 1. ส่งข้อความ (Send Message)
```
Trigger → Slack (Send Message)
```

### 2. อัปโหลดไฟล์ (Upload File)  
```
Trigger → Slack (Upload File)
```

### 3. รับข้อความ (Slack Trigger)
```
Slack Trigger → Process Message
```

### 4. อัปเดต Message
```
Trigger → Slack (Update Message)
```

### 5. จัดการ Channels
```
Trigger → Slack (Create/Archive Channel)
```

## Event Subscriptions (สำหรับ Triggers)

หากต้องการใช้ Slack Trigger ใน n8n:

### 1. ตั้งค่า Event Subscriptions
1. ไปที่ **Event Subscriptions** ใน Slack App
2. เปิดใช้งาน **Enable Events**
3. ใส่ **Request URL**: 
   ```
   https://your-n8n-domain.com/webhook/slack
   ```

### 2. Subscribe to Bot Events
เลือก Events ที่ต้องการ:
```
- message.channels    # ข้อความใน public channels
- message.groups      # ข้อความใน private channels  
- message.im          # ข้อความใน direct messages
- app_mention         # เมื่อมีคนแมนชัน bot
- file_shared         # เมื่อมีการแชร์ไฟล์
```

## การทดสอบ Connection

### ขั้นตอนการทดสอบ:
1. บันทึกการตั้งค่าใน n8n
2. คลิก **Test** 
3. ควรแสดง "Connection tested successfully" ✅

### การทดสอบด้วย Node:
1. สร้าง Simple Workflow
2. เพิ่ม **Slack** node
3. เลือก **Send Message**
4. เลือก Channel และใส่ข้อความทดสอบ
5. Execute workflow

## Troubleshooting

### ปัญหาที่พบบ่อย

1. **"invalid_auth" หรือ "token_revoked"**
   - ตรวจสอบ Access Token
   - ตรวจสอบว่า App ยังติดตั้งใน Workspace

2. **"missing_scope"**
   - เพิ่ม Bot Token Scopes ที่จำเป็น
   - Reinstall App หลังเพิ่ม Scopes

3. **"channel_not_found"**
   - ตรวจสอบว่า Bot อยู่ใน Channel
   - เชิญ Bot เข้า Channel: `/invite @your_bot_name`

4. **"not_in_channel"**
   - เพิ่ม Bot เข้า Channel ก่อนส่งข้อความ

### การแก้ไข:
- ตรวจสอบ Bot Token Scopes
- ตรวจสอบว่า Bot อยู่ใน Channel ที่จะส่งข้อความ
- ตรวจสอบ Signing Secret ถูกต้อง
- ลอง Generate Token ใหม่หากจำเป็น

## ความปลอดภัย

### แนวปราทิส:
- เก็บ Access Token และ Signing Secret เป็นความลับ
- ใช้ HTTPS สำหรับ Webhook URLs
- ตรวจสอบ Event Signatures (Slack ใช้ Signing Secret)
- กำหนด Scopes เฉพาะที่จำเป็น
- ใช้ Bot Token แทน User Token เมื่อเป็นไปได้

## Bot vs User Permissions

### Bot Token (แนะนำ):
- ปลอดภัยกว่า
- สิทธิ์จำกัดตาม Scopes
- ไม่หมดอายุ (ยกเว้นถูก revoke)

### User Token:
- มีสิทธิ์มากกว่า
- อาจหมดอายุ
- ทำงานในนาม User

## หมายเหตุ
- Connection tested successfully แสดงว่าการตั้งค่าถูกต้อง
- Access Token และ Signature Secret ถูกซ่อนด้วยเหตุผลความปลอดภัย  
- สามารถใช้งาน Enterprise plan features สำหรับ external vaults
- ตรวจสอบ Slack App permissions เป็นระยะ

## ตัวอย่าง Use Cases

### 1. การแจ้งเตือนอัตโนมัติ
```
Schedule Trigger → Check System → Slack (Send Alert)
```

### 2. การประมวลผลคำสั่ง
```
Slack Trigger (mention) → Process Command → Slack (Reply)
```

### 3. การรายงานประจำวัน
```
Cron → Generate Report → Slack (Send File)
```

### 4. การ Forward ข้อความ
```
Slack Trigger → Filter → Slack (Send to Another Channel)
```
