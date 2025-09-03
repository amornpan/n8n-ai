# การตั้งค่า Google Service Account ใน n8n

## ข้อมูลจากการตั้งค่าปัจจุบัน

### รายละเอียด Credential
- **ชื่อ**: Google Service Account account
- **ประเภท**: Google Service Account API
- **สถานะ**: Connection tested successfully ✅
- **วันที่สร้าง**: 3 September
- **อัปเดตล่าสุด**: 1 hour ago

## การตั้งค่าที่ใช้

### 1. Region
```
Americas (Council Bluffs) - us-central1
```

### 2. Service Account Email
```
service-google-sheet@n8n-nt-networking.iam.gserviceaccount.com
```

### 3. Private Key
- ใช้ Private Key ในรูปแบบ JSON key file
- Private Key จะถูกเข้ารหัสและแสดงเป็น dots (••••••••••••••••••••••••••••••••••••••••••••••••••••)

### 4. Impersonate a User
- ตั้งค่าเป็น: OFF (ปิดใช้งาน)

## วิธีการสร้าง Google Service Account

### Step 1: สร้าง Google Cloud Project
1. ไปที่ [Google Cloud Console](https://console.cloud.google.com/)
2. สร้าง Project ใหม่หรือเลือก Project ที่มีอยู่
3. เปิดใช้งาน APIs ที่จำเป็น (เช่น Google Sheets API, Google Drive API)

### Step 2: สร้าง Service Account
1. ไปที่ **IAM & Admin** > **Service Accounts**
2. คลิก **Create Service Account**
3. ใส่ชื่อและรายละเอียด Service Account
4. กำหนด Roles ที่เหมาะสม (เช่น Editor หรือ specific API roles)

### Step 3: สร้าง Key
1. เลือก Service Account ที่สร้าง
2. ไปที่แท็บ **Keys**
3. คลิก **Add Key** > **Create new key**
4. เลือก **JSON** format
5. Download JSON key file

### Step 4: ตั้งค่าใน n8n
1. ไปที่ **Credentials** ใน n8n
2. เลือก **Google Service Account API**
3. กรอกข้อมูล:
   - **Region**: เลือกตามที่ตั้งของ Google Cloud Project
   - **Service Account Email**: copy จาก JSON file หรือ Google Cloud Console
   - **Private Key**: copy เนื้อหาทั้งหมดจาก JSON file
   - **Impersonate a User**: ปล่อยว่างหรือปิดไว้ หากไม่จำเป็น

## การใช้งาน

Google Service Account นี้สามารถใช้กับ nodes ต่างๆ เช่น:
- Google Sheets
- Google Drive  
- Google Calendar
- Gmail (บางกรณี)
- Google Cloud services อื่นๆ

## หมายเหตุ
- Service Account Email: `service-google-sheet@n8n-nt-networking.iam.gserviceaccount.com` 
  แสดงว่าถูกสร้างใน Project ชื่อ "n8n-nt-networking"
- การตั้งค่านี้ทำงานได้สำเร็จแล้วตามที่แสดงใน Connection status
- เก็บไฟล์ JSON key ไว้ในที่ปลอดภัยและไม่แชร์กับผู้อื่น

## Troubleshooting
- หากเชื่อมต่อไม่สำเร็จ ให้ตรวจสอบว่า APIs ที่จำเป็นเปิดใช้งานแล้ว
- ตรวจสอบ Roles/Permissions ของ Service Account
- ตรวจสอบว่า Private Key ถูกต้องและครบถ้วน
