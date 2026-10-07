# JAIFUL AI Update — คู่มือโปรเจกต์สำหรับโค้ดคุง

เว็บสรุปข่าว AI รายวันภาษาไทยของ JAIFUL (เจ้าของ: พี่เบนซ์) ที่ **https://aiupdate.jaiful.life**
คนอ่านคือคนทำงานไทยที่กลัวตกข่าว AI ส่วนใหญ่เปิดบนมือถือตอนเช้า ภาษาต้องง่าย ใช้ได้จริง

## สถาปัตยกรรม (ตั้งใจให้เรียบง่าย)

- **Static site ล้วน ไม่มี build step ไม่มี framework** — `index.html` ไฟล์เดียว มี CSS/JS อยู่ข้างใน
- โฮสต์บน **Netlify** ดึงจาก GitHub branch `main` แล้ว deploy อัตโนมัติทุกครั้งที่ push (ตั้งค่าใน `netlify.toml`)
- โดเมนจดที่ **Squarespace** ใช้ CNAME `aiupdate` → `<site>.netlify.app`
- อย่าเพิ่ม bundler, npm dependency หรือ framework ถ้าพี่เบนซ์ไม่ได้ขอ

## Data contract — ห้ามเปลี่ยนโดยไม่คุยก่อน

ข้อมูลข่าวถูกเขียนโดย **งานประจำเช้าของ "บิท"** (Claude scheduled task, ~07:00 น. เวลาไทย) ซึ่งจะ commit 2 ไฟล์ทุกวัน:

1. `data/days/YYYY-MM-DD.js` — เนื้อหาทั้งไฟล์คือ `radarDay({...});`
2. `data/manifest.js` — บรรทัดเดียว `window.RADAR_MANIFEST=[...];` เรียงวันใหม่สุดก่อน

```
manifest entry: { d: "YYYY-MM-DD", h: headline, n: จำนวนข่าว, b: [brand keys], u: "YYYYMMDDHHMM" (version stamp) }

day object: { date, updatedAt, headline, lede, tldr: [3 ข้อ], trends: [{title, body}],
  items: [{ id, brand, level: "hot"|"notable"|"watch", date, title,
            keyfig?: {value (≤8 ตัวอักษร), label},
            summary, impact, who, opportunity, risk, action,   // 6 มุม Executive Summary — ต้องมีครบ
            tags: [...], source, url }] }

brand keys: openai, anthropic, google, meta, microsoft, xai, apple, china, others
```

- `headline` มีรูปแบบ `"หัวสั้น — รายละเอียด"` หน้าเว็บตัดที่ ` — ` เพื่อทำหัวใหญ่กับบรรทัดรอง
- หน้าเว็บโหลดไฟล์วันด้วย `data/days/<date>.js?v=<u>` (cache busting ด้วย `u`)
- **ห้ามแก้หรือลบเนื้อหาไฟล์ใน `data/days/` ที่มีอยู่** เป็นคลังข่าวย้อนหลัง ถ้าจำเป็นต้องแก้ให้ถามพี่เบนซ์ก่อน
- ถ้าจะเปลี่ยนชื่อไฟล์ path หรือ field ใด ต้องแจ้งพี่เบนซ์ เพราะงานประจำเช้าต้องแก้ตาม

## หลักการออกแบบ

- **Mobile-first** ทดสอบที่ความกว้าง 390px ก่อนเสมอ ห้ามมี horizontal scroll
- ลำดับหน้า: หัวข้อวัน → "ถ้ามีเวลา 30 วินาที" → รายการข่าวแบบย่อ (แตะเพื่อกาง 6 มุม) → เทรนด์ไว้ท้าย
- สีทั้งหมดเป็น CSS token ใน `:root` และมีโหมดมืดทั้งแบบ `prefers-color-scheme` และ `[data-theme]`
- ฟอนต์: **IBM Plex Sans Thai Looped** (เนื้อหาทั้งหมด อ่านง่ายสำหรับคนทั่วไป), **Mitr** (เฉพาะหัวข้อใหญ่ของวัน), **Fredoka** (wordmark และตัวเลขเด่น)
- แบรนด์ JAIFUL: gradient แดง→ส้ม `#FC212E → #FBA83A` และ tagline "AI มาคืนเวลา ให้เราได้กลับไปใส่ใจกัน"
- localStorage ใช้แค่ความสะดวกรายคน (วันที่เปิดล่าสุด, ข่าวที่อ่านแล้ว) ต้องอยู่ใน try/catch เสมอ

## ภาษาในหน้าเว็บ

- ไทยเป็นธรรมชาติ ใช้ศัพท์อังกฤษได้ **ห้ามใช้ "ผม" หรือ "คุณ"** ถ้าต้องมีสรรพนามให้ใช้ "เรา"
- ข้อความปุ่มบอกสิ่งที่จะเกิดตรงๆ เช่น "คัดลอกลิงก์ข่าวนี้" แล้วแจ้งผลว่า "คัดลอกลิงก์แล้ว"

## ทดสอบบนเครื่อง

```bash
python3 -m http.server 8000   # แล้วเปิด http://localhost:8000
```
เช็กทุกครั้งก่อน push: มือถือ 390px, โหมดมืด, แท็บ ค้นหา/ย้อนหลัง, ลิงก์ตรงข่าว `/#2026-10-07~<id>`, ไม่มี error ใน console
