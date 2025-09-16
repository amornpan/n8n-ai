# Hands-on 3: AI Customer Support Bot 🤖

**ระดับ:** กลาง-สูง  
**เวลาที่ใช้:** 90-120 นาที  
**สิ่งที่จะได้:** AI Bot ที่ตอบคำถามลูกค้าอัตโนมัติและส่งต่อ human agent เมื่อจำเป็น

---

## 🎯 เป้าหมาย

สร้าง AI Customer Support Bot ที่:
1. รับคำถามจาก Line Bot, Discord, หรือ Webhook
2. ใช้ OpenAI GPT-4 วิเคราะห์และตอบคำถาม
3. ตรวจจับความเร่งด่วนและส่งต่อ human agent
4. เก็บ conversation history และวิเคราะห์ patterns
5. สร้างรายงาน customer satisfaction อัตโนมัติ
6. มีระบบ escalation แบบอัตโนมัติ

---

## 📋 เตรียมความพร้อม

### 1. API Keys ที่ต้องมี
- ✅ OpenAI API Key (หรือ Claude API)
- ✅ Line Bot API (ฟรี)
- ✅ Discord Bot Token (ฟรี)
- ✅ Google Sheets API (ฟรี)

### 2. Knowledge Base
- ✅ FAQ เกี่ยวกับสินค้า/บริการ
- ✅ Company policies
- ✅ Contact information
- ✅ Escalation procedures

---

## 🔑 ขั้นตอนที่ 1: เตรียม AI และ Messaging APIs

### 1.1 เตรียม OpenAI API

**Step 1:** สมัคร OpenAI Account
1. ไปที่ https://platform.openai.com/
2. Sign up หรือ login
3. เติมเงินใน Billing (อย่างน้อย $5)

**Step 2:** สร้าง API Key
1. ไปที่ "API Keys"
2. "Create new secret key"
3. ชื่อ: "n8n-customer-support"
4. คัดลอก key

**Step 3:** ทดสอบ API
```bash
curl https://api.openai.com/v1/chat/completions \
-H "Content-Type: application/json" \
-H "Authorization: Bearer YOUR_API_KEY" \
-d '{
  "model": "gpt-4",
  "messages": [{"role": "user", "content": "Hello!"}],
  "max_tokens": 100
}'
```

### 1.2 สร้าง Line Bot

**Step 1:** สร้าง Line Developer Account
1. ไปที่ https://developers.line.biz/
2. Login ด้วย Line account
3. สร้าง Provider ใหม่: "n8n Support Bot"

**Step 2:** สร้าง Messaging API Channel
1. คลิก "Create a Messaging API channel"
2. กรอกข้อมูล:
   ```
   Channel name: Customer Support Bot
   Channel description: AI-powered customer support
   Category: Customer Support
   Subcategory: Other
   ```

**Step 3:** เก็บ API Credentials
```
Channel Secret: your_channel_secret
Channel Access Token: your_channel_access_token
```

**Step 4:** ตั้งค่า Webhook URL (จะได้จาก n8n ในขั้นตอนถัดไป)

### 1.3 สร้าง Discord Bot

**Step 1:** สร้าง Discord Application
1. ไปที่ https://discord.com/developers/applications
2. "New Application"
3. ชื่อ: "Customer Support AI"

**Step 2:** สร้าง Bot
1. ไปที่ "Bot" tab
2. "Add Bot"
3. เก็บ Token

**Step 3:** เชิญ Bot เข้า Server
1. ไปที่ "OAuth2" → "URL Generator"
2. เลือก Scopes: "bot"
3. เลือก Permissions: "Send Messages", "Read Message History"
4. คัดลอก URL และเปิดในเบราว์เซอร์

### 1.4 เตรียม Knowledge Base

**Step 1:** สร้าง Google Sheet ชื่อ "Customer Support Knowledge"

**Step 2:** สร้าง Tabs:
- **FAQ** - คำถามที่พบบ่อย
- **Products** - ข้อมูลสินค้า
- **Policies** - นโยบายบริษัท
- **Contacts** - ข้อมูลติดต่อ
- **Conversations** - เก็บ chat history

**Step 3:** ใส่ข้อมูลตัวอย่างใน FAQ tab:
```
A1: Category | B1: Question | C1: Answer | D1: Keywords
A2: General | B2: What are your business hours? | C2: We're open Monday-Friday 9AM-6PM | D2: hours,time,open
A3: Shipping | B3: How long does shipping take? | C3: Standard shipping takes 3-5 business days | D3: shipping,delivery,time
A4: Returns | B4: What is your return policy? | C4: We accept returns within 30 days of purchase | D4: return,refund,exchange
A5: Support | B5: How can I contact support? | C5: You can reach us via Line, email, or phone | D5: contact,support,help
```

---

## 🔧 ขั้นตอนที่ 2: สร้าง Main AI Bot Workflow

### 2.1 สร้าง Webhook Workflow

**Step 1:** สร้าง workflow ใหม่ชื่อ "AI Customer Support"

**Step 2:** เพิ่ม Webhook Node
```
Authentication: None
HTTP Method: POST
Path: /customer-support
Respond: Immediately
Response Code: 200
```

**Step 3:** คัดลอก Webhook URL (จะใช้ตั้งค่า Line Bot)

### 2.2 Node 1: Code (Parse Incoming Message)

**Step 1:** เชื่อม Webhook → Code

**Step 2:** ใส่ JavaScript Code:
```javascript
// รับข้อมูลจาก webhook
const webhookData = $input.all()[0].json;
let messageData = {};

console.log("Webhook data:", JSON.stringify(webhookData, null, 2));

// ตรวจสอบว่าข้อมูลมาจากแพลตฟอร์มไหน
if (webhookData.events && webhookData.events[0]) {
  // Line Bot
  const event = webhookData.events[0];
  messageData = {
    platform: 'line',
    userId: event.source.userId,
    messageText: event.message?.text || '',
    messageType: event.message?.type || 'text',
    replyToken: event.replyToken,
    timestamp: new Date(event.timestamp).toISOString(),
    eventType: event.type
  };
} else if (webhookData.author && webhookData.content) {
  // Discord (ถ้าใช้ webhook)
  messageData = {
    platform: 'discord',
    userId: webhookData.author.id,
    username: webhookData.author.username,
    messageText: webhookData.content,
    messageType: 'text',
    channelId: webhookData.channel_id,
    timestamp: webhookData.timestamp
  };
} else {
  // Generic webhook
  messageData = {
    platform: 'webhook',
    userId: webhookData.userId || 'anonymous',
    messageText: webhookData.message || webhookData.text || '',
    messageType: 'text',
    timestamp: new Date().toISOString()
  };
}

// กรองข้อความที่ไม่ใช่ text หรือเป็นข้อความว่าง
if (messageData.messageType !== 'text' || !messageData.messageText.trim()) {
  return [{ 
    json: { 
      skip: true, 
      reason: 'Not a text message or empty message'
    }
  }];
}

// ทำความสะอาดข้อความ
messageData.messageText = messageData.messageText.trim();

// ตรวจสอบคำสำคัญเร่งด่วน
const urgentKeywords = [
  'urgent', 'emergency', 'asap', 'immediately', 'critical',
  'ด่วน', 'เร่งด่วน', 'เหตุฉุกเฉิน', 'รีบ', 'สำคัญมาก'
];

const isUrgent = urgentKeywords.some(keyword => 
  messageData.messageText.toLowerCase().includes(keyword.toLowerCase())
);

// ตรวจสอบคำที่บ่งบอกถึงความไม่พอใจ
const negativeKeywords = [
  'angry', 'frustrated', 'disappointed', 'terrible', 'awful', 'horrible',
  'โกรธ', 'หงุดหงิด', 'ผิดหวัง', 'แย่', 'ไม่พอใจ', 'เสียใจ'
];

const isNegative = negativeKeywords.some(keyword => 
  messageData.messageText.toLowerCase().includes(keyword.toLowerCase())
);

// ประเมินความซับซ้อนของคำถาม
const complexityIndicators = [
  'explain', 'how to', 'step by step', 'detailed', 'complex',
  'อธิบาย', 'วิธีการ', 'ขั้นตอน', 'รายละเอียด', 'ซับซ้อน'
];

const isComplex = complexityIndicators.some(keyword => 
  messageData.messageText.toLowerCase().includes(keyword.toLowerCase())
) || messageData.messageText.length > 200;

console.log(`Message analysis:`, {
  isUrgent,
  isNegative, 
  isComplex,
  length: messageData.messageText.length
});

return [{
  json: {
    ...messageData,
    isUrgent: isUrgent,
    isNegative: isNegative,
    isComplex: isComplex,
    priority: isUrgent ? 'high' : isNegative ? 'medium' : 'normal'
  }
}];
```

### 2.3 Node 2: IF (Check if should skip)

**Step 1:** เชื่อม Code → IF

**Step 2:** ตั้งค่า IF:
```
Conditions:
- Value 1: {{ $json.skip }}
- Operation: Equal
- Value 2: true
```

**Step 3:** เชื่อม "True" ไปยัง Stop and Error node (เพื่อหยุดการทำงาน)

### 2.4 Node 3: Google Sheets (Read Knowledge Base)

**Step 1:** เชื่อมจาก "False" output ของ IF → Google Sheets

**Step 2:** ตั้งค่า Google Sheets:
```
Authentication: Service Account (ใช้จาก hands-on 1)

Operation: Read
Document: Customer Support Knowledge
Sheet: FAQ
Range: A:D
```

### 2.5 Node 4: Code (Search Knowledge Base)

**Step 1:** เชื่อม Google Sheets → Code

**Step 2:** ใส่ Code เพื่อค้นหาข้อมูลที่เกี่ยวข้อง:
```javascript
// รับข้อมูลจาก Knowledge Base และ user message
const knowledgeData = $input.all()[0].json;
const userMessage = $input.all()[1].json;
const customerQuestion = userMessage.messageText.toLowerCase();

console.log("Searching knowledge base for:", customerQuestion);

// ข้าม header row
const faqData = knowledgeData.slice(1);

// ค้นหาคำตอบที่เกี่ยวข้อง
let relevantAnswers = [];
let confidence = 0;

faqData.forEach((row, index) => {
  const [category, question, answer, keywords] = row;
  
  if (!question || !answer) return;
  
  // คำนวณ relevance score
  let score = 0;
  
  // เช็คใน keywords
  if (keywords) {
    const keywordList = keywords.toLowerCase().split(',');
    keywordList.forEach(keyword => {
      if (customerQuestion.includes(keyword.trim())) {
        score += 3;
      }
    });
  }
  
  // เช็คใน question
  const questionWords = question.toLowerCase().split(' ');
  questionWords.forEach(word => {
    if (word.length > 3 && customerQuestion.includes(word)) {
      score += 2;
    }
  });
  
  // เช็คใน category
  if (customerQuestion.includes(category.toLowerCase())) {
    score += 1;
  }
  
  if (score > 0) {
    relevantAnswers.push({
      category,
      question,
      answer,
      score,
      index
    });
  }
});

// เรียงตาม score
relevantAnswers.sort((a, b) => b.score - a.score);

// คำนวณ confidence
if (relevantAnswers.length > 0) {
  const topScore = relevantAnswers[0].score;
  confidence = Math.min(topScore / 10, 1); // normalize to 0-1
}

// เตรียมข้อมูลสำหรับ AI
const context = relevantAnswers.slice(0, 3).map(item => 
  `คำถาม: ${item.question}\nคำตอบ: ${item.answer}`
).join('\n\n');

console.log("Found relevant answers:", relevantAnswers.length);
console.log("Confidence:", confidence);

return [{
  json: {
    ...userMessage,
    knowledgeContext: context,
    relevantAnswers: relevantAnswers,
    confidence: confidence,
    hasKnowledge: relevantAnswers.length > 0
  }
}];
```

### 2.6 Node 5: OpenAI (AI Response Generation)

**Step 1:** เชื่อม Code → OpenAI

**Step 2:** ตั้งค่า OpenAI Node:
```
Authentication: HTTP Header Auth
- Header Name: Authorization
- Header Value: Bearer {{ $env.OPENAI_API_KEY }}

Resource: Chat
Model: gpt-4
Temperature: 0.3
Max Tokens: 300

System Message:
คุณเป็น AI Customer Support ที่เป็นมิตรและมีประสิทธิภาพของบริษัท [ชื่อบริษัทของคุณ]

กฎการตอบคำถาม:
1. ตอบด้วยความสุภาพและเป็นมิตรเสมอ
2. ใช้ข้อมูลจาก Knowledge Base เป็นหลัก
3. ถ้าไม่แน่ใจ ให้บอกว่าจะส่งต่อให้ทีมที่เกี่ยวข้อง
4. ตอบเป็นภาษาไทยหรือภาษาเดียวกับที่ลูกค้าใช้
5. ตอบสั้นกระชับ ไม่เกิน 3 ประโยค
6. ถ้าเป็นเรื่องซับซ้อนหรือต้องการความช่วยเหลือพิเศษ ให้แนะนำให้ติดต่อ human agent

User Message:
คำถาม: {{ $json.messageText }}

ข้อมูลอ้างอิง:
{{ $json.knowledgeContext }}

ประเภทความสำคัญ: {{ $json.priority }}
ความซับซ้อน: {{ $json.isComplex ? 'สูง' : 'ปกติ' }}
```

### 2.7 Node 6: Code (Process AI Response)

**Step 1:** เชื่อม OpenAI → Code

**Step 2:** ประมวลผลคำตอบจาก AI:
```javascript
// รับข้อมูลจาก OpenAI
const aiResponse = $input.all()[0].json;
const userData = $input.all()[1].json;

let aiAnswer = '';
let confidence = userData.confidence || 0;

// ดึง response จาก OpenAI
if (aiResponse.choices && aiResponse.choices[0]) {
  aiAnswer = aiResponse.choices[0].message.content.trim();
} else {
  aiAnswer = 'ขออภัย เกิดข้อผิดพลาดในระบบ กรุณาลองใหม่อีกครั้ง';
  confidence = 0;
}

// ตรวจสอบว่า AI ตอบได้หรือควรส่งต่อ human
const escalationPhrases = [
  'ส่งต่อ', 'ติดต่อ', 'ไม่แน่ใจ', 'ไม่ทราบ', 'ช่วยไม่ได้',
  'contact', 'transfer', 'not sure', 'don\'t know', 'can\'t help'
];

const needsEscalation = escalationPhrases.some(phrase =>
  aiAnswer.toLowerCase().includes(phrase.toLowerCase())
) || confidence < 0.3 || userData.isUrgent || 
   (userData.isNegative && userData.isComplex);

// สร้างข้อความสำหรับลูกค้า
let finalMessage = aiAnswer;

if (needsEscalation) {
  finalMessage += '\n\n🔄 เราจะส่งเรื่องของคุณให้ทีมผู้เชี่ยวชาญเพื่อช่วยเหลือเพิ่มเติม กรุณารอสักครู่นะคะ';
}

// เพิ่มข้อมูลติดต่อเพิ่มเติม
if (userData.priority === 'high') {
  finalMessage += '\n\n📞 สำหรับเรื่องเร่งด่วน: โทร 02-xxx-xxxx';
}

console.log("AI Response processed:", {
  needsEscalation,
  confidence,
  priority: userData.priority
});

return [{
  json: {
    ...userData,
    aiAnswer: aiAnswer,
    finalMessage: finalMessage,
    needsEscalation: needsEscalation,
    confidence: confidence,
    responseTimestamp: new Date().toISOString()
  }
}];
```

---

## 📱 ขั้นตอนที่ 3: ส่งคำตอบกลับลูกค้า

### 3.1 Node 7: IF (Check Platform)

**Step 1:** เชื่อม Code → IF

**Step 2:** ตั้งค่า IF เพื่อแยกแพลตฟอร์ม:
```
Conditions:
- Value 1: {{ $json.platform }}
- Operation: Equal
- Value 2: line
```

### 3.2 Node 8a: HTTP Request (Line Reply)

**Step 1:** เชื่อมจาก "True" output

**Step 2:** ตั้งค่า Line Reply:
```
Authentication: HTTP Header Auth
- Header Name: Authorization
- Header Value: Bearer {{ $env.LINE_CHANNEL_ACCESS_TOKEN }}

Request Method: POST
URL: https://api.line.me/v2/bot/message/reply

Headers:
  Content-Type: application/json

Body:
{
  "replyToken": "{{ $json.replyToken }}",
  "messages": [
    {
      "type": "text",
      "text": "{{ $json.finalMessage }}"
    }
  ]
}
```

### 3.3 Node 8b: HTTP Request (Discord Reply)

**Step 1:** เชื่อมจาก "False" output

**Step 2:** ตั้งค่า Discord Reply (ถ้าใช้ webhook):
```
Request Method: POST
URL: {{ $env.DISCORD_WEBHOOK_URL }}

Headers:
  Content-Type: application/json

Body:
{
  "content": "{{ $json.finalMessage }}",
  "username": "Customer Support AI",
  "avatar_url": "https://example.com/bot-avatar.png"
}
```

---

## 🚨 ขั้นตอนที่ 4: Human Escalation System

### 4.1 Node 9: IF (Check Escalation)

**Step 1:** เชื่อมจาก Node 6 (Process AI Response) → IF

**Step 2:** ตั้งค่า IF:
```
Conditions:
- Value 1: {{ $json.needsEscalation }}
- Operation: Equal
- Value 2: true
```

### 4.2 Node 10: HTTP Request (Notify Human Agents)

**Step 1:** เชื่อมจาก "True" output

**Step 2:** ส่งแจ้งเตือนไปยัง Discord channel ของทีม:
```
Request Method: POST
URL: {{ $env.TEAM_DISCORD_WEBHOOK_URL }}

Headers:
  Content-Type: application/json

Body:
{
  "content": "@here 🚨 **Customer Escalation Required**",
  "embeds": [
    {
      "title": "Customer Support Escalation",
      "color": 15158332,
      "fields": [
        {
          "name": "👤 Customer ID",
          "value": "{{ $json.userId }}",
          "inline": true
        },
        {
          "name": "📱 Platform", 
          "value": "{{ $json.platform }}",
          "inline": true
        },
        {
          "name": "⚠️ Priority",
          "value": "{{ $json.priority }}",
          "inline": true
        },
        {
          "name": "❓ Customer Question",
          "value": "{{ $json.messageText }}",
          "inline": false
        },
        {
          "name": "🤖 AI Response",
          "value": "{{ $json.aiAnswer }}",
          "inline": false
        },
        {
          "name": "📊 Analysis",
          "value": "Urgent: {{ $json.isUrgent }}\nNegative: {{ $json.isNegative }}\nComplex: {{ $json.isComplex }}\nConfidence: {{ $json.confidence }}",
          "inline": false
        }
      ],
      "footer": {
        "text": "Customer Support AI • Escalation System"
      },
      "timestamp": "{{ $json.responseTimestamp }}"
    }
  ]
}
```

### 4.3 Node 11: Google Sheets (Log Escalation)

**Step 1:** เชื่อม Node 10 → Google Sheets

**Step 2:** สร้าง Sheet ใหม่ชื่อ "Escalations" และตั้งค่า:
```
Operation: Append
Document: Customer Support Knowledge
Sheet: Escalations
Range: A:J

Values:
- {{ $json.responseTimestamp }}
- {{ $json.userId }}
- {{ $json.platform }}
- {{ $json.priority }}
- {{ $json.messageText }}
- {{ $json.aiAnswer }}
- {{ $json.confidence }}
- {{ $json.isUrgent }}
- {{ $json.isNegative }}
- {{ $json.isComplex }}
```

---

## 📊 ขั้นตอนที่ 5: Conversation Logging

### 5.1 Node 12: Google Sheets (Log All Conversations)

**Step 1:** เชื่อมจาก Node 6 (Process AI Response) → Google Sheets

**Step 2:** ตั้งค่าเพื่อบันทึกทุก conversation:
```
Operation: Append
Document: Customer Support Knowledge
Sheet: Conversations
Range: A:L

Values:
- {{ $json.timestamp }}
- {{ $json.userId }}
- {{ $json.platform }}
- {{ $json.messageText }}
- {{ $json.aiAnswer }}
- {{ $json.confidence }}
- {{ $json.priority }}
- {{ $json.needsEscalation }}
- {{ $json.isUrgent }}
- {{ $json.isNegative }}
- {{ $json.isComplex }}
- {{ $json.responseTimestamp }}
```

---

## 📈 ขั้นตอนที่ 6: Analytics และ Reporting

### 6.1 สร้าง Daily Analytics Workflow

**Step 1:** สร้าง workflow ใหม่ชื่อ "Customer Support Analytics"

**Step 2:** เพิ่ม Schedule Trigger:
```
Cron Expression: 0 23 * * *  // ทุกวันเวลา 23:00
```

### 6.2 Node 1: Google Sheets (Read Daily Data)

```
Operation: Read
Document: Customer Support Knowledge
Sheet: Conversations
Range: A:L
```

### 6.3 Node 2: Code (Calculate Daily Metrics)

```javascript
// อ่านข้อมูลจาก Google Sheets
const conversationData = $input.all()[0].json;

if (!conversationData || conversationData.length <= 1) {
  return [{ json: { message: "No conversations found for today" } }];
}

// ข้ามหัวตาราง
const conversations = conversationData.slice(1);

// กรองข้อมูลวันนี้
const today = new Date().toISOString().split('T')[0];
const todayConversations = conversations.filter(row => {
  const rowDate = new Date(row[0]).toISOString().split('T')[0];
  return rowDate === today;
});

if (todayConversations.length === 0) {
  return [{ json: { message: "No conversations today" } }];
}

// คำนวณ metrics
const totalConversations = todayConversations.length;
let escalations = 0;
let highPriority = 0;
let avgConfidence = 0;
let platformBreakdown = {};
let hourlyBreakdown = {};

todayConversations.forEach(row => {
  const [timestamp, userId, platform, question, answer, confidence, priority, needsEscalation] = row;
  
  // Count escalations
  if (needsEscalation === 'true' || needsEscalation === true) {
    escalations++;
  }
  
  // Count high priority
  if (priority === 'high') {
    highPriority++;
  }
  
  // Average confidence
  avgConfidence += parseFloat(confidence) || 0;
  
  // Platform breakdown
  platformBreakdown[platform] = (platformBreakdown[platform] || 0) + 1;
  
  // Hourly breakdown
  const hour = new Date(timestamp).getHours();
  hourlyBreakdown[hour] = (hourlyBreakdown[hour] || 0) + 1;
});

avgConfidence = avgConfidence / totalConversations;

// สร้างรายงาน
const report = {
  date: today,
  totalConversations,
  escalations,
  escalationRate: Math.round((escalations / totalConversations) * 100),
  highPriority,
  avgConfidence: Math.round(avgConfidence * 100) / 100,
  aiSuccessRate: Math.round(((totalConversations - escalations) / totalConversations) * 100),
  platformBreakdown,
  peakHour: Object.entries(hourlyBreakdown).sort(([,a], [,b]) => b - a)[0]?.[0] || 'N/A'
};

// สร้างข้อความรายงาน
const reportMessage = {
  embeds: [{
    title: "📊 Daily Customer Support Report",
    description: `Report for ${new Date().toLocaleDateString('th-TH')}`,
    color: 3447003,
    fields: [
      {
        name: "💬 Total Conversations",
        value: totalConversations.toString(),
        inline: true
      },
      {
        name: "🤖 AI Success Rate",
        value: `${report.aiSuccessRate}%`,
        inline: true
      },
      {
        name: "🚨 Escalations",
        value: `${escalations} (${report.escalationRate}%)`,
        inline: true
      },
      {
        name: "⚡ High Priority Cases",
        value: highPriority.toString(),
        inline: true
      },
      {
        name: "🎯 Avg AI Confidence",
        value: `${(avgConfidence * 100).toFixed(1)}%`,
        inline: true
      },
      {
        name: "📈 Peak Hour",
        value: `${report.peakHour}:00`,
        inline: true
      },
      {
        name: "📱 Platform Usage",
        value: Object.entries(platformBreakdown)
          .map(([platform, count]) => `${platform}: ${count}`)
          .join('\n') || 'N/A',
        inline: false
      }
    ],
    footer: {
      text: "Customer Support Analytics • Daily Report"
    },
    timestamp: new Date().toISOString()
  }]
};

return [{ 
  json: { 
    report, 
    reportMessage,
    rawData: todayConversations 
  } 
}];
```

### 6.4 Node 3: HTTP Request (Send Daily Report)

```
Request Method: POST
URL: {{ $env.TEAM_DISCORD_WEBHOOK_URL }}

Headers:
  Content-Type: application/json

Body:
{{ JSON.stringify($json.reportMessage) }}
```

---

## 🧪 ขั้นตอนที่ 7: ทดสอบระบบ

### 7.1 เตรียม Environment Variables

เพิ่มใน n8n Settings → Environment Variables:
```
OPENAI_API_KEY=sk-your-openai-key
LINE_CHANNEL_ACCESS_TOKEN=your-line-access-token
LINE_CHANNEL_SECRET=your-line-channel-secret
DISCORD_WEBHOOK_URL=your-discord-webhook-url
TEAM_DISCORD_WEBHOOK_URL=your-team-discord-webhook-url
```

### 7.2 ตั้งค่า Line Bot Webhook

1. ไปที่ Line Developers Console
2. เลือก Channel ที่สร้างไว้
3. ไปที่ "Messaging API" tab
4. ใส่ Webhook URL จาก n8n
5. เปิดใช้งาน "Use webhook"
6. ปิด "Auto-reply messages"

### 7.3 ทดสอบ Workflow

**Test Case 1: คำถามธรรมดา**
- ส่งข้อความ: "What are your business hours?"
- ควรได้รับ: คำตอบจาก AI โดยไม่ escalate

**Test Case 2: คำถามเร่งด่วน**
- ส่งข้อความ: "URGENT: My order is missing!"
- ควรได้รับ: คำตอบ + escalation alert ในทีม

**Test Case 3: คำถามซับซ้อน**
- ส่งข้อความ: "Can you explain step by step how to return a damaged product and get a refund?"
- ควรได้รับ: คำตอบ + escalation

**Test Case 4: คำถามที่ไม่มีใน Knowledge Base**
- ส่งข้อความ: "Do you sell unicorns?"
- ควรได้รับ: ข้อความส่งต่อ human agent

### 7.4 ตรวจสอบ Logging

1. เช็ค Google Sheets "Conversations" tab
2. ตรวจสอบข้อมูลถูกบันทึกครบถ้วน
3. เช็ค "Escalations" tab สำหรับกรณี escalate

---

## 🎨 ขั้นตอนที่ 8: ปรับแต่งและเพิ่มฟีเจอร์

### 8.1 เพิ่ม Multi-Language Support

**แก้ไข System Message ใน OpenAI Node:**
```
ตรวจจับภาษาที่ลูกค้าใช้และตอบด้วยภาษาเดียวกัน:
- ภาษาไทย: ใช้คำสุภาพ เช่น ครับ/ค่ะ
- English: Use professional but friendly tone
- อื่นๆ: ตอบเป็นภาษาอังกฤษ

หากลูกค้าถามเป็นภาษาต่างประเทศ ให้แนะนำว่ามี support เป็นภาษาไทยและอังกฤษ
```

### 8.2 เพิ่ม Sentiment Analysis

**เพิ่ม HTTP Request Node หลัง Parse Message:**
```
URL: https://api-inference.huggingface.co/models/cardiffnlp/twitter-roberta-base-sentiment-latest
Method: POST
Headers:
  Authorization: Bearer {{ $env.HUGGINGFACE_TOKEN }}
Body:
{
  "inputs": "{{ $json.messageText }}"
}
```

### 8.3 เพิ่ม Quick Reply Buttons (Line)

**แก้ไข Line Reply Message:**
```json
{
  "replyToken": "{{ $json.replyToken }}",
  "messages": [
    {
      "type": "text",
      "text": "{{ $json.finalMessage }}",
      "quickReply": {
        "items": [
          {
            "type": "action",
            "action": {
              "type": "message",
              "label": "ติดต่อเจ้าหน้าที่",
              "text": "ขอติดต่อเจ้าหน้าที่"
            }
          },
          {
            "type": "action", 
            "action": {
              "type": "message",
              "label": "ข้อมูลติดต่อ",
              "text": "ข้อมูลการติดต่อ"
            }
          },
          {
            "type": "action",
            "action": {
              "type": "message", 
              "label": "FAQ อื่นๆ",
              "text": "คำถามที่พบบ่อย"
            }
          }
        ]
      }
    }
  ]
}
```

### 8.4 เพิ่ม Customer Satisfaction Survey

**สร้าง Workflow ใหม่สำหรับ Survey:**

**Node 1: Schedule Trigger**
```
Cron: 0 9 * * *  // ทุกเช้า 9:00
```

**Node 2: Google Sheets (Read Yesterday's Conversations)**

**Node 3: Code (Generate Survey)**
```javascript
// เลือกลูกค้าที่ chat เมื่อวาน (ไม่ escalate)
const conversations = yesterdayData.filter(row => 
  row[7] === 'false' && // ไม่ escalate
  Math.random() < 0.3   // สุ่ม 30%
);

// สร้าง survey message
const surveyMessage = `
🙏 สวัสดีค่ะ ขอบคุณที่ใช้บริการ Customer Support ของเรา

กรุณาให้คะแนนความพอใจ:
⭐⭐⭐⭐⭐ ดีมาก (5)
⭐⭐⭐⭐ ดี (4)  
⭐⭐⭐ ปานกลาง (3)
⭐⭐ ควรปรับปรุง (2)
⭐ ไม่พอใจ (1)

กรุณาส่งหมายเลขคะแนนกลับมาค่ะ
`;

return conversations.map(conv => ({
  userId: conv[1],
  platform: conv[2],
  surveyMessage
}));
```

---

## 🔍 ขั้นตอนที่ 9: Monitoring และ Optimization

### 9.1 สร้าง Health Check Workflow

**Node 1: Manual Trigger** (รันทุก 30 นาที)

**Node 2: Code (Health Check)**
```javascript
// ตรวจสอบ API health
const healthChecks = {
  openai: false,
  line: false,
  discord: false,
  sheets: false
};

// Test OpenAI
try {
  const openaiTest = await fetch('https://api.openai.com/v1/models', {
    headers: { 'Authorization': `Bearer ${process.env.OPENAI_API_KEY}` }
  });
  healthChecks.openai = openaiTest.status === 200;
} catch (e) {
  console.log('OpenAI health check failed:', e.message);
}

// สร้างรายงาน health status
const healthReport = {
  timestamp: new Date().toISOString(),
  status: Object.values(healthChecks).every(Boolean) ? 'healthy' : 'degraded',
  services: healthChecks
};

return [{ json: healthReport }];
```

### 9.2 Performance Monitoring

**เพิ่ม Code ใน Main Workflow เพื่อวัด response time:**
```javascript
// เพิ่มที่จุดเริ่มต้น
const startTime = Date.now();

// เพิ่มที่จุดสิ้นสุด
const responseTime = Date.now() - startTime;
console.log(`Response time: ${responseTime}ms`);

// บันทึกลง performance sheet
return {
  ...existingData,
  responseTime: responseTime,
  timestamp: new Date().toISOString()
};
```

---

## 🎉 สรุป

หลังจากทำ Hands-on นี้เสร็จ คุณจะได้:

✅ **AI Customer Support Bot ที่ครบครัน**  
✅ **ระบบ Escalation อัตโนมัติ**  
✅ **Knowledge Base ที่ค้นหาได้**  
✅ **Analytics และ Reporting**  
✅ **Multi-platform Support (Line, Discord)**  
✅ **Conversation Logging**  
✅ **Performance Monitoring**

### Key Features ที่ได้
- 🤖 AI ตอบคำถามโดยใช้ Knowledge Base
- 🚨 Auto-escalation สำหรับกรณีซับซ้อน  
- 📊 Real-time analytics และรายงาน
- 💬 Multi-platform messaging
- 📈 Customer satisfaction tracking
- 🔍 Advanced search ใน Knowledge Base

### Next Steps
1. เพิ่ม Voice message support
2. สร้าง Mobile app integration  
3. เพิ่ม Video call escalation
4. สร้าง Chatbot training interface
5. เพิ่ม Multi-language AI models

**🚀 พร้อมสำหรับ AI Content Creator ใน Hands-on ถัดไป!**
