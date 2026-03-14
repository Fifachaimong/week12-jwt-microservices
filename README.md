# ENGCE301 Lab — JWT Microservices Security Architecture

**ชื่อ-นามสกุล:** นฤชิต ไชยมงคล
**รหัสนักศึกษา:** 66543206012-7

---

## ภาพรวมโครงงาน

Lab นี้เป็นการพัฒนา **Secure Task Board System** โดยใช้แนวคิด **Microservices Architecture** และเพิ่มองค์ประกอบด้าน **Security Architecture**

ระบบประกอบด้วยหลายบริการ (Services) ที่แยกหน้าที่กันอย่างชัดเจน และสื่อสารกันผ่าน **API Gateway (Nginx)**

ใน Lab นี้มีการเพิ่มองค์ประกอบด้านความปลอดภัย เช่น

* JWT Authentication
* API Gateway
* Rate Limiting
* Role-Based Access Control
* Centralized Logging

---

## สถาปัตยกรรมระบบ

โครงสร้างระบบโดยรวม

```
Browser
   │
   ▼
Nginx API Gateway
   │
   ├── Auth Service
   │       └── PostgreSQL (auth-db)
   │
   ├── Task Service
   │       └── PostgreSQL (task-db)
   │
   └── User Service
           └── PostgreSQL (user-db)

Logging Stack
   ├── Loki
   └── Grafana
```

แนวคิดที่ใช้

* **Database per Service**
* **JWT Authentication**
* **API Gateway Pattern**
* **Centralized Logging**

---

## ส่วนประกอบของระบบ

### 1. Auth Service

หน้าที่

* Login
* Register
* Generate JWT Token
* Verify Token

ตัวอย่าง API

```
POST /api/auth/login
POST /api/auth/register
GET  /api/auth/verify
```

---

### 2. Task Service

หน้าที่

* Create Task
* Read Task
* Update Task
* Delete Task

ตัวอย่าง API

```
GET    /api/tasks
POST   /api/tasks
PUT    /api/tasks/:id
DELETE /api/tasks/:id
```

---

### 3. User Service

หน้าที่

* แสดงข้อมูลผู้ใช้
* ตรวจสอบสิทธิ์ของผู้ใช้

ตัวอย่าง API

```
GET /api/users/me
GET /api/users
```

---

## Security Mechanisms

ระบบมีการเพิ่มมาตรการด้านความปลอดภัยดังนี้

### JWT Authentication

ผู้ใช้ต้อง Login เพื่อรับ **JWT Token**

ตัวอย่าง header

```
Authorization: Bearer <token>
```

ทุก service จะตรวจสอบ token ก่อนอนุญาตให้เข้าถึง API

---

### API Gateway

ใช้ **Nginx** เป็น API Gateway

หน้าที่

* Routing request
* Forward Authorization header
* เพิ่ม security headers
* Rate limiting

---

### Rate Limiting

จำกัดจำนวน request ต่อ IP

```
Auth API : 20 requests/min
API      : 60 requests/min
```

หากเกินจะได้

```
HTTP 429 Too Many Requests
```

---

### Role Based Access Control

ระบบมี role

```
member
admin
```

ตัวอย่าง

* member → จัดการ task ของตัวเอง
* admin → ดูข้อมูลผู้ใช้ทั้งหมด

---

## Centralized Logging

ระบบใช้ **Loki + Grafana** สำหรับดู log จากทุก service

Flow ของ log

```
Container Logs
   │
   ▼
Loki Docker Driver
   │
   ▼
Loki
   │
   ▼
Grafana Dashboard
```

สามารถดู log ของแต่ละ service ได้ใน Grafana

---

# ใบงาน Week 12: Security Architecture Analysis

## ส่วนที่ 1 — Security Test Cases

การทดสอบ API และ security อยู่ในไฟล์

```
REPORT.md
```

เนื้อหาในรายงานประกอบด้วย

* Login success
* Login fail
* Access API without JWT
* Access API with invalid token
* Role-based access control
* Rate limit test

---

## ส่วนที่ 2 — Architecture Comparison

การเปรียบเทียบสถาปัตยกรรมก่อนและหลังเพิ่ม security

ไฟล์

```
docs/c2-security-architecture-week6.drawio
docs/c2-security-architecture.drawio
```

เนื้อหา

* Architecture เดิม (Week 6)
* Architecture ใหม่ (Week 12)
* การเพิ่ม Auth Service
* การเพิ่ม API Gateway
* การเพิ่ม Logging Stack

---

## ส่วนที่ 3 — Architecture Decision Record (ADR)

การอธิบายเหตุผลในการออกแบบสถาปัตยกรรม

ไฟล์

```
docs/ADR-001-auth-service.md
```

เนื้อหา

* ปัญหาที่ต้องแก้
* ตัวเลือกที่พิจารณา
* เหตุผลที่เลือก Auth Service แยกออกมา
* ผลกระทบต่อระบบ

---

## วิธีรันระบบ

ติดตั้ง Loki Docker Driver

```
docker plugin install grafana/loki-docker-driver:latest \
  --alias loki \
  --grant-all-permissions
```

รันระบบ

```
docker compose up --build
```

เปิดใช้งาน

```
http://localhost
```

Grafana

```
http://localhost:3030
```

---

## เทคโนโลยีที่ใช้

* Node.js
* Express.js
* PostgreSQL
* Docker
* Docker Compose
* Nginx
* JWT
* Loki
* Grafana

---

## สรุป

Lab นี้แสดงให้เห็นการออกแบบ **Security-Aware Microservices Architecture**

โดยมีองค์ประกอบสำคัญ ได้แก่

* JWT Authentication
* API Gateway
* Role-Based Access Control
* Centralized Logging

ซึ่งช่วยเพิ่มความปลอดภัย ความสามารถในการขยายระบบ และการตรวจสอบการทำงานของระบบ
