# n8n Workflows ง่ายๆ สำหรับ Customer Database

## Workflow 1: อ่านข้อมูลลูกค้าทั้งหมด (Basic Read)

### โครงสร้าง Workflow:
```
Manual Trigger → Google Sheets (Read) → Table
```

### ขั้นตอนการสร้าง:

#### 1. เพิ่ม Manual Trigger Node
- **Node Type**: Manual Trigger
- **Name**: "เริ่มต้น"
- ไม่ต้องตั้งค่าอะไร

#### 2. เพิ่ม Google Sheets Node
- **Node Type**: Google Sheets
- **Operation**: Get row(s) in sheet
- **Document**: เลือก Sheet ที่สร้างไว้
- **Sheet**: Sheet1 (หรือชื่อที่ตั้งไว้)
- **Range**: `A1:H100` (หรือ `A:H` สำหรับทุกข้อมูล)
- **Header Row**: `Yes`

#### 3. เพิ่ม Table Node (แสดงผล)
- **Node Type**: Table
- **Name**: "แสดงข้อมูลลูกค้า"

---

## Workflow 2: ค้นหาลูกค้าตาม Status

### โครงสร้าง Workflow:
```
Manual Trigger → Google Sheets (Read) → Code → Table
```

### Node Configuration:

#### 1. Manual Trigger
- **Name**: "เริ่มการค้นหา"

#### 2. Google Sheets Node
- **Operation**: Get row(s) in sheet
- **Range**: `A1:H200`
- **Header Row**: Yes

#### 3. Code Node
```javascript
// กรองเฉพาะลูกค้าที่ Active
const activeCustomers = $input.all().filter(item => 
  item.json.Status === 'Active'
);

return activeCustomers;
```

#### 4. Table Node
- **Name**: "ลูกค้า Active"

---

## Workflow 3: เพิ่มลูกค้าใหม่

### โครงสร้าง Workflow:
```
Manual Trigger → Set → Google Sheets (Append) → Edit Fields
```

### Node Configuration:

#### 1. Manual Trigger
- **Name**: "เพิ่มลูกค้าใหม่"

#### 2. Set Node
```javascript
// ข้อมูลลูกค้าใหม่
return [
  {
    json: {
      ID: 101,
      Name: "ทดสอบ ใหม่",
      Email: "test.new@email.com",
      Phone: "099-999-9999",
      Registration_Date: new Date().toISOString().split('T')[0],
      Status: "Active",
      Last_Update: new Date().toISOString().split('T')[0],
      Notes: "เพิ่มจาก n8n"
    }
  }
];
```

#### 3. Google Sheets Node
- **Operation**: Append row in sheet
- **Range**: `A:H`
- **Header Row**: Yes

#### 4. Edit Fields Node
```javascript
// แสดงผลการเพิ่มข้อมูล
return [
  {
    json: {
      message: `เพิ่มลูกค้า ${$json.Name} สำเร็จแล้ว`,
      customer_id: $json.ID,
      timestamp: new Date().toISOString()
    }
  }
];
```

---

## Workflow 4: อัปเดต Status ลูกค้า

### โครงสร้าง Workflow:
```
Manual Trigger → Set → Google Sheets (Lookup) → Switch → Google Sheets (Update)
```

### Node Configuration:

#### 1. Manual Trigger
- **Name**: "อัปเดต Status"

#### 2. Set Node (ข้อมูลค้นหา)
```javascript
// ข้อมูลที่ต้องการอัปเดต
return [
  {
    json: {
      search_email: "somchai.jaidee@email.com",
      new_status: "Inactive",
      update_note: "ปิดบัญชีตามคำขอ"
    }
  }
];
```

#### 3. Google Sheets Node (Lookup)
- **Operation**: Lookup
- **Lookup Column**: Email
- **Lookup Value**: `{{$json.search_email}}`

#### 4. Switch Node
```javascript
// ตรวจสอบว่าเจอข้อมูลไหม
return [
  {
    json: {
      found: $json.ID ? true : false,
      data: $json
    }
  }
];
```

#### 5. Google Sheets Node (Update)
- **Operation**: Update row in sheet
- **Range**: `F{{$json.row}}:G{{$json.row}}` (Status และ Last_Update)
- **Values**: 
```javascript
[
  [$('Set').first().json.new_status, new Date().toISOString().split('T')[0]]
]
```

---

## Workflow 5: รายงานสรุปลูกค้า

### โครงสร้าง Workflow:
```
Schedule Trigger → Google Sheets (Read) → Code → Set → HTTP Request (Webhook)
```

### Node Configuration:

#### 1. Schedule Trigger
- **Rule**: `0 9 * * 1` (ทุกวันจันทร์ 9:00 AM)
- **Name**: "รายงานประจำสัปดาห์"

#### 2. Google Sheets Node
- **Operation**: Get row(s) in sheet
- **Range**: `A:H`

#### 3. Code Node (สรุปสถิติ)
```javascript
const customers = $input.all();
const stats = {
  total: customers.length,
  active: customers.filter(c => c.json.Status === 'Active').length,
  inactive: customers.filter(c => c.json.Status === 'Inactive').length,
  pending: customers.filter(c => c.json.Status === 'Pending').length,
  vip: customers.filter(c => c.json.Notes?.includes('VIP')).length,
  new_this_week: customers.filter(c => {
    const regDate = new Date(c.json.Registration_Date);
    const weekAgo = new Date();
    weekAgo.setDate(weekAgo.getDate() - 7);
    return regDate > weekAgo;
  }).length
};

return [{ json: stats }];
```

#### 4. Set Node (จัดรูปแบบรายงาน)
```javascript
const stats = $json;
const report = `📊 รายงานลูกค้าประจำสัปดาห์

👥 จำนวนลูกค้าทั้งหมด: ${stats.total} คน
✅ ลูกค้า Active: ${stats.active} คน  
❌ ลูกค้า Inactive: ${stats.inactive} คน
⏳ ลูกค้า Pending: ${stats.pending} คน
⭐ ลูกค้า VIP: ${stats.vip} คน
🆕 ลูกค้าใหม่สัปดาห์นี้: ${stats.new_this_week} คน

📅 สร้างรายงาน: ${new Date().toLocaleDateString('th-TH')}`;

return [{ json: { report, stats } }];
```

---

## Workflow 6: ตรวจสอบลูกค้าที่ไม่ได้ติดต่อนาน

### โครงสร้าง Workflow:
```
Schedule Trigger → Google Sheets (Read) → Code → IF → Google Sheets (Update)
```

### Node Configuration:

#### 1. Schedule Trigger
- **Rule**: `0 10 * * *` (ทุกวัน 10:00 AM)

#### 2. Google Sheets Node
- **Operation**: Get row(s) in sheet
- **Range**: `A:H`

#### 3. Code Node
```javascript
const customers = $input.all();
const thirtyDaysAgo = new Date();
thirtyDaysAgo.setDate(thirtyDaysAgo.getDate() - 30);

const oldCustomers = customers.filter(customer => {
  const lastUpdate = new Date(customer.json.Last_Update);
  return customer.json.Status === 'Active' && lastUpdate < thirtyDaysAgo;
});

return oldCustomers;
```

#### 4. IF Node
```javascript
// ตรวจสอบว่ามีลูกค้าที่ต้องติดตามไหม
return $input.all().length > 0;
```

#### 5. Set Node (สร้างรายการติดตาม)
```javascript
const followUpList = $input.all().map(customer => ({
  ...customer.json,
  follow_up_required: true,
  follow_up_reason: 'ไม่ได้ติดต่อมากกว่า 30 วัน'
}));

return followUpList.map(item => ({ json: item }));
```

---

## การใช้งานแต่ละ Workflow

### Workflow 1: การดูข้อมูล
```
🎯 ใช้เมื่อ: ต้องการดูข้อมูลลูกค้าทั้งหมด
🔧 วิธีใช้: คลิก Execute Workflow
📊 ผลลัพธ์: ตารางแสดงข้อมูลลูกค้าทั้งหมด
```

### Workflow 2: การกรองข้อมูล
```
🎯 ใช้เมื่อ: ต้องการเฉพาะลูกค้า Active
🔧 วิธีใช้: คลิก Execute Workflow  
📊 ผลลัพธ์: เฉพาะลูกค้าที่มีสถานะ Active
```

### Workflow 3: เพิ่มลูกค้า
```
🎯 ใช้เมื่อ: มีลูกค้าใหม่
🔧 วิธีใช้: แก้ไขข้อมูลใน Set Node แล้ว Execute
📊 ผลลัพธ์: เพิ่มลูกค้าใหม่ลง Google Sheet
```

### Workflow 4: อัปเดตข้อมูล
```
🎯 ใช้เมื่อ: ต้องการเปลี่ยน Status ลูกค้า
🔧 วิธีใช้: แก้ไข email และ status ใหม่ใน Set Node
📊 ผลลัพธ์: อัปเดต Status ใน Google Sheet
```

### Workflow 5: รายงานอัตโนมัติ
```
🎯 ใช้เมื่อ: ต้องการรายงานประจำ
🔧 วิธีใช้: ตั้งเวลาอัตโนมัติ หรือ Execute ทดสอบ
📊 ผลลัพธ์: รายงานสรุปสถิติลูกค้า
```

### Workflow 6: ติดตามลูกค้า
```
🎯 ใช้เมื่อ: ต้องการหาลูกค้าที่ไม่ได้ติดต่อนาน
🔧 วิธีใช้: ตั้งเวลาอัตโนมัติทุกวัน
📊 ผลลัพธ์: รายการลูกค้าที่ต้องติดตาม
```

---

## เทคนิคการใช้งาน

### 1. การ Debug
- ใช้ **Table Node** เพื่อดูข้อมูลใน step ต่างๆ
- เปิด **Debug Mode** ใน n8n
- ดู **JSON** output ของแต่ละ Node

### 2. การจัดการ Error
```javascript
// ใน Code Node
try {
  // ประมวลผลข้อมูล
  const result = processData($json);
  return [{ json: result }];
} catch (error) {
  return [{ 
    json: { 
      error: true, 
      message: error.message,
      timestamp: new Date().toISOString()
    }
  }];
}
```

### 3. การตั้งค่า Environment Variables
```javascript
// สำหรับข้อมูลที่ใช้บ่อยๆ
const SHEET_ID = $vars.CUSTOMER_SHEET_ID;
const EMAIL_SENDER = $vars.COMPANY_EMAIL;
```

---

## ข้อควรระวัง

### 1. Rate Limiting
- Google Sheets API มีขีดจำกัดการเรียกใช้
- ใช้ **Wait Node** หากมีการเรียกใช้บ่อย
- หลีกเลี่ยงการ loop ที่ไม่จำเป็น

### 2. ข้อมูลขนาดใหญ่
- แบ่งการประมวลผลเป็นชุดย่อยๆ
- ใช้ **Limit** ใน Google Sheets Node
- พิจารณาใช้ Database แทนสำหรับข้อมูลมาก

### 3. การรักษาความปลอดภัย
- เก็บ Credentials ไว้อย่างปลอดภัย
- ไม่แชร์ Service Account Key
- ตั้งสิทธิ์ Google Sheet เท่าที่จำเป็น

---

## การเริ่มต้น

1. **เริ่มจาก Workflow 1** เพื่อทดสอบการเชื่อมต่อ
2. **ลอง Workflow 3** เพื่อทดสอบการเพิ่มข้อมูล  
3. **ปรับแต่งตามความต้องการ**
4. **ตั้งค่า Schedule** สำหรับ automation

---

**Tips**: เก็บไฟล์นี้ไว้เป็น reference และปรับแต่งตามการใช้งานจริงของคุณ!
