# MoveEasy Transport — Integrated Platform Starter

แพลตฟอร์มเดียวสำหรับลูกค้า คนขับ และแอดมิน โดยใช้ Supabase/PostgreSQL ชุดเดียวกัน

## ส่วนประกอบ
- `apps/ops-web`: เว็บลูกค้า + แอดมิน (Next.js)
- `apps/driver-mobile`: แอปคนขับ Android/iOS (Expo React Native + Background Location)
- `supabase/migrations`: โครงสร้างฐานข้อมูลและ Row Level Security เบื้องต้น
- `supabase/functions/gps-ingest`: API รับ GPS โดยตรวจสอบบัญชีคนขับและงานที่กำลังทำ
- `packages/shared`: ชนิดข้อมูล/สถานะกลางที่ใช้ร่วมกัน

## เริ่มต้น
1. สร้าง Supabase project
2. รัน `supabase/migrations/001_platform.sql` ใน Supabase SQL Editor
3. ตั้งค่า environment ตาม `.env.example` ของแต่ละแอป
4. ติดตั้ง dependencies ในแต่ละแอป
5. Deploy Edge Function `gps-ingest`
6. ตั้งค่า Google Maps Platform API และจำกัด key ตามแพลตฟอร์ม/API ที่ใช้

## Web
```bash
cd apps/ops-web
cp .env.example .env.local
npm install
npm run dev
```

## Driver mobile
```bash
cd apps/driver-mobile
cp .env.example .env
npm install
npx expo prebuild
npx expo run:android
# หรือ npx expo run:ios
```
ต้องทดสอบบนอุปกรณ์จริงและ build แบบ native สำหรับ Background Location; Expo Go ไม่เพียงพอสำหรับยืนยันพฤติกรรม production

## Flow รวม
ลูกค้าส่ง booking -> แอดมินตรวจสอบ/มอบหมายรถและคนขับ -> คนขับเริ่มงาน -> แอปส่ง GPS -> แอดมินเห็น fleet map และลูกค้าเห็นเฉพาะงานของตัวเอง -> คนขับจบงานและระบบหยุด GPS

## ข้อจำกัดก่อน production
นี่คือโครงสร้างเริ่มต้นที่เชื่อม flow หลักในระดับโค้ด แต่ยังต้องตั้งค่า credentials, ตรวจสอบ RLS ตามนโยบายจริง, เพิ่มหน้าจัดการแอดมินให้ครบ, Google Maps route/distance, push notifications, payment gateway, audit logs, monitoring, backups และทดสอบความปลอดภัย/อุปกรณ์จริงก่อนเปิดใช้
- iOS background tracking ต้องขอ permission ที่เหมาะสมและเปิด Background Modes; force-quit อาจหยุดการติดตาม
- Android ต้องรองรับ foreground service notification และ permission ตาม Android version
- ติดตามเฉพาะช่วงงานที่คนขับเริ่มไว้ พร้อมแจ้งวัตถุประสงค์และขอความยินยอม
- ห้ามนำ service-role key ไปใส่ในเว็บหรือแอปมือถือ
- ราคาตัวอย่าง 15 บาท/กม. เป็นค่าเริ่มต้นที่แก้ไขได้ ไม่ใช่ราคายืนยัน
