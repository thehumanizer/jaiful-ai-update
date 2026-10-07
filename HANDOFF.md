# Brief: ขึ้นเว็บ JAIFUL AI Update บน aiupdate.jaiful.life

จาก: บิท (Cowork) → ถึง: โค้ดคุง (Claude Code)
อ่าน `CLAUDE.md` ก่อนเริ่ม ในโฟลเดอร์นี้มีเว็บที่ทำงานได้ครบแล้ว (หน้าใหม่ mobile-first + ข่าวจริง 16 วัน)
งานของเราคือ **เอาขึ้น GitHub → Netlify → ผูกโดเมน** ไม่ต้องออกแบบใหม่

## ข้อมูลที่มีแล้ว
- GitHub account: `thehumanizer`
- โดเมน `jaiful.life` จดที่ Squarespace
- Hosting ที่เลือก: Netlify (ฟรี, deploy อัตโนมัติจาก GitHub, HTTPS อัตโนมัติ)

## Phase 0 — เช็กบนเครื่อง (5 นาที)
1. `python3 -m http.server 8000` แล้วเปิดดู ให้พี่เบนซ์ดูบนมือถือด้วยถ้าทำได้ (เข้าผ่าน IP ในวง Wi-Fi เดียวกัน)
2. ยืนยันว่าแท็บ วันนี้/ค้นหา/ย้อนหลัง ทำงาน และไม่มี error ใน console

## Phase 1 — GitHub repo
1. ถามพี่เบนซ์: repo **public หรือ private** (เนื้อหาเป็นสาธารณะอยู่แล้ว public ได้ แต่ private ก็ใช้กับ Netlify ได้)
2. สร้าง repo ชื่อแนะนำ `thehumanizer/jaiful-ai-update` (ใช้ `gh repo create` ถ้ามี GitHub CLI ไม่มีก็ให้พี่สร้างที่ github.com แล้วส่ง URL มา)
3. `git init` → commit ทุกไฟล์ → push ขึ้น branch `main`

## Phase 2 — Netlify
1. ให้พี่เบนซ์ล็อกอิน Netlify → **Add new project → Import an existing project → GitHub** → เลือก repo นี้
   (หรือใช้ `npx netlify-cli` ถ้าพี่สะดวก: `netlify login` → `netlify init`)
2. Build command: เว้นว่าง / Publish directory: `.` (มีใน `netlify.toml` แล้ว)
3. ได้ URL ชั่วคราว `<ชื่อ>.netlify.app` → เปิดเช็กว่าทำงานเหมือนบนเครื่อง
4. เปลี่ยนชื่อ site ให้จำง่าย เช่น `jaiful-ai-update`

## Phase 3 — ผูกโดเมน aiupdate.jaiful.life
1. Netlify: **Domain management → Add a domain → `aiupdate.jaiful.life`** → Netlify จะบอกค่า CNAME (ปกติคือ `<ชื่อ>.netlify.app`)
2. พี่เบนซ์ไปที่ Squarespace (ต้องทำเอง เพราะต้องยืนยันรหัสผ่าน):
   `account.squarespace.com/domains` → เลือก `jaiful.life` → **DNS → DNS Settings** → เลื่อนไป **Custom Records** → **Add record**
   - Type: `CNAME`
   - Name: `aiupdate`
   - Data: `<ชื่อ>.netlify.app` (ไม่ต้องใส่ https:// หรือ /)
   - Save
3. รอ DNS กระจาย (มักไม่กี่นาทีถึงไม่กี่ชั่วโมง บางกรณีถึง 48 ชม.) → กด Verify ใน Netlify
4. Netlify ออก HTTPS ให้อัตโนมัติ เปิด **Force HTTPS**
5. ห้ามแตะ record อื่นของ jaiful.life (เว็บหลัก/อีเมล)

## Phase 4 — QA ก่อนแจกจริง
- [ ] เปิด https://aiupdate.jaiful.life บนมือถือ (iPhone + Android ถ้ามี) ต้องเข้าเว็บ ไม่เด้งไปแอปใด
- [ ] ไม่มี horizontal scroll ที่ 390px, โหมดมืดอ่านได้
- [ ] กด "คัดลอกลิงก์ข่าวนี้" แล้วเปิดลิงก์นั้น → ต้องเลื่อนไปที่ข่าวนั้นและกางอยู่
- [ ] แปะลิงก์ใน LINE / Facebook แล้วเห็นการ์ดพรีวิว (รูป `assets/og-image.png`) — ถ้า FB ไม่ขึ้นให้ลอง Facebook Sharing Debugger
- [ ] เพิ่มไว้ที่หน้าจอหลักได้ (ไอคอน JAIFUL ขึ้น)
- [ ] ทำ QR code ของ `https://aiupdate.jaiful.life` ไว้ให้พี่ใช้แจก

## Phase 5 — ส่งกลับให้บิท
เมื่อเว็บขึ้นแล้ว ให้สรุปให้พี่เบนซ์ส่งกลับมาที่บิท:
- repo full name (เช่น `thehumanizer/jaiful-ai-update`) และ branch (`main`)
- URL ของ Netlify site
บิทจะปรับงานประจำเช้าให้ commit `data/days/<date>.js` + `data/manifest.js` เข้า repo นี้ (รายละเอียดใน `docs/daily-routine.md`)
ช่วงแรกบิทจะส่งไปทั้งหน้าเดิมบน claude.ai และเว็บใหม่คู่กัน จนกว่าจะมั่นใจ

## ทำทีหลังได้ (ถามพี่ก่อน ไม่ต้องทำรอบนี้)
- ตัวนับผู้เข้าชม (Netlify Analytics / Plausible / GA4) — ถ้าเก็บข้อมูลคน ต้องมีข้อความ PDPA
- รูปพรีวิวแยกรายวัน (ต้องมี build step เล็กๆ)
- สมัครรับสรุปทาง LINE OA
- ช่องทางแจ้งแก้ไขข่าว (รอพี่เบนซ์ระบุช่องทาง)
