# Hands-on 2: ระบบติดตาม Social Media 📱

**ระดับ:** กลาง  
**เวลาที่ใช้:** 60-90 นาที  
**สิ่งที่จะได้:** ระบบติดตาม mentions และแจ้งเตือนแบบ real-time

---

## 🎯 เป้าหมาย

สร้างระบบที่:
1. ติดตาม mentions ใน Twitter/X ทุก 15 นาที
2. ส่งแจ้งเตือนไป Discord channel
3. วิเคราะห์ sentiment ของ mentions
4. บันทึก analytics ลง Google Sheets
5. ส่ง weekly report ของการมีส่วนร่วม

---

## 📋 เตรียมความพร้อม

### 1. API Keys ที่ต้องมี
- ✅ Twitter API v2 (Essential ฟรี)
- ✅ Discord Webhook URL (ฟรี)
- ✅ Google Sheets API (ฟรี)
- ✅ Hugging Face API (ฟรี - สำหรับ Sentiment Analysis)

### 2. Accounts ที่ต้องมี
- ✅ Twitter Developer Account
- ✅ Discord Server (admin)
- ✅ Google Account
- ✅ Hugging Face Account

---

## 🔑 ขั้นตอนที่ 1: เตรียม API Keys

### 1.1 สร้าง Twitter API v2

**Step 1:** สมัคร Twitter Developer Account
1. ไปที่ https://developer.twitter.com/
2. คลิก "Apply for a developer account"
3. เลือก "Hobbyist" → "Making a bot"
4. กรอกข้อมูลตามความจริง
5. รอการอนุมัติ (1-3 วัน)

**Step 2:** สร้าง App
1. ไปที่ Developer Portal
2. คลิก "Create App"
3. ชื่อ App: "n8n-social-monitor"
4. เลือก Environment: "Development"

**Step 3:** ดึง API Keys
```
API Key: your_api_key
API Secret: your_api_secret
Bearer Token: your_bearer_token
```

**Step 4:** ทดสอบ API
```bash
curl "https://api.twitter.com/2/tweets/search/recent?query=from:twitterdev" \
-H "Authorization: Bearer YOUR_BEARER_TOKEN"
```

### 1.2 สร้าง Discord Webhook

**Step 1:** สร้าง Discord Server (ถ้ายังไม่มี)
1. เปิด Discord
2. คลิก "+" → "Create a Server"
3. ตั้งชื่อ: "n8n Notifications"

**Step 2:** สร้าง Channel
1. คลิกขวาที่ server name
2. "Create Channel" → Text Channel
3. ชื่อ: "social-mentions"

**Step 3:** สร้าง Webhook
1. คลิกขวาที่ channel "social-mentions"
2. "Edit Channel" → "Integrations" → "Webhooks"
3. "Create Webhook"
4. ชื่อ: "Social Monitor Bot"
5. คัดลอก Webhook URL

**Step 4:** ทดสอบ Webhook
```bash
curl -X POST "YOUR_WEBHOOK_URL" \
-H "Content-Type: application/json" \
-d '{"content": "ทดสอบการส่งข้อความ 🤖"}'
```

### 1.3 เตรียม Hugging Face API

**Step 1:** สมัคร Hugging Face
1. ไปที่ https://huggingface.co/
2. Sign up ด้วย email
3. ยืนยันอีเมล

**Step 2:** สร้าง API Token
1. ไปที่ Settings → Access Tokens
2. "New token" → "Read"
3. ชื่อ: "n8n-sentiment"
4. คัดลอก token

**Step 3:** ทดสอบ Sentiment API
```bash
curl -X POST "https://api-inference.huggingface.co/models/cardiffnlp/twitter-roberta-base-sentiment-latest" \
-H "Authorization: Bearer YOUR_HF_TOKEN" \
-H "Content-Type: application/json" \
-d '{"inputs": "I love this product!"}'
```

### 1.4 เตรียม Google Sheets

**Step 1:** สร้าง Sheet ใหม่
1. ไปที่ https://sheets.google.com
2. สร้าง Sheet ชื่อ "Social Media Analytics"
3. สร้าง 2 tabs:
   - "Mentions" - เก็บ mentions ทั้งหมด
   - "Analytics" - เก็บสถิติ

**Step 2:** ตั้ง Headers สำหรับ "Mentions" tab
```
A1: Timestamp
B1: Platform
C1: Author
D1: Content
E1: Sentiment
F1: Sentiment Score
G1: Engagement
H1: URL
I1: Processed
```

**Step 3:** ตั้ง Headers สำหรับ "Analytics" tab
```
A1: Date
B1: Total Mentions
C1: Positive
D1: Negative
E1: Neutral
F1: Avg Sentiment
G1: Top Keywords
```

---

## 🔧 ขั้นตอนที่ 2: สร้าง Main Workflow

### 2.1 เปิด n8n และสร้าง Workflow

1. เปิด browser ไปที่ `http://localhost:5678`
2. สร้าง workflow ใหม่
3. บันทึกด้วยชื่อ "Social Media Monitor"

### 2.2 Node 1: Schedule Trigger

**Step 1:** ลาก "Schedule Trigger"
```
Settings:
- Trigger Interval: Every 15 minutes
- Cron Expression: */15 * * * *
```

**อธิบาย:** ระบบจะเช็ค mentions ทุก 15 นาที เพื่อไม่ให้เกิน rate limit

### 2.3 Node 2: HTTP Request (Twitter Search)

**Step 1:** เชื่อม Schedule → HTTP Request

**Step 2:** ตั้งค่า HTTP Request:
```
Authentication: Generic Credential Type
- Credential Type: HTTP Header Auth
- Name: Authorization
- Value: Bearer {{ $env.TWITTER_BEARER_TOKEN }}

Request Method: GET
URL: https://api.twitter.com/2/tweets/search/recent

Query Parameters:
  query: @yourusername OR #yourbrand OR "your company name"
  tweet.fields: created_at,author_id,public_metrics,context_annotations
  user.fields: username,name,profile_image_url
  expansions: author_id
  max_results: 100
```

**Step 3:** เพิ่ม Environment Variables:
```
TWITTER_BEARER_TOKEN = your_bearer_token
DISCORD_WEBHOOK_URL = your_discord_webhook_url
HUGGINGFACE_TOKEN = your_hf_token
```

### 2.4 Node 3: Code (Process Twitter Data)

**Step 1:** เชื่อม HTTP Request → Code

**Step 2:** ใส่ JavaScript Code:
```javascript
// รับข้อมูลจาก Twitter API
const twitterResponse = $input.all()[0].json;

// ตรวจสอบว่ามีข้อมูลหรือไม่
if (!twitterResponse.data || twitterResponse.data.length === 0) {
  return [{ json: { message: "No new mentions found", tweets: [] } }];
}

const tweets = twitterResponse.data;
const users = twitterResponse.includes?.users || [];

// สร้าง user mapping
const userMap = {};
users.forEach(user => {
  userMap[user.id] = user;
});

// ประมวลผลแต่ละ tweet
const processedTweets = tweets.map(tweet => {
  const author = userMap[tweet.author_id] || {};
  const metrics = tweet.public_metrics || {};
  
  // คำนวณ engagement rate
  const engagement = metrics.like_count + metrics.retweet_count + metrics.reply_count + metrics.quote_count;
  
  // ตรวจสอบว่าเป็น mention ใหม่หรือไม่ (ภายใน 30 นาที)
  const tweetTime = new Date(tweet.created_at);
  const thirtyMinutesAgo = new Date(Date.now() - 30 * 60 * 1000);
  const isRecent = tweetTime > thirtyMinutesAgo;
  
  return {
    id: tweet.id,
    text: tweet.text,
    created_at: tweet.created_at,
    author_id: tweet.author_id,
    author_username: author.username || 'unknown',
    author_name: author.name || 'Unknown User',
    author_profile_image: author.profile_image_url || '',
    engagement: engagement,
    likes: metrics.like_count || 0,
    retweets: metrics.retweet_count || 0,
    replies: metrics.reply_count || 0,
    quotes: metrics.quote_count || 0,
    url: `https://twitter.com/${author.username}/status/${tweet.id}`,
    isRecent: isRecent,
    platform: 'Twitter'
  };
});

// กรองเฉพาะ mentions ใหม่
const newMentions = processedTweets.filter(tweet => tweet.isRecent);

console.log(`Found ${tweets.length} total tweets, ${newMentions.length} new mentions`);

return [{
  json: {
    totalTweets: tweets.length,
    newMentions: newMentions.length,
    tweets: newMentions
  }
}];
```

### 2.5 Node 4: IF (Check if has new mentions)

**Step 1:** เชื่อม Code → IF

**Step 2:** ตั้งค่า IF:
```
Conditions:
- Value 1: {{ $json.newMentions }}
- Operation: Larger
- Value 2: 0
```

### 2.6 Node 5: Split in Batches (แยกประมวลผลทีละ tweet)

**Step 1:** เชื่อมจาก "True" output ของ IF

**Step 2:** ตั้งค่า Split in Batches:
```
Input Data: {{ $json.tweets }}
Batch Size: 1
```

---

## 🤖 ขั้นตอนที่ 3: เพิ่ม AI Sentiment Analysis

### 3.1 Node 6: HTTP Request (Sentiment Analysis)

**Step 1:** เชื่อม Split in Batches → HTTP Request

**Step 2:** ตั้งค่า HTTP Request สำหรับ Hugging Face:
```
Authentication: Generic Credential Type
- Credential Type: HTTP Header Auth  
- Name: Authorization
- Value: Bearer {{ $env.HUGGINGFACE_TOKEN }}

Request Method: POST
URL: https://api-inference.huggingface.co/models/cardiffnlp/twitter-roberta-base-sentiment-latest

Headers:
  Content-Type: application/json

Body:
{
  "inputs": "{{ $json.text }}"
}
```

### 3.2 Node 7: Code (Process Sentiment)

**Step 1:** เชื่อม HTTP Request → Code

**Step 2:** ใส่ Code:
```javascript
// รับข้อมูล sentiment จาก Hugging Face
const sentimentResponse = $input.all()[0].json;
const tweetData = $input.all()[0].json;

// ประมวลผล sentiment
let sentiment = 'neutral';
let sentimentScore = 0;

if (sentimentResponse && sentimentResponse[0]) {
  const results = sentimentResponse[0];
  
  // หา sentiment ที่มี score สูงสุด
  const topSentiment = results.reduce((prev, current) => 
    (prev.score > current.score) ? prev : current
  );
  
  // แปลง label เป็นภาษาไทย
  const sentimentMap = {
    'LABEL_0': 'negative',    // เชิงลบ
    'LABEL_1': 'neutral',     // เป็นกลาง
    'LABEL_2': 'positive',    // เชิงบวก
    'NEGATIVE': 'negative',
    'NEUTRAL': 'neutral', 
    'POSITIVE': 'positive'
  };
  
  sentiment = sentimentMap[topSentiment.label] || 'neutral';
  sentimentScore = Math.round(topSentiment.score * 100) / 100;
}

// สร้างข้อมูลสำหรับ Discord
const urgencyLevel = sentiment === 'negative' ? '🚨' : 
                    sentiment === 'positive' ? '✅' : '📝';

const discordMessage = {
  embeds: [{
    title: `${urgencyLevel} New ${sentiment.toUpperCase()} Mention`,
    description: tweetData.text,
    color: sentiment === 'positive' ? 3066993 : 
           sentiment === 'negative' ? 15158332 : 9807270,
    fields: [
      {
        name: "👤 Author",
        value: `[@${tweetData.author_username}](https://twitter.com/${tweetData.author_username})`,
        inline: true
      },
      {
        name: "📊 Engagement", 
        value: `${tweetData.engagement} (👍${tweetData.likes} 🔄${tweetData.retweets})`,
        inline: true
      },
      {
        name: "😊 Sentiment",
        value: `${sentiment} (${(sentimentScore * 100).toFixed(1)}%)`,
        inline: true
      }
    ],
    url: tweetData.url,
    timestamp: tweetData.created_at,
    footer: {
      text: "Social Media Monitor",
      icon_url: "https://abs.twimg.com/icons/apple-touch-icon-192x192.png"
    }
  }]
};

// เตรียมข้อมูลสำหรับ Google Sheets
const sheetData = {
  timestamp: new Date().toISOString(),
  platform: tweetData.platform,
  author: `${tweetData.author_name} (@${tweetData.author_username})`,
  content: tweetData.text.replace(/"/g, '""'), // escape quotes
  sentiment: sentiment,
  sentimentScore: sentimentScore,
  engagement: tweetData.engagement,
  url: tweetData.url,
  processed: new Date().toLocaleString('th-TH')
};

return {
  discordMessage: discordMessage,
  sheetData: sheetData,
  originalTweet: tweetData,
  sentiment: sentiment,
  sentimentScore: sentimentScore
};
```

### 3.3 Node 8: HTTP Request (Send to Discord)

**Step 1:** เชื่อม Code → HTTP Request

**Step 2:** ตั้งค่า Discord Webhook:
```
Request Method: POST
URL: {{ $env.DISCORD_WEBHOOK_URL }}

Headers:
  Content-Type: application/json

Body:
{{ JSON.stringify($json.discordMessage) }}
```

### 3.4 Node 9: Google Sheets (Save Data)

**Step 1:** เชื่อม Code → Google Sheets (ใช้ตัวเดียวกับ Discord)

**Step 2:** ตั้งค่า Google Sheets:
```
Authentication: Service Account (ใช้จาก hands-on 1)

Operation: Append
Document: Social Media Analytics
Sheet: Mentions
Range: A:I

Values:
- {{ $json.sheetData.timestamp }}
- {{ $json.sheetData.platform }}
- {{ $json.sheetData.author }}
- {{ $json.sheetData.content }}
- {{ $json.sheetData.sentiment }}
- {{ $json.sheetData.sentimentScore }}
- {{ $json.sheetData.engagement }}
- {{ $json.sheetData.url }}
- {{ $json.sheetData.processed }}
```

---

## 📊 ขั้นตอนที่ 4: สร้าง Analytics Workflow

### 4.1 สร้าง Workflow ใหม่สำหรับ Analytics

**Step 1:** สร้าง workflow ใหม่ชื่อ "Social Analytics"

### 4.2 Node 1: Schedule Trigger (Daily Summary)

```
Cron Expression: 0 23 * * *  // ทุกวันเวลา 23:00
```

### 4.3 Node 2: Google Sheets (Read Today's Data)

```
Operation: Read
Document: Social Media Analytics
Sheet: Mentions
Range: A:I
```

### 4.4 Node 3: Code (Calculate Analytics)

```javascript
// อ่านข้อมูลจาก Google Sheets
const sheetData = $input.all()[0].json;

if (!sheetData || sheetData.length <= 1) {
  return [{ json: { message: "No data found for today" } }];
}

// ข้ามหัวตาราง
const rows = sheetData.slice(1);

// กรองข้อมูลวันนี้
const today = new Date().toISOString().split('T')[0];
const todayData = rows.filter(row => {
  const rowDate = new Date(row[0]).toISOString().split('T')[0];
  return rowDate === today;
});

if (todayData.length === 0) {
  return [{ json: { message: "No mentions today" } }];
}

// คำนวณสถิติ
const totalMentions = todayData.length;
const sentimentCounts = {
  positive: 0,
  negative: 0,
  neutral: 0
};

let totalEngagement = 0;
let sentimentScoreSum = 0;
const keywords = [];

todayData.forEach(row => {
  const sentiment = row[4]?.toLowerCase() || 'neutral';
  const engagement = parseInt(row[6]) || 0;
  const sentimentScore = parseFloat(row[5]) || 0;
  const content = row[3] || '';
  
  sentimentCounts[sentiment] = (sentimentCounts[sentiment] || 0) + 1;
  totalEngagement += engagement;
  sentimentScoreSum += sentimentScore;
  
  // แยกคำสำคัญ (ง่ายๆ)
  const words = content.toLowerCase()
    .replace(/[^\w\s]/g, '')
    .split(' ')
    .filter(word => word.length > 3);
  keywords.push(...words);
});

// หาคำที่ใช้บ่อยที่สุด
const wordCount = {};
keywords.forEach(word => {
  wordCount[word] = (wordCount[word] || 0) + 1;
});

const topKeywords = Object.entries(wordCount)
  .sort(([,a], [,b]) => b - a)
  .slice(0, 5)
  .map(([word, count]) => `${word}(${count})`)
  .join(', ');

const avgSentiment = totalMentions > 0 ? sentimentScoreSum / totalMentions : 0;
const avgEngagement = totalMentions > 0 ? totalEngagement / totalMentions : 0;

// สร้างรายงาน
const report = {
  date: today,
  totalMentions: totalMentions,
  positive: sentimentCounts.positive || 0,
  negative: sentimentCounts.negative || 0,
  neutral: sentimentCounts.neutral || 0,
  avgSentiment: Math.round(avgSentiment * 100) / 100,
  avgEngagement: Math.round(avgEngagement * 100) / 100,
  topKeywords: topKeywords || 'N/A'
};

// สร้างข้อความสำหรับ Discord
const positivePercent = Math.round((sentimentCounts.positive / totalMentions) * 100);
const negativePercent = Math.round((sentimentCounts.negative / totalMentions) * 100);

const dailyReportMessage = {
  embeds: [{
    title: "📊 Daily Social Media Report",
    description: `Summary for ${new Date().toLocaleDateString('th-TH')}`,
    color: 3447003,
    fields: [
      {
        name: "📈 Total Mentions",
        value: totalMentions.toString(),
        inline: true
      },
      {
        name: "😊 Sentiment Breakdown",
        value: `✅ ${sentimentCounts.positive} (${positivePercent}%)\n❌ ${sentimentCounts.negative} (${negativePercent}%)\n📝 ${sentimentCounts.neutral}`,
        inline: true
      },
      {
        name: "🔥 Avg Engagement",
        value: avgEngagement.toFixed(1),
        inline: true
      },
      {
        name: "🔍 Top Keywords",
        value: topKeywords || 'No keywords found',
        inline: false
      }
    ],
    footer: {
      text: "Generated by n8n Social Monitor",
      icon_url: "https://n8n.io/favicon.ico"
    },
    timestamp: new Date().toISOString()
  }]
};

return {
  report: report,
  dailyReportMessage: dailyReportMessage,
  rawData: todayData
};
```

### 4.5 Node 4: Google Sheets (Save Analytics)

```
Operation: Append
Document: Social Media Analytics  
Sheet: Analytics
Range: A:G

Values:
- {{ $json.report.date }}
- {{ $json.report.totalMentions }}
- {{ $json.report.positive }}
- {{ $json.report.negative }}
- {{ $json.report.neutral }}
- {{ $json.report.avgSentiment }}
- {{ $json.report.topKeywords }}
```

### 4.6 Node 5: HTTP Request (Send Daily Report)

```
Request Method: POST
URL: {{ $env.DISCORD_WEBHOOK_URL }}

Headers:
  Content-Type: application/json

Body:
{{ JSON.stringify($json.dailyReportMessage) }}
```

---

## 🚨 ขั้นตอนที่ 5: Alert System สำหรับ Negative Mentions

### 5.1 เพิ่ม IF Node ใน Main Workflow

**Step 1:** เชื่อมจาก Node 7 (Process Sentiment) → IF

**Step 2:** ตั้งค่า IF:
```
Conditions:
- Value 1: {{ $json.sentiment }}
- Operation: Equal
- Value 2: negative
```

### 5.2 Node: HTTP Request (Urgent Alert)

**Step 1:** เชื่อมจาก "True" output

**Step 2:** ส่ง Urgent notification:
```javascript
// สร้างข้อความเตือนพิเศษ
const urgentAlert = {
  content: "@here 🚨 **URGENT: Negative Mention Detected!**",
  embeds: [{
    title: "⚠️ Immediate Attention Required",
    description: $json.originalTweet.text,
    color: 15158332, // สีแดง
    fields: [
      {
        name: "👤 Author",
        value: `[@${$json.originalTweet.author_username}](https://twitter.com/${$json.originalTweet.author_username})`,
        inline: true
      },
      {
        name: "📊 Engagement Risk",
        value: `${$json.originalTweet.engagement} interactions`,
        inline: true
      },
      {
        name: "😟 Sentiment Score", 
        value: `${($json.sentimentScore * 100).toFixed(1)}% negative`,
        inline: true
      },
      {
        name: "🔗 Take Action",
        value: `[View Tweet](${$json.originalTweet.url})`,
        inline: false
      }
    ],
    footer: {
      text: "Respond quickly to maintain brand reputation"
    },
    timestamp: new Date().toISOString()
  }]
};

return { urgentAlert: urgentAlert };
```

---

## 🧪 ขั้นตอนที่ 6: ทดสอบระบบ

### 6.1 ทดสอบ Main Workflow

1. **Schedule Trigger**: เปลี่ยนเป็น Manual Trigger ก่อน
2. **Twitter API**: ตรวจสอบได้ข้อมูล mentions
3. **Sentiment Analysis**: ตรวจสอบการวิเคราะห์ความรู้สึก
4. **Discord**: ตรวจสอบได้รับข้อความ
5. **Google Sheets**: ตรวจสอบข้อมูลถูกบันทึก

### 6.2 ทดสอบ Analytics Workflow

1. สร้างข้อมูลตัวอย่างใน Google Sheets
2. รัน Analytics workflow
3. ตรวจสอบรายงานใน Discord

### 6.3 ทดสอบ Alert System

1. หา mention ที่เป็น negative sentiment
2. ตรวจสอบได้รับ urgent alert
3. ทดสอบ @here notification

---

## 🎨 ขั้นตอนที่ 7: ปรับแต่งและเพิ่มฟีเจอร์

### 7.1 เพิ่มการติดตาม Instagram/Facebook

**เพิ่ม HTTP Request Node:**
```javascript
// Facebook Graph API
const facebookQuery = {
  url: `https://graph.facebook.com/v18.0/search`,
  params: {
    q: 'your_brand',
    type: 'post',
    access_token: process.env.FACEBOOK_ACCESS_TOKEN
  }
};
```

### 7.2 เพิ่ม Keyword Filtering

**แก้ไข Code ใน Node 3:**
```javascript
// เพิ่มการกรอง keywords
const importantKeywords = ['urgent', 'problem', 'issue', 'complaint', 'love', 'great'];
const hasImportantKeyword = importantKeywords.some(keyword => 
  tweet.text.toLowerCase().includes(keyword)
);

return {
  // ... existing data
  isImportant: hasImportantKeyword,
  priority: hasImportantKeyword ? 'high' : 'normal'
};
```

### 7.3 เพิ่ม Auto-Response สำหรับ Positive Mentions

**เพิ่ม IF Node สำหรับ positive sentiment:**
```javascript
// ถ้า sentiment เป็น positive และ engagement สูง
if (sentiment === 'positive' && engagement > 50) {
  // ส่ง thank you message หรือ retweet อัตโนมัติ
}
```

---

## 📊 ขั้นตอนที่ 8: สร้าง Dashboard

### 8.1 สร้าง Google Data Studio Dashboard

1. ไปที่ https://datastudio.google.com
2. เชื่อมกับ Google Sheets
3. สร้างกราฟ:
   - Sentiment trends over time
   - Engagement metrics
   - Top keywords cloud
   - Response time analysis

### 8.2 เพิ่ม Real-time Charts ใน Google Sheets

```javascript
// สูตรสำหรับ real-time dashboard
=SPARKLINE(FILTER(Analytics!B:B,Analytics!A:A>=TODAY()-7))  // Mentions trend
=COUNTIFS(Mentions!A:A,">="&TODAY(),Mentions!E:E,"positive")  // Today's positive
=AVERAGE(FILTER(Mentions!F:F,Mentions!A:A>=TODAY()))  // Average sentiment today
```

---

## 🔍 ขั้นตอนที่ 9: Troubleshooting

### ปัญหาที่พบบ่อย

**1. Twitter API Rate Limit**
```
Error: 429 Too Many Requests
Solution: เพิ่มระยะเวลา interval หรือลด max_results
```

**2. Sentiment Analysis ช้า**
```
Error: 503 Service Unavailable  
Solution: เพิ่ม retry logic หรือใช้ model อื่น
```

**3. Discord Webhook ไม่ทำงาน**
```
Error: 400 Bad Request
Solution: ตรวจสอบ JSON format และ embed structure
```

### Debug Tips

1. **Monitor API Usage:**
```javascript
// เพิ่มใน Code node
console.log(`API calls today: ${apiCallCount}`);
console.log(`Rate limit remaining: ${rateLimitRemaining}`);
```

2. **Log Sentiment Accuracy:**
```javascript
// บันทึกผลลัพธ์ sentiment เพื่อปรับปรุง
console.log(`Text: "${text}"`);
console.log(`Detected sentiment: ${sentiment} (${confidence}%)`);
```

---

## 🎉 สรุป

หลังจากทำ Hands-on นี้เสร็จ คุณจะได้:

✅ **ระบบติดตาม Social Media แบบ Real-time**  
✅ **AI Sentiment Analysis**  
✅ **Alert System สำหรับ Negative Mentions**  
✅ **Analytics Dashboard อัตโนมัติ**  
✅ **Integration ระหว่าง API หลายตัว**  
✅ **Discord Bot Notifications**

### Next Steps

1. เพิ่มการติดตาม LinkedIn, YouTube
2. สร้าง Auto-response system
3. เพิ่ม Image/Video content analysis
4. สร้าง Mobile app notifications
5. เพิ่ม Competitor monitoring

**🚀 พร้อมสำหรับ AI Customer Support ใน Hands-on ถัดไป!**
