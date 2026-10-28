# Quiz Arena

เว็บแอปเกมตอบคำถามสดหลายคนแข่งกัน (แบบ Kahoot) — ไม่ต้องมีบัญชีใดๆ เพื่อนกดลิงก์แล้วเล่นได้เลย

รันด้วย Firebase Firestore (ฟรี) เก็บสถานะเกมแบบ realtime และ host ด้วย Vercel + GitHub (ฟรี)

## 1) ตั้งค่า Firebase (ทำครั้งเดียว ~5 นาที)

1. เข้า https://console.firebase.google.com → "Add project" → ตั้งชื่อโปรเจกต์ (เช่น `quiz-arena`) → สร้างเสร็จ (ปิด Google Analytics ก็ได้ ไม่จำเป็น)
2. เมนูซ้าย → **Build > Firestore Database** → "Create database" → เลือก location ใกล้ๆ (เช่น `asia-southeast1`) → เริ่มด้วย **Production mode**
3. แท็บ **Rules** ของ Firestore วางกฎนี้แทนของเดิม แล้วกด **Publish**:

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /games/{code} {
         allow read, write: if true;
         match /{sub}/{docId} {
           allow read, write: if true;
         }
       }
     }
   }
   ```

   (กฎนี้เปิดให้อ่าน/เขียนได้เฉพาะข้อมูลใต้ `games/**` แบบไม่ต้องล็อกอิน — เหมาะกับเกมเล่นกันเองระหว่างเพื่อน ไม่ใช่แอปที่มีข้อมูลสำคัญ)

4. กลับหน้าหลักโปรเจกต์ → ไอคอน **⚙️ Project settings** → เลื่อนลงส่วน "Your apps" → กด ไอคอนเว็บ **`</>`** → ตั้งชื่อ app (เช่น `quiz-arena-web`) → **Register app** (ไม่ต้องติ๊ก Hosting)
5. จะได้ก้อนโค้ด `firebaseConfig = {...}` — คัดลอกค่าทั้งหมดมาวางแทนที่ค่า `YOUR_...` ใน [`firebase-config.js`](firebase-config.js) ของโฟลเดอร์นี้

## 2) เดโมทดสอบก่อนขึ้นเน็ต (ในเครื่องตัวเอง)

ดับเบิลคลิกเปิด `index.html` ได้เลย (ทดสอบสร้างห้อง/เข้าร่วมด้วยเบราว์เซอร์คนละแท็บบนเครื่องเดียวกันได้ทันทีหลังตั้งค่า Firebase เสร็จ)

## 3) ขึ้นเน็ตด้วย GitHub + Vercel (ฟรี, ให้เพื่อนเข้าจากมือถือได้จริง)

**3.1 ส่งขึ้น GitHub**

1. สร้าง repo ใหม่บน GitHub (Public หรือ Private ก็ได้ Vercel เข้าถึงได้ทั้งคู่) เช่น `quiz-arena`
2. อัปโหลด/push ไฟล์ 3 ไฟล์ในโฟลเดอร์นี้ (`index.html`, `firebase-config.js`, `README.md`) ขึ้น repo
   > **สำคัญ:** `firebase-config.js` ที่มีค่าจริงจะถูกอัปโหลดขึ้น repo ด้วย — ไม่เป็นไร เพราะค่าพวกนี้เป็น "public config" ของ Firebase (ความปลอดภัยจริงอยู่ที่ Firestore Rules ข้อ 3 ด้านบน ไม่ใช่การซ่อนค่านี้)

**3.2 ต่อ Vercel เข้ากับ repo**

1. เข้า https://vercel.com → **Sign up / Log in with GitHub** (ใช้บัญชี GitHub เดียวกับข้อ 3.1)
2. กด **Add New... > Project**
3. เลือก repo `quiz-arena` ที่เพิ่ง push ไป → **Import**
4. หน้า Configure Project: เป็นไฟล์ static ธรรมดา ไม่ต้องตั้งค่าอะไรเพิ่ม (Framework Preset ปล่อยเป็น "Other", ไม่ต้องใส่ Build Command) → กด **Deploy**
5. รอ ~30 วิ จะได้ลิงก์ประมาณ `https://quiz-arena-xxxx.vercel.app`
6. ส่งลิงก์นี้ให้เพื่อนได้เลย ไม่ต้องมีบัญชี Claude/GitHub/Google ใดๆ ทั้งสิ้น

**ข้อดีของทางนี้:** ทุกครั้งที่ push โค้ดใหม่ขึ้น GitHub (เช่น แก้คำถาม/ปรับดีไซน์) Vercel จะ deploy ให้อัตโนมัติ ไม่ต้องกด Settings > Pages ซ้ำแบบ GitHub Pages

## เกมที่ 2: ตู้ทายคำจากภาพ (`rebus.html`)

เปิดที่ `https://<ลิงก์-vercel>/rebus.html` — host สร้างห้อง ใส่รูปทีละช่อง (1–6 ช่อง) กด PLAY ให้ตู้หมุน แล้วกดเฉลยเอง ผู้เล่นใส่รหัสห้อง 5 ตัว + ชื่อ แล้วพิมพ์คำตอบ host เป็นคนติ๊ก ✓/✗ ให้แต่ละคำตอบเอง (✓ = 1 คะแนน, กดซ้ำ = ยกเลิก) ตอนกดเฉลย คำตอบที่ตรงเฉลยเป๊ะและยังไม่ได้ตรวจจะได้ ✓ อัตโนมัติ

- ใช้ Firebase ตัวเดียวกัน เก็บใต้ `games/rebus-<รหัส>` จึงใช้กฎ Firestore เดิมได้เลย ไม่ต้องเพิ่มกฎ
- ห้องอยู่ถาวร เตรียมโจทย์ล่วงหน้าได้ หน้าแรกมีรายการ "ห้องที่สร้างจากเครื่องนี้"
- รูปถูกย่อเป็น JPEG เล็กๆ เก็บในเอกสารคำถาม (Firestore จำกัด 1 MB ต่อเอกสาร)
- เฉลยเก็บแยกใน `secret/` ผู้เล่นไม่ได้โหลดมา แต่คนที่รู้รหัสห้องและแกะโค้ดเป็นยังอ่านได้ (กฎเปิด)

## หมายเหตุ

- เกมใช้ระบบให้คะแนนแบบ Kahoot: ตอบถูก + ตอบเร็ว = คะแนนเยอะกว่า (500–1000 แต้ม/ข้อ)
- ทุกคำถามมีเวลาจำกัด ปรับได้ 10–60 วินาทีตอนตั้งค่า
- host ต้องเปิดหน้าเว็บค้างไว้ตลอดเกม (เหมือน Kahoot จริง ถ้า host ปิดแท็บ เกมจะค้าง)
- Firestore free tier (Spark plan) รองรับการอ่าน/เขียนหลักหมื่นครั้ง/วันฟรี เพียงพอสำหรับเล่นกันเองสบายๆ
