# n8n AI Automation Projects 🤖

ชุดโปรเจค n8n ที่รวม AI และ Automation เข้าด้วยกัน สำหรับสร้างระบบอัตโนมัติที่ชาญฉลาด

## 📁 โครงสร้างโปรเจค

```
n8n-ai/
├── README.md                          # ไฟล์นี้
├── projects/                          # โปรเจคต่างๆ
│   ├── 01-weather-notification/       # ระบบแจ้งเตือนอากาศ
│   ├── 02-social-media-monitor/       # ติดตาม Social Media
│   ├── 03-ai-customer-support/        # AI Customer Support Bot
│   ├── 04-ai-content-creator/         # AI สร้างเนื้อหา
│   ├── 05-ai-data-analyst/           # AI วิเคราะห์ข้อมูล
│   └── 06-rag-system/                # RAG Knowledge Base
├── templates/                         # Template workflows
├── docs/                             # เอกสารประกอบ
├── configs/                          # ไฟล์ config
└── assets/                           # รูปภาพ, ไฟล์ประกอบ
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

## 📊 โปรเจคในชุดนี้

### 🌤️ 1. Weather Notification System
**ระดับ: เริ่มต้น**
- เช็คสภาพอากาศทุกเช้า
- ส่ง Line Notify เมื่อฝนตก
- บันทึกข้อมูลใน Google Sheets

**APIs ที่ใช้:**
- OpenWeatherMap API
- Line Notify API
- Google Sheets API

### 📱 2. Social Media Monitor
**ระดับ: กลาง**
- ติดตาม mentions ใน Twitter/X
- ส่งแจ้งเตือนใน Discord
- วิเคราะห์ engagement metrics

**APIs ที่ใช้:**
- Twitter API v2
- Discord Webhook
- Google Sheets API

### 🤖 3. AI Customer Support Bot
**ระดับ: กลาง-สูง**
- รับคำถามจาก Line/Discord
- ใช้ AI ตอบคำถามอัตโนมัติ
- ส่งต่อ human agent เมื่อจำเป็น
- บันทึก conversation history

**AI Services:**
- OpenAI GPT-4
- Claude (Anthropic)
- ความสามารถในการเรียนรู้บริบท

### ✍️ 4. AI Content Creator
**ระดับ: กลาง-สูง**
- สร้างเนื้อหา social media อัตโนมัติ
- แปลภาษาและปรับโทนเสียง
- โพสต์ลงแพลตฟอร์มต่างๆ
- วิเคราะห์ประสิทธิภาพเนื้อหา

**AI Services:**
- OpenAI GPT-4 (สร้างเนื้อหา)
- Hugging Face (แปลภาษา)
- Sentiment Analysis

### 📊 5. AI Data Analyst
**ระดับ: สูง**
- วิเคราะห์ข้อมูลยอดขายอัตโนมัติ
- สร้างรายงานพร้อม insights
- เสนอแนะกลยุทธ์ทางธุรกิจ
- ส่งรายงานประจำสัปดาห์

**AI Services:**
- OpenAI GPT-4 (วิเคราะห์)
- Code Interpreter
- Statistical Analysis

### 🔍 6. RAG Knowledge Base System
**ระดับ: ขั้นสูง**
- ตอบคำถามจากเอกสารบริษัท
- ใช้ Vector Database เก็บความรู้
- ค้นหาข้อมูลแบบ semantic
- อ้างอิงแหล่งที่มาได้

**AI Services:**
- OpenAI Embeddings
- Pinecone Vector DB
- GPT-4 (RAG)

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

## 📚 เอกสารและทรัพยากร

### หลักสูตรการเรียนรู้
1. **[n8n Basics](./docs/n8n-basics.md)** - พื้นฐาน n8n และ Node ต่างๆ
2. **[AI Integration](./docs/ai-integration.md)** - การใช้ AI ใน n8n
3. **[Best Practices](./docs/best-practices.md)** - แนวทางปฏิบัติที่ดี
4. **[Troubleshooting](./docs/troubleshooting.md)** - แก้ปัญหาที่พบบ่อย

### External Resources
- [n8n Official Documentation](https://docs.n8n.io/)
- [n8n Community Forum](https://community.n8n.io/)
- [OpenAI API Documentation](https://platform.openai.com/docs)
- [Claude API Documentation](https://docs.anthropic.com/)

## 🔧 การใช้งาน

### การ Import Workflow
1. เปิด n8n Web Interface
2. คลิก "+" → "Import from File"
3. เลือกไฟล์ `.json` จากโฟลเดอร์ `projects/`
4. กำหนดค่า credentials ต่างๆ
5. ทดสอบ workflow

### การสร้าง Workflow ใหม่
1. ศึกษา template ใน `templates/`
2. คัดลอกและปรับแต่งตามต้องการ
3. บันทึกเป็นไฟล์ใหม่
4. อัพเดท README ของโปรเจค

## 🎯 Use Cases

### สำหรับธุรกิจ
- **Customer Support**: ลดเวลาตอบคำถาม 80%
- **Content Marketing**: สร้างเนื้อหา 24/7
- **Data Analysis**: รายงานอัตโนมัติทุกสัปดาห์
- **Process Automation**: ลดงานซ้ำซ้อน

### สำหรับ Developers
- **API Integration**: เชื่อมต่อ services ต่างๆ
- **Monitoring**: ติดตามระบบแบบ real-time
- **Testing**: ทดสอบ API อัตโนมัติ
- **Deployment**: CI/CD pipelines

### สำหรับ Content Creators
- **Social Media**: โพสต์อัตโนมัติทุกแพลตฟอร์ม
- **SEO**: วิเคราะห์ keywords และ trends
- **Analytics**: ติดตามประสิทธิภาพเนื้อหา
- **Scheduling**: จัดการปฏิทินเนื้อหา

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

## 📈 Roadmap

### Q1 2025
- [ ] เพิ่ม Slack integration
- [ ] สร้าง Mobile notification system
- [ ] เพิ่ม Voice AI workflows

### Q2 2025
- [ ] Computer Vision workflows
- [ ] Advanced RAG with multiple sources
- [ ] Multi-language support

### Q3 2025
- [ ] Custom AI models integration
- [ ] Workflow marketplace
- [ ] Performance optimization

## 📞 ติดต่อและสนับสนุน

- **Issues**: [GitHub Issues](https://github.com/your-repo/n8n-ai/issues)
- **Discussions**: [GitHub Discussions](https://github.com/your-repo/n8n-ai/discussions)
- **Email**: your-email@domain.com

## 📄 License

MIT License - ดูรายละเอียดใน [LICENSE](LICENSE) file

---

**Happy Automating! 🚀**

> สร้างด้วย ❤️ โดยชุมชน n8n และ AI enthusiasts
