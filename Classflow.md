🚀 Full Start-up Specification: Class Flow (Niche CRM MVP)
1. 🎯 Project Goal & Strategy
• Project Name: Class Flow
• Target Niche: สตูดิโอโยคะ, พิลาทิส, ฟิตเนสบูติก และโรงเรียนสอนพิเศษขนาดเล็ก
• Value Proposition: ระบบ SaaS ที่ช่วยให้สตูดิโอจัดการการจองคลาสและติดตามการหมดอายุของคอร์สแบบอัตโนมัติ เพื่อเพิ่มอัตราการต่อคอร์ส (Retention) และลดภาระงาน Admin
• Monetization Model: SaaS Subscription (Tiered Pricing: Starter, Pro, Studio Plus)
• Goal: พัฒนา MVP ภายใน 4-6 สัปดาห์ เพื่อ Launch และทดสอบตลาด
2. 💻 Tech Stack & EnvironmentComponentTechnology / ToolConfiguration NotesFrontendNext.js 16 (React 19.2, TypeScript)ใช้ App Router, เน้น Server Components สำหรับ Data Fetching ที่ปลอดภัย, Turbopack เป็น default bundlerStyling/UITailwind CSSสำหรับ Utility-first Styling และ Responsive DesignDatabaseSupabase (PostgreSQL)ใช้เป็นแกนหลักสำหรับ DB, Auth, RLS, และ Edge FunctionsSecurityRLS (Row Level Security)ต้องบังคับใช้ RLS ในทุกตารางข้อมูลหลักที่ผูกกับ studio_id
3. 🎨 Design Theme & Guidelines
• Aesthetics: Minimalist, Clean, High Whitespace, Professional, Mobile-First.
• Primary Colors: Deep Teal (#004D40) สำหรับ Headers/Nav.
• Accent Colors: Soft Sage/Mint (#81C784) สำหรับ CTAs, Active Status และเน้นข้อมูลสำคัญ
• Typography: Sans-serif (e.g., Inter/Prompt), เน้นความชัดเจนและอ่านง่าย
• Interaction: Rounded Corners ($4$px - $8$px), Intuitive Navigation
4. 💾 Database Schema (PostgreSQL & RLS Core)Critical RLS Requirement: ทุกตารางข้อมูลหลักต้องมี studio_id (FK) และถูกป้องกันด้วย RLS Policyตารางคอลัมน์สำคัญConstraint & RoleNotesstudiosid (PK), owner_id (FK), nameSaaS Rootข้อมูลพื้นฐานของสตูดิโอprofilesid (PK, FK auth.users), studio_id (FK), role ('Owner', 'Staff', 'Member'), phoneAuthenticationผูกผู้ใช้เข้ากับสตูดิโอcoursesid (PK), studio_id (FK), name, total_classes, duration_daysCore Inventoryข้อมูลแพ็กเกจที่ขายclassesid (PK), studio_id (FK), teacher_id (FK), start_time, capacitySchedulingคลาสที่เปิดสอนจริงmembershipsid (PK), profile_id (FK), course_id (FK), classes_remaining, expires_atTransactionalสถานะคอร์สที่ซื้อ (สำคัญต่อรายได้)bookingsid (PK), class_id (FK), profile_id (FK), membership_id (FK), checked_in (BOOLEAN)Transactionalบันทึกการจองและการเข้าเรียน/ไม่เรียน
5. 🛠️ Technical Specification: Core MVP Features
A. Authentication & Onboarding
1. Sign Up/Login: Implement Supabase Auth.
2. Initial Onboarding Flow: หลัง Sign Up, Owner ต้องสร้าง Row ในตาราง studios และเชื่อม profiles.studio_id เข้ากับ Studio ใหม่
B. Admin Dashboard (Role: Owner/Staff)
1. Classes CRUD: สร้าง, อ่าน, อัปเดต, ลบคลาสในตาราง classes (ต้อง Filter ด้วย studio_id)
2. Course Management: สร้างและแก้ไขแพ็กเกจ/คอร์สในตาราง courses
3. Member Check-in Action:
    ◦ สร้างปุ่ม/Modal สำหรับ Admin Check-in
    ◦ Logic: เมื่อ Check-in สำเร็จ ต้อง Update bookings.checked_in = TRUE และ Trigger Database Function/Logic เพื่อลด memberships.classes_remaining - 1
C. Client Module (Role: Member)
1. Public/Member Schedule View: หน้าแสดงตาราง classes ที่กำลังจะมาถึง
2. Class Booking Action: สมาชิกสามารถกด "จอง" (Insert Row ใน bookings) โดยมี Logic ตรวจสอบ:
    ◦ Class ไม่เต็ม (classes.capacity > จำนวน bookings ปัจจุบัน)
    ◦ Member มี memberships ที่ Active และมี classes_remaining > 0
3. My Membership View: หน้าแสดงรายการ memberships ที่ Active พร้อมแสดง classes_remaining และ expires_at
D. Automation Logic (Core Value Proposition)
1. Course Expiry Reminder:
    ◦ Implementation: ตั้งค่า Supabase Trigger/Edge Function ให้ทำงานเป็นประจำ
    ◦ Criteria: ค้นหา Row ใน memberships ที่ expires_at อยู่ในช่วง 7 วันนับจากวันนี้
    ◦ Action: ส่งข้อมูลไปยัง API ภายนอก (Mock API) เพื่อจำลองการส่งแจ้งเตือน (SMS/Line/Email)
6. 📈 SDLC & Next Steps
• Current Phase: Phase 1 (Core MVP Development)
• Next Action: Implement RLS Policies บนตารางหลักทั้งหมด (ตาม Logic ในข้อ 4) ก่อนเริ่ม Core Development
[End of Prompt]