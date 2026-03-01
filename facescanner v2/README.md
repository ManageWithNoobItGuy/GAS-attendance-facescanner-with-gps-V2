# 🏢 Face Scan Attendance

> ระบบบันทึกเวลาเข้างานด้วยการสแกนใบหน้า + GPS Geofencing
> Deploy บน Netlify · ข้อมูลเก็บใน Google Sheets · ไม่ต้องการ Server

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![face-api.js](https://img.shields.io/badge/face--api.js-v0.22.2-blueviolet)
![Netlify](https://img.shields.io/badge/deploy-Netlify-00C7B7)
![Google Sheets](https://img.shields.io/badge/database-Google%20Sheets-34A853)

---

## ✨ คุณสมบัติ

| Feature | รายละเอียด |
|---------|-----------|
| 📷 **Face Recognition** | จดจำใบหน้าด้วย face-api.js (SSD MobileNet + FaceNet) ค่า threshold 0.45 |
| 📍 **GPS Geofencing** | ตรวจสอบตำแหน่งด้วยสูตร Haversine กำหนดรัศมีได้อิสระ |
| 📊 **Google Sheets** | บันทึกข้อมูลพนักงาน / ประวัติเข้างาน / ตั้งค่า GPS ใน Sheets |
| 🌐 **Static Hosting** | Frontend เป็น HTML/CSS/JS ล้วน Deploy Netlify ได้ทันที |
| 📱 **Mobile-First** | ออกแบบสำหรับการใช้งานบนมือถือ responsive ทุกขนาดจอ |
| 🌙 **Dark Glassmorphism UI** | ดีไซน์ modern dark theme พร้อม animation |

---

## 🏗️ สถาปัตยกรรมระบบ

```
┌─────────────────────────┐   fetch() (HTTPS REST JSON)   ┌──────────────────────────┐   R/W   ┌──────────────────┐
│   Netlify Static Site   │ ───────────────────────────▶  │  Google Apps Script      │ ──────▶ │  Google Sheets   │
│                         │                               │  Web App (REST API)      │         │                  │
│  index.html   (เมนู)    │   GET  ?action=getConfig      │                          │         │  📋 Users        │
│  register.html (ลงทะ)   │   GET  ?action=getKnownFaces  │  doGet(e)  → read        │         │  📋 Attendance   │
│  scan.html    (สแกน)    │   POST action=registerUser    │  doPost(e) → write       │         │  📋 Config       │
│  config.html  (ตั้งค่า) │   POST action=logAttendance   │                          │         │                  │
└─────────────────────────┘   POST action=saveConfig      └──────────────────────────┘         └──────────────────┘
```

---

## 🛠️ Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | HTML5, CSS3, Vanilla JavaScript |
| Face AI | [face-api.js](https://github.com/justadudewhohacks/face-api.js) v0.22.2 |
| Backend API | Google Apps Script Web App |
| Database | Google Sheets |
| Hosting | Netlify (Static) |
| Font | Sarabun (Google Fonts) |

---

## 📁 โครงสร้างไฟล์

```
facescanner v2/
├── index.html          ← หน้าเมนูหลัก
├── register.html       ← ลงทะเบียนใบหน้าพนักงาน
├── scan.html           ← สแกนใบหน้าเพื่อเข้างาน
├── config.html         ← ตั้งค่า GPS จุดเช็คอิน + API URL
├── js/
│   └── api-config.js   ← ไฟล์เก็บ GAS Web App URL (แก้ไขก่อน Deploy)
├── code.gs             ← Google Apps Script — คัดลอกไปวางใน GAS Editor
└── netlify.toml        ← Netlify build configuration
```

---

## 🚀 วิธีติดตั้งและใช้งาน

### Step 1 — ตั้งค่า Google Sheets & Apps Script

1. สร้าง **Google Sheets** ใหม่ (ไม่ต้องสร้าง sheet เพิ่ม ระบบจะสร้างให้อัตโนมัติ)
2. ไปที่ **Extensions → Apps Script**
3. ลบโค้ดเดิมออก แล้ว **วางเนื้อหาจากไฟล์ `code.gs`** ทั้งหมด
4. คลิก **Deploy → New deployment**
   - Type: **Web app**
   - Execute as: **Me**
   - Who has access: **Anyone**
5. กด **Deploy** แล้วคัดลอก **Web App URL** ที่ได้

> ⚠️ หาก script ขอสิทธิ์ access Google Sheets ให้กด **Allow** ทุกรายการ

---

### Step 2 — แก้ไข `js/api-config.js`

เปิดไฟล์ `js/api-config.js` แล้วแทนที่ URL ด้วย URL ที่ได้จาก Step 1:

```javascript
const GAS_API_URL = 'https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec';
```

---

### Step 3 — Deploy ขึ้น Netlify

**วิธีที่ 1 — Drag & Drop (ง่ายที่สุด)**
1. ไปที่ [app.netlify.com/drop](https://app.netlify.com/drop)
2. ลาก folder `facescanner v2` ทั้งโฟลเดอร์ไปวาง
3. รอสักครู่ → ได้ URL ทันที เช่น `https://amazing-site-123.netlify.app`

**วิธีที่ 2 — GitHub + Auto Deploy**
1. Push โค้ดขึ้น GitHub repository
2. ไปที่ [app.netlify.com](https://app.netlify.com) → **Add new site → Import an existing project**
3. เลือก GitHub repo → Netlify จะ auto deploy ทุกครั้งที่ push

---

### Step 4 — ตั้งค่าระบบครั้งแรก

1. เปิด `https://your-site.netlify.app/config.html`
2. ตรวจสอบว่า **GAS URL** ในช่องบนถูกต้อง
3. กด **"ดึงตำแหน่งปัจจุบัน"** หรือกรอก Latitude / Longitude ของจุดเช็คอิน
4. ระบุ **รัศมี** ที่ยอมรับ (หน่วย: กิโลเมตร) เช่น `0.1` คือ 100 เมตร
5. กด **"บันทึกการตั้งค่าทั้งหมด"**

---

## 📖 วิธีใช้งาน

### 👤 ลงทะเบียนพนักงานใหม่ — `/register.html`

1. กรอกชื่อ-นามสกุลพนักงาน
2. มองกล้อง **ตรงๆ** แล้วกด **"บันทึกใบหน้า"** (ครั้งที่ 1)
3. **หันซ้ายเล็กน้อย** กด **"บันทึกเพิ่ม"** (ครั้งที่ 2)
4. **หันขวาเล็กน้อย** กด **"บันทึกเพิ่ม"** (ครั้งที่ 3)

> 💡 บันทึกอย่างน้อย 3 มุมเพื่อความแม่นยำสูงสุด

---

### 📷 สแกนเข้างาน — `/scan.html`

```
เปิดหน้า scan.html
       ↓
ระบบตรวจสอบ GPS → อยู่ในรัศมีที่กำหนด?
       ↓ ใช่
โหลด AI Model (face-api.js)
       ↓
โหลดฐานข้อมูลพนักงานจาก Google Sheets
       ↓
กด "แตะเพื่อเริ่มสแกน" → กล้องเปิด
       ↓
ระบบสแกนทุก 500ms → พบใบหน้าตรงกัน?
       ↓ ใช่ (distance < 0.45)
แสดง Modal ยืนยัน — กด "ยืนยันเข้างาน"
       ↓
บันทึกใน Google Sheets (ชื่อ / เวลา / วันที่ / GPS / Map link)
```

---

### 📊 ดูรายงานการเข้างาน

เปิด **Google Sheets** → sheet **"Attendance"**

| Name | Time | Date | Latitude | Longitude | Google Map Link |
|------|------|------|----------|-----------|-----------------|
| สมชาย ใจดี | 08:30:15 | 1/3/2026 | 13.7563 | 100.5018 | https://maps.google.com/... |

---

## 🗄️ โครงสร้าง Google Sheets

ระบบสร้าง 3 sheets อัตโนมัติเมื่อใช้งานครั้งแรก:

### 📋 Users — ข้อมูลพนักงาน

| Column A | Column B | Column C |
|----------|----------|----------|
| Name | Face Descriptor (JSON array 128D) | Registered At |

### 📋 Attendance — ประวัติเข้างาน

| Column A | Column B | Column C | Column D | Column E | Column F |
|----------|----------|----------|----------|----------|----------|
| Name | Time | Date | Latitude | Longitude | Google Map Link |

### 📋 Config — การตั้งค่า GPS

| Parameter | Value |
|-----------|-------|
| Target Latitude | 13.7563 |
| Target Longitude | 100.5018 |
| Allowed Radius (KM) | 0.1 |

---

## ⚙️ การปรับแต่งค่า

### ปรับความเข้มงวดการจดจำใบหน้า

เปิดไฟล์ `scan.html` แก้ไขค่า `MATCH_THRESHOLD`:

```javascript
const MATCH_THRESHOLD = 0.45; // ค่าต่ำ = เข้มงวดขึ้น | ค่าสูง = หลวมขึ้น
```

| ค่า | ความหมาย |
|-----|---------|
| `0.35` | เข้มงวดมาก — แนะนำถ้าต้องการความปลอดภัยสูง |
| `0.45` | **ค่า default** — สมดุลระหว่างความแม่นยำและความสะดวก |
| `0.55` | หลวมกว่า — แนะนำถ้ากล้องคุณภาพต่ำหรือแสงน้อย |

### ปิดการตรวจสอบ GPS

ไปที่ `/config.html` แล้วใส่ค่า **รัศมี = `0`** → ระบบจะข้ามการตรวจสอบตำแหน่ง

---

## 🌐 Browser Compatibility

| Browser | รองรับ |
|---------|--------|
| Chrome 80+ | ✅ |
| Safari 14+ (iOS) | ✅ |
| Firefox 75+ | ✅ |
| Edge 80+ | ✅ |

> ⚠️ ต้องเข้าผ่าน **HTTPS** เท่านั้น (Netlify ให้ HTTPS อัตโนมัติ) เพื่อให้ `getUserMedia()` และ Geolocation API ทำงานได้

---

## ❓ Troubleshooting

| ปัญหา | วิธีแก้ไข |
|-------|---------|
| กล้องไม่เปิด | ตรวจสอบว่าเข้าผ่าน HTTPS และให้สิทธิ์ Camera ใน Browser |
| GPS ไม่ทำงาน | เปิดการแชร์ Location ใน Browser Settings |
| `เชื่อมต่อ API ไม่ได้` | ตรวจสอบ GAS URL ใน `js/api-config.js` และ config.html |
| `ไม่พบใบหน้า` | แสงต้องเพียงพอ มองตรงกล้อง ไม่มีอะไรบัง |
| `ความเหมือน: ต่ำ` | ลงทะเบียนใบหน้าเพิ่มในหลายมุมมากขึ้น |
| CORS Error | ตรวจสอบว่า GAS Deploy เป็น "Anyone" access |

---

## 📄 License

MIT License — ใช้งานได้อิสระ แก้ไขและ redistribute ได้

---

<div align="center">
  <sub>Built with ❤️ · face-api.js · Google Apps Script · Netlify</sub>
</div>
