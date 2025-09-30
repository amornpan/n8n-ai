# n8n AI Automation Projects 🤖

ชุดโปรเจค n8n ที่รวม AI และ Automation เข้าด้วยกัน สำหรับสร้างระบบอัตโนมัติที่ชาญฉลาด

## 📁 โครงสร้างโปรเจค

```
n8n-ai/
├── README.md                                      # ไฟล์นี้
├── hands-on/                                      # Workshop Hands-on ทีละขั้นตอน
│   ├── 01-weather-notification.md                 # ระบบแจ้งเตือนสภาพอากาศ
│   ├── 02-social-media-monitor.md                 # ติดตาม Social Media
│   ├── 03-ai-customer-support.md                  # AI Customer Support Bot
│   ├── 04-ai-content-creator.md                   # AI สร้างเนื้อหา
│   ├── 05-ai-data-analyst.md                      # AI วิเคราะห์ข้อมูล
│   └── 06-rag-knowledge-base.md                   # RAG Knowledge Base
├── n8n-lead-management-workflow/                  # โปรเจค Lead Management
│   ├── Automating Lead Nurturing.json             # Workflow JSON
│   ├── lead_form_responses.csv                    # ข้อมูลตัวอย่าง
│   └── README.md                                  # เอกสารโปรเจค
├── N8N-Google-Services-Integration/               # การเชื่อมต่อ Google Services
│   ├── N8N Integration with Google Services.json  # Workflow JSON
│   └── README.md                                  # เอกสารโปรเจค
├── Weather Alert Workflow for Discord using n8n/  # แจ้งเตือนสภาพอากาศ Discord
│   └── Weather Alert Workflow for Discord using n8n.json
├── workshops/                                     # Workshop พิเศษ
│   └── Create Food Emoji Icons with OpenAI GPT & Image Generation.json
├── n8n-lead-management-workflow.md               # 📘 Workshop: สร้าง Lead Management System
├── n8n-workflows-examples.md                     # ตัวอย่าง Workflows
├── gmail-oauth2-setup.md                         # คู่มือตั้งค่า Gmail OAuth2
├── google-service-account-setup.md               # คู่มือตั้งค่า Google Service Account
├── slack-api-setup.md                            # คู่มือตั้งค่า Slack API
└── customer-database-100-rows.xlsx               # ข้อมูลตัวอย่าง
```

## 🚀 Quick Start

### ติดตั้ง n8n
```bash
# วิธีที่ 1: ใช้ npx (แนะนำ)
npx n8n

# วิธีที่ 2: ติดตั้งแบบ global
npm install n8n -g
n8n

# วิธีที่ 3: ใช้ Docker
docker run -it --rm --name n8n -p 5678:5678 -v ~/.n8n:/home/node/.n8n n8nio/n8n
```

### เข้าใช้งาน
1. เปิดเบราว์เซอร์ไปที่ `http://localhost:5678`
2. สร้างบัญชีผู้ใช้ใหม่
3. เริ่มสร้าง workflow แรกของคุณ!

---

## 📚 Workshops & Tutorials

### 🎯 Workshop หลัก - Lead Management System
**📘 [สร้าง Lead Management Workflow](./n8n-lead-management-workflow.md)**
- ระบบจัดการ Lead แบบครบวงจร
- รับข้อมูล Lead จากฟอร์มเว็บไซต์
- กรอง Hot Lead และ Warm Lead อัตโนมัติ
- บันทึกลง Google Sheets
- แจ้งเตือนผ่าน Slack และ Email
- ส่งอีเมลตอบกลับอัตโนมัติ
- เชื่อมต่อกับ CRM
- **ระดับ:** กลาง-สูง
- **เวลา:** 60-90 นาที

### 📖 Hands-on Tutorials

#### 🌤️ [1. Weather Notification System](./hands-on/01-weather-notification.md)
**ระดับ: เริ่มต้น | เวลา: 30 นาที**
- เช็คสภาพอากาศทุกเช้า
- ส่ง Line Notify เมื่อฝนตก
- บันทึกข้อมูลใน Google Sheets
- **APIs:** OpenWeatherMap, Line Notify, Google Sheets

#### 📱 [2. Social Media Monitor](./hands-on/02-social-media-monitor.md)
**ระดับ: กลาง | เวลา: 45 นาที**
- ติดตาม mentions ใน Twitter/X
- ส่งแจ้งเตือนใน Discord
- วิเคราะห์ engagement metrics
- **APIs:** Twitter API v2, Discord Webhook

#### 🤖 [3. AI Customer Support Bot](./hands-on/03-ai-customer-support.md)
**ระดับ: กลาง-สูง | เวลา: 60 นาที**
- รับคำถามจาก Line/Discord
- ใช้ AI ตอบคำถามอัตโนมัติ
- ส่งต่อ human agent เมื่อจำเป็น
- บันทึก conversation history
- **AI Services:** OpenAI GPT-4, Claude

#### ✍️ [4. AI Content Creator](./hands-on/04-ai-content-creator.md)
**ระดับ: กลาง-สูง | เวลา: 60 นาที**
- สร้างเนื้อหา social media อัตโนมัติ
- แปลภาษาและปรับโทนเสียง
- โพสต์ลงแพลตฟอร์มต่างๆ
- วิเคราะห์ประสิทธิภาพเนื้อหา
- **AI Services:** OpenAI GPT-4, Hugging Face

#### 📊 [5. AI Data Analyst](./hands-on/05-ai-data-analyst.md)
**ระดับ: สูง | เวลา: 90 นาที**
- วิเคราะห์ข้อมูลยอดขายอัตโนมัติ
- สร้างรายงานพร้อม insights
- เสนอแนะกลยุทธ์ทางธุรกิจ
- ส่งรายงานประจำสัปดาห์
- **AI Services:** OpenAI GPT-4, Code Interpreter

#### 🔍 [6. RAG Knowledge Base System](./hands-on/06-rag-knowledge-base.md)
**ระดับ: ขั้นสูง | เวลา: 120 นาที**
- ตอบคำถามจากเอกสารบริษัท
- ใช้ Vector Database เก็บความรู้
- ค้นหาข้อมูลแบบ semantic
- อ้างอิงแหล่งที่มาได้
- **AI Services:** OpenAI Embeddings, Pinecone, GPT-4

---

## 💼 โปรเจคตัวอย่าง

### 📁 [Lead Management Workflow](./n8n-lead-management-workflow/)
ระบบจัดการ Lead แบบครบวงจร พร้อม Webhook, Google Sheets, Slack และ Email
- **ไฟล์:** `Automating Lead Nurturing.json`
- **เอกสาร:** [README.md](./n8n-lead-management-workflow/README.md)
- **Workshop:** [n8n-lead-management-workflow.md](./n8n-lead-management-workflow.md)

### 🔗 [Google Services Integration](./N8N-Google-Services-Integration/)
การเชื่อมต่อกับ Google Services (Gmail, Sheets, Calendar, Drive)
- **ไฟล์:** `N8N Integration with Google Services.json`
- **เอกสาร:** [README.md](./N8N-Google-Services-Integration/README.md)

### 🌦️ [Weather Alert for Discord](./Weather%20Alert%20Workflow%20for%20Discord%20using%20n8n/)
ระบบแจ้งเตือนสภาพอากาศผ่าน Discord
- **ไฟล์:** `Weather Alert Workflow for Discord using n8n.json`

---

## 🛠️ การติดตั้งและ Config

### Environment Variables
สร้างไฟล์ `.env` ในโฟลเดอร์ n8n:
```env
# OpenAI
OPENAI_API_KEY=sk-your-openai-key

# Anthropic Claude
ANTHROPIC_API_KEY=your-claude-key

# Google Services
GOOGLE_SERVICE_ACCOUNT_EMAIL=your-email@project.iam.gserviceaccount.com
GOOGLE_PRIVATE_KEY="-----BEGIN PRIVATE KEY-----\n...\n-----END PRIVATE KEY-----\n"

# Line Notify
LINE_NOTIFY_TOKEN=your-line-token

# Discord
DISCORD_WEBHOOK_URL=https://discord.com/api/webhooks/...

# Slack
SLACK_BOT_TOKEN=xoxb-your-slack-token
SLACK_WEBHOOK_URL=https://hooks.slack.com/services/...

# Weather API
OPENWEATHER_API_KEY=your-weather-key

# Pinecone (สำหรับ RAG)
PINECONE_API_KEY=your-pinecone-key
PINECONE_INDEX_NAME=your-index-name
```

### การติดตั้ง Dependencies
```bash
# ติดตั้ง packages เพิ่มเติมสำหรับ AI
npm install @n8n/nodes-langchain openai anthropic pinecone-client
```

---

## 📖 เอกสารการตั้งค่า

### 🔐 Authentication & API Setup
- **[Gmail OAuth2 Setup](./gmail-oauth2-setup.md)** - ตั้งค่า Gmail OAuth2 สำหรับส่งอีเมล
- **[Google Service Account Setup](./google-service-account-setup.md)** - ตั้งค่า Service Account สำหรับ Google Services
- **[Slack API Setup](./slack-api-setup.md)** - ตั้งค่า Slack Bot และ Webhook

### 📝 เอกสารเพิ่มเติม
- **[n8n Workflows Examples](./n8n-workflows-examples.md)** - ตัวอย่าง Workflows พื้นฐาน
- **[Hands-on README](./hands-on/README.md)** - ภาพรวม Hands-on Tutorials

---

## 🎯 Use Cases

### สำหรับธุรกิจ
- **Lead Management**: จัดการ Lead อัตโนมัติ ตั้งแต่รับข้อมูลจนถึงการติดตาม
- **Customer Support**: ลดเวลาตอบคำถาม 80% ด้วย AI
- **Content Marketing**: สร้างเนื้อหา 24/7 อัตโนมัติ
- **Data Analysis**: รายงานอัตโนมัติทุกสัปดาห์พร้อม insights
- **Process Automation**: ลดงานซ้ำซ้อนและเพิ่มประสิทธิภาพ

### สำหรับ Developers
- **API Integration**: เชื่อมต่อ services ต่างๆ ได้อย่างง่ายดาย
- **Monitoring**: ติดตามระบบแบบ real-time
- **Testing**: ทดสอบ API อัตโนมัติ
- **Deployment**: สร้าง CI/CD pipelines

### สำหรับ Content Creators
- **Social Media**: โพสต์อัตโนมัติทุกแพลตฟอร์ม
- **SEO**: วิเคราะห์ keywords และ trends
- **Analytics**: ติดตามประสิทธิภาพเนื้อหา
- **Scheduling**: จัดการปฏิทินเนื้อหา

---

## 📚 External Resources

### Documentation
- [n8n Official Documentation](https://docs.n8n.io/)
- [n8n Community Forum](https://community.n8n.io/)
- [OpenAI API Documentation](https://platform.openai.com/docs)
- [Claude API Documentation](https://docs.anthropic.com/)

### Video Tutorials
- [n8n YouTube Channel](https://www.youtube.com/@n8n-io)
- [n8n Quickstart](https://www.youtube.com/watch?v=RpjQTGKm-ok)

### Community
- [n8n Discord](https://discord.gg/n8n)
- [n8n GitHub](https://github.com/n8n-io/n8n)

---

## 🔧 การใช้งาน

### การ Import Workflow
1. เปิด n8n Web Interface
2. คลิก "+" → "Import from File"
3. เลือกไฟล์ `.json` จากโฟลเดอร์โปรเจค
4. กำหนดค่า credentials ต่างๆ
5. ทดสอบ workflow

### การสร้าง Workflow ใหม่
1. ศึกษา workshop ที่สนใจ
2. ทำตาม hands-on ทีละขั้นตอน
3. ปรับแต่งตามความต้องการ
4. บันทึกเป็น workflow ใหม่

---

## 🚨 ข้อควรระวัง

### Security
- **ไม่เก็บ API keys ในโค้ด** - ใช้ Environment Variables
- **ตั้งค่า Rate Limiting** - หลีกเลี่ยง API quota exceeded
- **Validate Input** - ตรวจสอบข้อมูลก่อนส่งให้ AI
- **Monitor Usage** - ติดตามค่าใช้จ่าย API

### Performance
- **Cache Results** - เก็บผลลัพธ์ที่ใช้ซ้ำ
- **Optimize Prompts** - ทำให้ AI prompts สั้นและชัดเจน
- **Batch Processing** - ประมวลผลข้อมูลเป็นชุด
- **Error Handling** - จัดการ errors อย่างเหมาะสม

### Best Practices
- **Version Control** - ใช้ Git เก็บ workflows
- **Documentation** - เขียนคำอธิบายใน workflow
- **Testing** - ทดสอบก่อน deploy จริง
- **Monitoring** - ติดตามการทำงานของ workflow

---

## 🤝 การมีส่วนร่วม

### การส่ง Pull Request
1. Fork repository นี้
2. สร้าง branch ใหม่: `git checkout -b feature/amazing-workflow`
3. Commit การเปลี่ยนแปลง: `git commit -m 'Add amazing workflow'`
4. Push ไปยัง branch: `git push origin feature/amazing-workflow`
5. สร้าง Pull Request

### การรายงานปัญหา
- ใช้ GitHub Issues
- ระบุขั้นตอนการทำซ้ำ
- แนบ screenshots หรือ logs
- ระบุ environment (OS, n8n version, etc.)

### การแบ่งปัน Workflow
- แชร์ไฟล์ JSON workflow
- เขียนเอกสารอธิบาย
- ระบุ prerequisites และ dependencies
- ให้ตัวอย่างการใช้งาน

---

## 📈 Roadmap

### ✅ Completed (Q4 2024)
- ✅ Weather Notification System
- ✅ Lead Management Workflow
- ✅ Google Services Integration
- ✅ Basic AI Tutorials (6 workshops)

### 🚧 In Progress (Q1 2025)
- 🚧 Slack Integration Advanced
- 🚧 Mobile notification system
- 🚧 Voice AI workflows
- 🚧 Multi-language support

### 📅 Planned (Q2-Q3 2025)
- 📅 Computer Vision workflows
- 📅 Advanced RAG with multiple sources
- 📅 Custom AI models integration
- 📅 Workflow marketplace
- 📅 Performance optimization guide

---

## 📞 ติดต่อและสนับสนุน

### ช่องทางติดต่อ
- **Issues**: [GitHub Issues](https://github.com/your-repo/n8n-ai/issues)
- **Discussions**: [GitHub Discussions](https://github.com/your-repo/n8n-ai/discussions)
- **Email**: your-email@domain.com

### การสนับสนุน
หากโปรเจคนี้มีประโยชน์กับคุณ สามารถสนับสนุนได้ดังนี้:
- ⭐ ให้ Star บน GitHub
- 🐛 รายงานปัญหาที่พบ
- 💡 แนะนำ feature ใหม่
- 📝 แชร์ workflow ของคุณ
- 📖 เขียนบทความหรือสอนต่อ

---

## 📄 License

MIT License - ดูรายละเอียดใน [LICENSE](LICENSE) file

---

## 🙏 Acknowledgments

ขอขอบคุณ:
- **n8n Team** - สำหรับเครื่องมือที่ยอดเยี่ยม
- **Community Contributors** - สำหรับ workflows และความช่วยเหลือ
- **AI Service Providers** - OpenAI, Anthropic, และอื่นๆ

---

**Happy Automating! 🚀**

> สร้างด้วย ❤️ โดยชุมชน n8n และ AI enthusiasts

---

## 📊 สถิติโปรเจค

- 🎯 **Workshops**: 7+ workshops
- 📁 **Example Projects**: 5+ workflows
- 📖 **Documentation**: 10+ guides
- ⏰ **Total Learning Time**: 8+ hours
- 🎓 **Skill Levels**: เริ่มต้น → ขั้นสูง

**อัพเดทล่าสุด:** September 2025
