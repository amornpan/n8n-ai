# Hands-on 1: ระบบแจ้งเตือนสภาพอากาศ 🌤️

**ระดับ:** เริ่มต้น  
**เวลาที่ใช้:** 45-60 นาที  
**สิ่งที่จะได้:** ระบบแจ้งเตือนอากาศอัตโนมัติที่ทำงานทุกเช้า

---

## 🎯 เป้าหมาย

สร้างระบบที่:
1. เช็คสภาพอากาศทุกเช้า 8:00 น.
2. ถ้าฝนตกส่ง Slack แจ้งเตือน
3. บันทึกข้อมูลลง Google Sheets ทุกวัน
4. ส่งสรุปสภาพอากาศประจำสัปดาห์

---

## 📋 เตรียมความพร้อม

### 1. API Keys ที่ต้องมี
- ✅ OpenWeatherMap API (ฟรี)
- ✅ Slack Webhook URL (ฟรี)
- ✅ Google Sheets API (ฟรี)

### 2. เครื่องมือที่ต้องติดตั้ง
- ✅ n8n (ติดตั้งแล้ว)
- ✅ Google Account
- ✅ Slack Workspace

---

## 🔑 ขั้นตอนที่ 1: เตรียม API Keys

### 1.1 สมัคร OpenWeatherMap API

**Step 1:** ไปที่ https://openweathermap.org/api
1. คลิก "Sign Up" (ถ้ายังไม่มี account)
2. ยืนยันอีเมล
3. ไปที่ "API Keys" tab
4. คัดลอก API key

**Step 2:** ทดสอบ API
```bash
# เปิด browser ทดสอบ URL นี้
https://api.openweathermap.org/data/2.5/weather?q=Bangkok&appid=YOUR_API_KEY&units=metric
```

**ผลลัพธ์ที่ควรได้:**
```json
{
  "main": {
    "temp": 32.5,
    "humidity": 65
  },
  "weather": [
    {
      "main": "Rain",
      "description": "light rain"
    }
  ]
}
```

### 1.2 สร้าง Slack Webhook URL

**Step 1:** เข้าสู่ Slack Workspace
1. ไปที่ https://api.slack.com/apps
2. คลิก "Create New App" > "From scratch"
3. ตั้งชื่อ App: "Weather Alert Bot"
4. เลือก Workspace ที่ต้องการ

**Step 2:** เปิดใช้ Incoming Webhooks
1. ในหน้า App Settings ไปที่ "Incoming Webhooks"
2. Toggle เปิด "Activate Incoming Webhooks"
3. คลิก "Add New Webhook to Workspace"
4. เลือก Channel ที่ต้องการส่งข้อความ (เช่น #general หรือ #weather)
5. คลิก "Allow"
6. **คัดลอก Webhook URL** (ขึ้นต้นด้วย https://hooks.slack.com/services/)

**Step 3:** ทดสอบ Slack Webhook
```bash
curl -X POST -H 'Content-type: application/json' \
--data '{"text":"ทดสอบการส่งข้อความจาก Weather Bot 🌤️"}' \
YOUR_WEBHOOK_URL
```

**การปรับแต่ง Slack Message (ขั้นสูง):**
```json
{
  "channel": "#weather",
  "username": "Weather Bot",
  "icon_emoji": ":sun_with_face:",
  "attachments": [
    {
      "color": "good",
      "fields": [
        {
          "title": "อุณหภูมิ",
          "value": "32°C",
          "short": true
        }
      ]
    }
  ]
}
```

### 1.3 เตรียม Google Sheets API

**Step 1:** ไปที่ Google Cloud Console
1. ไปที่ https://console.cloud.google.com/
2. สร้าง Project ใหม่: "n8n-weather"
3. เปิดใช้ Google Sheets API:
   - ไปที่ "APIs & Services" > "Library"
   - ค้นหา "Google Sheets API"
   - คลิก "Enable"

**Step 2:** สร้าง Service Account
1. ไปที่ "APIs & Services" > "Credentials"
2. คลิก "Create Credentials" > "Service Account"
3. ชื่อ: "n8n-weather-bot"
4. สร้าง Key (JSON format)
5. ดาวน์โหลดไฟล์ JSON

**Step 3:** สร้าง Google Sheet
1. ไปที่ https://sheets.google.com
2. สร้าง Sheet ใหม่ชื่อ "Weather Data"
3. ตั้งชื่อ columns ดังนี้:
   ```
   A1: Date
   B1: Time  
   C1: Temperature
   D1: Humidity
   E1: Weather
   F1: Description
   G1: Rain Alert
   ```
4. Share sheet กับ Service Account email
   - คลิก "Share"
   - ใส่ email จากไฟล์ JSON (field: client_email)
   - ให้สิทธิ์ "Editor"

---

## 🔧 ขั้นตอนที่ 2: สร้าง Workflow ใน n8n

### 2.1 เปิด n8n และสร้าง Workflow ใหม่

1. เปิด browser ไปที่ `http://localhost:5678`
2. คลิก "New workflow"
3. บันทึกด้วยชื่อ "Weather Alert System"

### 2.2 Node 1: Schedule Trigger

**Step 1:** ลาก "Schedule Trigger" จาก Triggers
```
Settings:
- Trigger Interval: Cron
- Cron Expression: 0 8 * * *
- Timezone: Asia/Bangkok
```

**อธิบาย Cron:**
- `0 8 * * *` = ทุกวันเวลา 8:00 น.
- `0 20 * * 0` = ทุกวันอาทิตย์เวลา 8:00 น.

**Step 2:** คลิก "Execute Node" เพื่อทดสอบ

### 2.3 Node 2: HTTP Request (ดึงข้อมูลอากาศ)

**Step 1:** เชื่อม Schedule Trigger → HTTP Request

**Step 2:** ตั้งค่า HTTP Request:
```
Authentication: None
Request Method: GET
URL: https://api.openweathermap.org/data/2.5/weather
Query Parameters:
  q: Bangkok
  appid: {{ $env.OPENWEATHER_API_KEY }}
  units: metric
  lang: th
```

**Step 3:** เพิ่ม Environment Variable
1. ไปที่ Settings → Environment Variables
2. เพิ่ม: `OPENWEATHER_API_KEY` = your_actual_api_key

**Step 4:** ทดสอบ
- คลิก "Execute Node"
- ดู Output ว่าได้ข้อมูลอากาศมาหรือไม่

### 2.4 Node 3: Code (ประมวลผลข้อมูล)

**Step 1:** เชื่อม HTTP Request → Code

**Step 2:** ใส่ JavaScript Code:
```javascript
// ดึงข้อมูลจาก API
const weatherData = $input.all()[0].json;

// แยกข้อมูลที่ต้องการ
const temp = Math.round(weatherData.main.temp);
const humidity = weatherData.main.humidity;
const weatherMain = weatherData.weather[0].main;
const description = weatherData.weather[0].description;
const feelsLike = Math.round(weatherData.main.feels_like);

// เช็คว่าฝนตกหรือไม่
const rainKeywords = ['rain', 'drizzle', 'thunderstorm', 'ฝน', 'พายุ'];
const isRaining = rainKeywords.some(keyword => 
  weatherMain.toLowerCase().includes(keyword.toLowerCase()) ||
  description.toLowerCase().includes(keyword.toLowerCase())
);

// สร้างข้อความแจ้งเตือนสำหรับ Slack
let slackMessage = {
  text: "🌤️ รายงานสภาพอากาศประจำวัน",
  attachments: [
    {
      color: isRaining ? "warning" : "good",
      fields: [
        {
          title: "📍 สถานที่",
          value: "กรุงเทพฯ",
          short: true
        },
        {
          title: "🌡️ อุณหภูมิ",
          value: `${temp}°C (รู้สึกเหมือน ${feelsLike}°C)`,
          short: true
        },
        {
          title: "💧 ความชื้น",
          value: `${humidity}%`,
          short: true
        },
        {
          title: "☁️ สภาพอากาศ",
          value: description,
          short: true
        }
      ]
    }
  ]
};

// เพิ่มการแจ้งเตือนพิเศษถ้าฝนตก
if (isRaining) {
  slackMessage.attachments.push({
    color: "danger",
    text: "☔ *แจ้งเตือน:* วันนี้มีฝนตก อย่าลืมพกร่มนะ! 🌂"
  });
}

// สร้างข้อมูลสำหรับ Google Sheets
const currentDate = new Date();
const dateStr = currentDate.toLocaleDateString('th-TH');
const timeStr = currentDate.toLocaleTimeString('th-TH');

return {
  temperature: temp,
  humidity: humidity,
  weather: weatherMain,
  description: description,
  isRaining: isRaining,
  slackMessage: slackMessage,
  dateStr: dateStr,
  timeStr: timeStr,
  feelsLike: feelsLike
};
```

**Step 3:** ทดสอบ Code
- คลิก "Execute Node"
- ตรวจสอบ Output ว่าได้ข้อมูลที่ประมวลผลแล้ว

### 2.5 Node 4: IF (เช็คเงื่อนไขฝนตก)

**Step 1:** เชื่อม Code → IF

**Step 2:** ตั้งค่า IF:
```
Conditions:
- Value 1: {{ $json.isRaining }}
- Operation: Equal
- Value 2: true
```

### 2.6 Node 5: HTTP Request (Slack Notification) - เชื่อมจาก True

**Step 1:** เชื่อมจาก "True" output ของ IF Node

**Step 2:** ตั้งค่า HTTP Request:
```
Authentication: None
Request Method: POST
URL: {{ $env.SLACK_WEBHOOK_URL }}
Headers:
  Content-Type: application/json
Body:
  Content Type: JSON
  JSON: {{ $json.slackMessage }}
```

**Step 3:** เพิ่ม Environment Variable:
- `SLACK_WEBHOOK_URL` = your_slack_webhook_url

**ทางเลือก: ส่งข้อความทุกวัน (ไม่ว่าฝนตกหรือไม่)**
- เชื่อม Node นี้จาก Code Node โดยตรง แทนที่จะผ่าน IF Node

### 2.7 Node 6: Google Sheets (บันทึกข้อมูล)

**Step 1:** เชื่อมจาก Code Node (ไม่ใช่ IF)

**Step 2:** ตั้งค่า Google Sheets:
```
Authentication: Service Account
  Service Account Email: จากไฟล์ JSON
  Private Key: จากไฟล์ JSON
  
Operation: Append
Document: เลือก "Weather Data" sheet
Range: A:G
Values:
  - {{ $json.dateStr }}
  - {{ $json.timeStr }}
  - {{ $json.temperature }}
  - {{ $json.humidity }}
  - {{ $json.weather }}
  - {{ $json.description }}
  - {{ $json.isRaining }}
```

---

## 🧪 ขั้นตอนที่ 3: ทดสอบระบบ

### 3.1 ทดสอบทีละ Node

1. **Schedule Trigger**: คลิก "Execute Node"
2. **HTTP Request**: ตรวจสอบได้ข้อมูลอากาศ
3. **Code**: ตรวจสอบการประมวลผล
4. **IF**: ตรวจสอบ logic การแยกทาง
5. **Slack Notification**: ตรวจสอบได้รับข้อความใน Slack Channel
6. **Google Sheets**: ตรวจสอบข้อมูลในชีต

### 3.2 ทดสอบ Workflow ทั้งหมด

1. คลิก "Execute Workflow" (ปุ่มใหญ่ข้างบน)
2. ตรวจสอบ:
   - ✅ ได้รับข้อความใน Slack Channel (ถ้าฝนตก)
   - ✅ ข้อมูลถูกบันทึกใน Google Sheets
   - ✅ ไม่มี Error ในแต่ละ Node

### 3.3 ทดสอบ Schedule

1. เปลี่ยน Cron expression เป็น `*/1 * * * *` (ทุกนาที)
2. รอดู 1-2 นาที
3. เปลี่ยนกลับเป็น `0 8 * * *`

---

## 🎨 ขั้นตอนที่ 4: ปรับแต่งและเพิ่มฟีเจอร์

### 4.1 เพิ่มการแจ้งเตือนอุณหภูมิสูง

**แก้ไข Code Node:**
```javascript
// เพิ่มหลัง isRaining check
const isTooHot = temp > 35;
const isTooCold = temp < 20;

// ปรับปรุง Slack Message
if (isTooHot) {
  slackMessage.attachments.push({
    color: "danger",
    text: "🔥 *เตือน:* อากาศร้อนมาก! ดื่มน้ำเยอะๆ นะ"
  });
} else if (isTooCold) {
  slackMessage.attachments.push({
    color: "warning",
    text: "🧊 *เตือน:* อากาศเย็น อย่าลืมใส่เสื้อกันหนาว"
  });
}

// เพิ่มใน return
return {
  // ... existing fields
  isTooHot: isTooHot,
  isTooCold: isTooCold
};
```

### 4.2 เพิ่ม Weekly Summary

**เพิ่ม Schedule Trigger ใหม่:**
```
Cron: 0 20 * * 0  // ทุกวันอาทิตย์ 8 โมงเย็น
```

**เพิ่ม Code สำหรับสรุปสัปดาห์:**
```javascript
// อ่านข้อมูล 7 วันที่ผ่านมา
const sevenDaysAgo = new Date();
sevenDaysAgo.setDate(sevenDaysAgo.getDate() - 7);

// สร้างสรุปสัปดาห์สำหรับ Slack
let weeklySlackMessage = {
  text: "📊 สรุปสภาพอากาศสัปดาห์นี้",
  attachments: [
    {
      color: "good",
      fields: [
        {
          title: "📅 ช่วงเวลา",
          value: `${sevenDaysAgo.toLocaleDateString('th-TH')} - ${new Date().toLocaleDateString('th-TH')}`,
          short: false
        }
        // เพิ่มสถิติต่างๆ
      ]
    }
  ]
};

return { weeklySlackMessage: weeklySlackMessage };
```

### 4.3 เพิ่ม Slack Thread และ Interactive Elements

**Slack Message ขั้นสูง:**
```javascript
let advancedSlackMessage = {
  text: "🌤️ รายงานสภาพอากาศประจำวัน",
  blocks: [
    {
      type: "header",
      text: {
        type: "plain_text",
        text: "🌤️ รายงานสภาพอากาศวันนี้"
      }
    },
    {
      type: "section",
      fields: [
        {
          type: "mrkdwn",
          text: `*📍 สถานที่:*\nกรุงเทพฯ`
        },
        {
          type: "mrkdwn",
          text: `*🌡️ อุณหภูมิ:*\n${temp}°C`
        },
        {
          type: "mrkdwn",
          text: `*💧 ความชื้น:*\n${humidity}%`
        },
        {
          type: "mrkdwn",
          text: `*☁️ สภาพอากาศ:*\n${description}`
        }
      ]
    }
  ]
};

if (isRaining) {
  advancedSlackMessage.blocks.push({
    type: "section",
    text: {
      type: "mrkdwn",
      text: "☔ *แจ้งเตือน:* วันนี้มีฝนตก อย่าลืมพกร่มนะ! 🌂"
    }
  });
}
```

### 4.4 เพิ่ม Error Handling

**แก้ไข HTTP Request Node:**
```javascript
// เพิ่มใน Settings
Continue on Fail: true
```

**เพิ่ม Code สำหรับ Error:**
```javascript
const input = $input.all()[0];

if (!input.json || input.json.error) {
  const errorSlackMessage = {
    text: "❌ ข้อผิดพลาด",
    attachments: [
      {
        color: "danger",
        text: "ไม่สามารถดึงข้อมูลอากาศได้ กรุณาตรวจสอบระบบ"
      }
    ]
  };
  
  return {
    error: true,
    slackMessage: errorSlackMessage
  };
}

// ... rest of code
```

---

## 📊 ขั้นตอนที่ 5: Monitor และ Analytics

### 5.1 สร้าง Dashboard ใน Google Sheets

1. เปิด Google Sheets
2. สร้าง Sheet ใหม่ชื่อ "Dashboard"
3. สร้างกราฟ:
   ```
   A1: =SPARKLINE(FILTER('Weather Data'!C:C,'Weather Data'!A:A<>"")))
   A2: อุณหภูมิ 7 วันที่ผ่านมา
   
   B1: =AVERAGE(FILTER('Weather Data'!C:C,'Weather Data'!A:A>=TODAY()-7))
   B2: อุณหภูมิเฉลี่ย
   
   C1: =COUNTIF(FILTER('Weather Data'!G:G,'Weather Data'!A:A>=TODAY()-7),TRUE)
   C2: วันที่ฝนตก
   ```

### 5.2 ตั้งค่า Slack Channel Organization

**สร้าง Dedicated Weather Channel:**
1. สร้าง channel ใหม่: `#weather-alerts`
2. ตั้งค่า channel description: "อัพเดทสภาพอากาศอัตโนมัติจาก Weather Bot"
3. Pin ข้อความสำคัญเป็น reference

**เพิ่ม Slack App Icon และ Display Name:**
1. ไปที่ Slack App Settings
2. ใน "Display Information" เพิ่ม:
   - App name: "Weather Alert Bot"
   - Short description: "ระบบแจ้งเตือนสภาพอากาศอัตโนมัติ"
   - App icon: อัพโหลดรูป weather icon

### 5.3 ตั้งค่า Monitoring

**เพิ่ม Node สำหรับ Health Check:**
```javascript
// ตรวจสอบว่า workflow ทำงานปกติ
const lastRun = new Date();
const healthStatus = {
  timestamp: lastRun.toISOString(),
  status: "healthy",
  slackMessage: {
    text: "✅ Weather Alert System Status",
    attachments: [
      {
        color: "good",
        fields: [
          {
            title: "Status",
            value: "System running normally",
            short: true
          },
          {
            title: "Last Check",
            value: lastRun.toLocaleString('th-TH'),
            short: true
          }
        ]
      }
    ]
  }
};

return healthStatus;
```

---

## 🔍 ขั้นตอนที่ 6: Troubleshooting

### ปัญหาที่พบบ่อย

**1. OpenWeatherMap API ไม่ทำงาน**
```
Error: 401 Unauthorized
Solution: ตรวจสอบ API key ใน Environment Variables
```

**2. Slack Webhook ไม่ส่งข้อความ**
```
Error: 400 Bad Request
Solution: 
- ตรวจสอบ Webhook URL
- ตรวจสอบ JSON format ของ message
- ตรวจสอบว่า Slack App ยังอยู่ใน Workspace
```

**3. Slack Message Format ผิด**
```
Error: Invalid payload
Solution:
- ใช้ Slack Block Kit Builder: https://app.slack.com/block-kit-builder
- ตรวจสอบ JSON syntax
- ทดสอบด้วย simple text message ก่อน
```

**4. Google Sheets ไม่บันทึกข้อมูล**
```
Error: 403 Forbidden
Solution: ตรวจสอบ Service Account permissions
```

### Debug Tips

1. **ใช้ Console Log:**
```javascript
console.log("Weather data:", weatherData);
console.log("Slack message:", slackMessage);
```

2. **ทดสอบ Slack Message:**
- ใช้ Slack's webhook tester
- ส่ง simple message ก่อน
- ตรวจสอบ JSON format

3. **ตรวจสอบ HTTP Response:**
- ดู Status Code (200 = สำเร็จ)
- ดู Response Body
- ตรวจสอบ Headers

4. **ทดสอบ Manual:**
- ใช้ Manual Trigger แทน Schedule
- ทดสอบทีละ Node
- ตรวจสอบ Data Flow

---

## 🎉 สรุป

หลังจากทำ Hands-on นี้เสร็จ คุณจะได้:

✅ **ระบบแจ้งเตือนอากาศอัตโนมัติผ่าน Slack**  
✅ **ความรู้เรื่อง API Integration**  
✅ **การใช้ Google Sheets เป็น Database**  
✅ **การส่งแจ้งเตือนผ่าน Slack Webhook**  
✅ **การจัดการ Environment Variables**  
✅ **พื้นฐาน Error Handling**  
✅ **การใช้ Slack Block Kit สำหรับ Rich Messages**

### Next Steps

1. ลองปรับแต่งข้อความ Slack และ Block Layout
2. เพิ่มเมืองอื่นๆ ในประเทศไทย
3. เพิ่มการแจ้งเตือนคุณภาพอากาศ (AQI)
4. สร้าง Slack Slash Commands สำหรับเช็คอากาศแบบ Manual
5. เพิ่มการพยากรณ์อากาศ 3-5 วัน
6. เชื่อมต่อกับ Slack Calendar เพื่อแจ้งเตือนก่อนมีนัดหมายกลางแจ้ง

**🚀 พร้อมแล้วสำหรับ Hands-on ถัดไป!**