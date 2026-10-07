# งานประจำเช้าของบิท → repo นี้ (สำหรับอ้างอิง)

งานตั้งเวลา (Claude scheduled task, ~07:00 น. เวลาไทย) ทำตามลำดับ:
1. ค้นข่าว 24–36 ชม. ล่าสุด เขียน day object ตาม data contract ใน `CLAUDE.md`
2. เขียน `data/days/<DATE>.js` = `radarDay(<json>);`
3. อ่าน `data/manifest.js` → ลบ entry วันเดียวกัน → เพิ่ม `{d,h,n,b,u}` → เรียงใหม่สุดก่อน → เขียนกลับเป็นบรรทัดเดียว
4. Commit ทั้ง 2 ไฟล์เข้า `main` ผ่าน GitHub Contents API (`PUT /repos/{owner}/{repo}/contents/{path}` พร้อม `sha` ของไฟล์เดิมสำหรับ manifest)
   ข้อความ commit: `news: <DATE> (<n> items)`
5. Netlify deploy อัตโนมัติ → เช็กว่า `https://aiupdate.jaiful.life/data/days/<DATE>.js` เปิดได้

เงื่อนไข: งานตั้งเวลาต้องเชื่อมสิทธิ์ GitHub ของ repo นี้ก่อน ไม่เช่นนั้น commit ไม่ได้
