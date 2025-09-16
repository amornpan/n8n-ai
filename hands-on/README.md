# Index ของ Hands-on Tutorials 📚

## 📝 รายการ Hands-on ทั้งหมด

### 🌤️ [Hands-on 1: ระบบแจ้งเตือนสภาพอากาศ](./01-weather-notification.md)
**ระดับ:** เริ่มต้น | **เวลา:** 45-60 นาที
- เช็คสภาพอากาศทุกเช้าด้วย OpenWeatherMap API
- ส่งแจ้งเตือนผ่าน Line Notify เมื่อฝนตก
- บันทึกข้อมูลใน Google Sheets อัตโนมัติ
- สร้างรายงานสรุปประจำสัปดาห์

### 📱 [Hands-on 2: ระบบติดตาม Social Media](./02-social-media-monitor.md)
**ระดับ:** กลาง | **เวลา:** 60-90 นาที
- ติดตาม mentions ใน Twitter/X แบบ real-time
- วิเคราะห์ sentiment ด้วย AI
- ส่งแจ้งเตือนไป Discord channel
- สร้าง analytics dashboard อัตโนมัติ

### 🤖 [Hands-on 3: AI Customer Support Bot](./03-ai-customer-support.md)
**ระดับ:** กลาง-สูง | **เวลา:** 90-120 นาที
- AI Bot ตอบคำถามลูกค้าอัตโนมัติ
- ระบบ escalation ไปยัง human agent
- Multi-platform support (Line, Discord)
- Analytics และ conversation logging

### 🎨 [Hands-on 4: AI Content Creator](./04-ai-content-creator.md)
**ระดับ:** กลาง-สูง | **เวลา:** 75-90 นาที
- สร้างเนื้อหา social media อัตโนมัติ
- โพสต์ใน Facebook, Twitter, LinkedIn
- Auto image selection จาก Unsplash
- Performance tracking และ optimization

### 📊 [Hands-on 5: AI Data Analyst](./05-ai-data-analyst.md)
**ระดับ:** สูง | **เวลา:** 90-120 นาที
- วิเคราะห์ข้อมูลธุรกิจด้วย AI
- สร้างรายงานพร้อม insights อัตโนมัติ
- Predictive analytics และ forecasting
- Real-time monitoring และ alerts

### 🧠 [Hands-on 6: RAG Knowledge Base System](./06-rag-knowledge-base.md)
**ระดับ:** ขั้นสูง | **เวลา:** 120-150 นาที
- ระบบ Q&A จากเอกสารบริษัท
- Semantic search ด้วย Vector Database
- Auto document ingestion และ updates
- Usage analytics และ quality monitoring

---

## 🎯 เส้นทางการเรียนรู้ที่แนะนำ

### สำหรับผู้เริ่มต้น
1. **Hands-on 1** → เรียนรู้พื้นฐาน n8n และ API integration
2. **Hands-on 2** → เพิ่มความซับซ้อนด้วย data processing
3. **Hands-on 3** → เริ่มใช้ AI และ conversation handling

### สำหรับระดับกลาง
1. **Hands-on 3** → AI integration และ multi-platform
2. **Hands-on 4** → Content automation และ social media
3. **Hands-on 5** → Data analytics และ business intelligence

### สำหรับระดับสูง
1. **Hands-on 5** → Advanced analytics และ predictive modeling
2. **Hands-on 6** → RAG system และ knowledge management
3. **สร้างโปรเจคผสม** → รวมทุกเทคนิคเข้าด้วยกัน

---

## 📋 ความรู้และเครื่องมือที่ต้องเตรียม

### เครื่องมือพื้นฐาน
- ✅ **n8n** (ติดตั้งแล้ว)
- ✅ **Google Account** (สำหรับ Sheets, Drive, Analytics)
- ✅ **OpenAI Account** (สำหรับ AI features)

### API Keys ที่จะใช้
- 🔑 **OpenAI API** - สำหรับ AI ทุกโปรเจค
- 🔑 **Line Notify** - สำหรับการแจ้งเตือน
- 🔑 **Discord Webhook** - สำหรับ team notifications
- 🔑 **Twitter API v2** - สำหรับ social media monitoring
- 🔑 **Facebook Graph API** - สำหรับ Facebook/Instagram
- 🔑 **Pinecone** - สำหรับ Vector Database ใน RAG

### ความรู้ที่ควรมี
- 📚 **JavaScript พื้นฐาน** - สำหรับ Code nodes
- 📚 **API concepts** - HTTP requests, JSON
- 📚 **Regular Expressions** - สำหรับ text processing (ไม่บังคับ)

---

## 🚀 สิ่งที่จะได้เรียนรู้

### จาก Automation Projects (1-2)
- การใช้ n8n nodes และ workflows
- API integration และ data processing
- Error handling และ monitoring
- Scheduling และ automation patterns

### จาก AI Projects (3-6)
- การใช้ OpenAI API และ prompting techniques
- AI content generation และ analysis
- Vector databases และ semantic search
- RAG (Retrieval Augmented Generation)
- Performance monitoring และ optimization

### จาก Business Applications
- Real-world use cases และ practical solutions
- Analytics และ reporting
- Multi-platform integration
- Scalability และ maintenance

---

## 💡 Tips การทำ Hands-on

### เตรียมตัวก่อนเริ่ม
1. **อ่าน README.md หลัก** เพื่อเข้าใจโครงสร้างโปรเจค
2. **เตรียม API keys** ตามที่แต่ละ hands-on ต้องการ
3. **สร้าง environment variables** ใน n8n
4. **ทดสอบการเชื่อมต่อ** ก่อนเริ่มสร้าง workflow

### ระหว่างทำ Hands-on
1. **ทำทีละขั้นตอน** ไม่รีบร้อน
2. **ทดสอบแต่ละ node** ก่อนต่อไปขั้นต่อไป
3. **เก็บ logs และ outputs** เพื่อ debug
4. **บันทึกค่า configurations** ที่สำคัญ

### หลังจากเสร็จ
1. **ทดสอบ end-to-end** ด้วยข้อมูลจริง
2. **ปรับแต่ง parameters** ให้เหมาะกับการใช้งาน
3. **สำรองข้อมูล** workflows เป็น JSON files
4. **สร้างเอกสาร** การใช้งานสำหรับทีม

---

## 🔗 ลิงก์และทรัพยากรเพิ่มเติม

### เอกสารอ้างอิง
- [n8n Official Documentation](https://docs.n8n.io/)
- [OpenAI API Documentation](https://platform.openai.com/docs)
- [Google APIs Documentation](https://developers.google.com/)
- [Discord Developer Portal](https://discord.com/developers/docs)

### Community และ Support
- [n8n Community Forum](https://community.n8n.io/)
- [GitHub Repository](https://github.com/n8n-io/n8n)
- [YouTube Tutorials](https://www.youtube.com/c/n8nio)

### เครื่องมือช่วยเหลือ
- [JSON Formatter](https://jsonformatter.org/) - สำหรับ debug API responses
- [Postman](https://www.postman.com/) - สำหรับทดสอบ APIs
- [ngrok](https://ngrok.com/) - สำหรับทดสอบ webhooks locally

---

## 🤝 การมีส่วนร่วมและ Feedback

หากคุณพบปัญหาหรือมีข้อเสนอแนะ:

1. **สร้าง Issue** ใน GitHub repository
2. **แชร์ประสบการณ์** ใน community forum  
3. **ส่ง feedback** ผ่าน Discord channel
4. **แนะนำ improvements** สำหรับ hands-on tutorials

---

**🎉 เริ่มต้นการเดินทางสู่การเป็น n8n AI Expert กันเลย!**

> สร้างด้วย ❤️ เพื่อการเรียนรู้และการพัฒนาทักษะ automation ด้วย AI
