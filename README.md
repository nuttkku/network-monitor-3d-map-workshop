<div align="center">
  <img src="picture/wunca46.jpg" alt="WUNCA46 Banner" width="100%" style="max-width: 1200px; border-radius: 10px;">
</div>

<br>

# 🗺️ ปฏิวัติการ Monitor: เปลี่ยนข้อมูล Network ให้เห็นภาพบนแผนที่ 3 มิติ

> 🎓 **Workshop:** ปฏิวัติการ Monitor (W013)
> 📅 12 กุมภาพันธ์ 2569 | ⏰ 09:00-16:00 น.
> 🔗 [รายละเอียดงานอบรม](https://wunca46.uni.net.th/workshop-detail/15)

---

## ⚠️ คำเตือนสำคัญ - Security Warning

> **🔒 โปรเจกต์นี้ออกแบบมาเพื่อการศึกษาและการฝึกอบรม (Workshop/Training) เท่านั้น**
>
> **🚫 ห้ามนำไปใช้ใน Production Environment โดยตรง!**
>
> Configuration ในโปรเจกต์นี้เน้นความสะดวกในการเรียนรู้ โดยมีการตั้งค่าเริ่มต้นที่ **ไม่ปลอดภัย** เช่น:
> - ใช้รหัสผ่าน default สำหรับ Database, Redis, และ SNMP
> - ไม่มี Authentication/Encryption สำหรับ MQTT Broker
> - เปิด Ports ให้เข้าถึงได้โดยไม่มีการจำกัด
> - ไม่มี SSL/TLS encryption
>
> **หากต้องการนำไปใช้งานใน Production กรุณา:**
> 1. ✅ อ่านคู่มือการตั้งค่าความปลอดภัยในแต่ละโฟลเดอร์ (ส่วน Security)
> 2. ✅ เปลี่ยนรหัสผ่านและ credentials ทั้งหมดให้แข็งแรง
> 3. ✅ เปิดใช้งาน Authentication/Authorization ทุกระบบ
> 4. ✅ ตั้งค่า Firewall และจำกัดการเข้าถึง
> 5. ✅ เปิดใช้งาน SSL/TLS encryption
> 6. ✅ ศึกษาเพิ่มเติมจาก Best Practices ของแต่ละเครื่องมือ
>
> 📚 **สำหรับผู้เข้าอบรม:** คุณสามารถใช้งานตาม Lab นี้ได้เลย ไม่ต้องกังวล! แต่โปรดจำไว้ว่า สิ่งที่เรียนรู้ที่นี่เป็นเพียง "sandbox" สำหรับการเรียนรู้เท่านั้น

---

## 🎯 Lab Overview

ในแล็ปนี้คุณจะได้เรียนรู้การสร้างระบบ Network Monitoring แบบ end-to-end และ**แสดงผลบนแผนที่ 3 มิติ**:

1. **ติดตั้ง LibreNMS** บน Docker สำหรับเป็น Monitoring System
2. **สร้าง MikroTik RouterOS VM** บน VirtualBox พร้อม configuration
3. **Monitor MikroTik** ด้วย LibreNMS ผ่าน SNMP protocol
4. **ดึงข้อมูลผ่าน API** เพื่อนำไปประมวลผลและแสดงผล
5. **แสดงผลบนแผนที่ 3D** ด้วย Node-RED + MQTT พร้อม location data

### 🎓 วัตถุประสงค์การอบรม

1. **เปลี่ยนมุมมอง** จากการดูสถานะแบบนามธรรม ไปสู่การแสดงภาพเชิงพื้นที่บนแผนที่ 3 มิติ
2. **เข้าใจขั้นตอน** การทำงานตั้งแต่ต้นจนจบ - จาก monitoring data ถึง 3D visualization
3. **ดึงข้อมูล** จาก LibreNMS ผ่าน API และประมวลผลแบบ real-time
4. **จัดเตรียมข้อมูล** พร้อม location data สำหรับนำไปแสดงผลบนแผนที่ 3 มิติ
5. **ส่งข้อมูล** ผ่าน MQTT protocol ให้พร้อมเชื่อมต่อกับ platform ต่างๆ

### 📊 สิ่งที่จะได้เรียนรู้

- ✅ Docker Compose orchestration
- ✅ Network Monitoring concepts (SNMP, Polling, Metrics)
- ✅ VirtualBox networking (Host-Only, NAT, Internal)
- ✅ MikroTik RouterOS configuration
- ✅ SNMP v2c setup และ troubleshooting
- ✅ **RESTful API integration (LibreNMS API)**
- ✅ **Flow-based programming (Node-RED)**
- ✅ **MQTT protocol สำหรับ IoT messaging**
- ✅ **Geolocation และ 3D visualization**
- ✅ **Real-time monitoring บนแผนที่ 3 มิติ**

### ⏱️ ระยะเวลา

- **Lab 1:** 20-25 นาที (Setup LibreNMS)
- **Lab 2:** 30-40 นาที (Setup MikroTik)
- **Lab 3:** 15-20 นาที (Integration & Monitoring)
- **Lab 4:** 30-40 นาที (API Integration - ใจความสำคัญ)
- **Lab 5:** 30-40 นาที (Data Preparation สำหรับ 3D Platform - ใจความสำคัญ)

### 📋 Prerequisites

**Software:**
- [Docker Desktop](https://docs.docker.com/get-docker/) 20.10+ (Windows/macOS/Linux)
- [VirtualBox](https://www.virtualbox.org/wiki/Downloads) 6.0+
- Terminal/PowerShell access
- Web Browser (Chrome, Firefox, Edge)

**Hardware:**
- CPU: 4 cores (recommended)
- RAM: 6 GB available
- Disk: 20 GB free space
- Internet connection

**Knowledge:**
- Basic Linux commands
- Basic networking concepts (IP, subnet, gateway)
- Docker basics (optional but helpful)

---

## 🏗️ Lab Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        Host Machine                              │
│                                                                  │
│  ┌──────────────────────┐       ┌──────────────────────────┐   │
│  │  LibreNMS (Docker)   │       │  MikroTik RouterOS (VM)  │   │
│  │  ==================   │       │  ====================    │   │
│  │  • Port: 8000        │◄─────►│  • ether1: 192.168.56.10│   │
│  │  • MariaDB           │ SNMP  │  • ether2: NAT          │   │
│  │  • Redis             │ Poll  │  • ether3: Internal     │   │
│  │  • Dispatcher        │       │  • ether4: Internal     │   │
│  └──────────────────────┘       └──────────────────────────┘   │
│           │                               │                     │
│           └───────────────┬───────────────┘                     │
│                           │                                     │
│                   Host-Only Network                             │
│                   192.168.56.0/24                               │
└─────────────────────────────────────────────────────────────────┘
```

**Network Design:**
- **Host-Only Network (192.168.56.0/24):** สำหรับ management และ monitoring
- **LibreNMS:** Host เข้าถึงผ่าน `localhost:8000`
- **MikroTik:** IP `192.168.56.10` บน ether1
- **SNMP:** Port 161/UDP สำหรับ polling data

---

## 🧪 LAB 1: ติดตั้งและรัน LibreNMS

**🎯 วัตถุประสงค์:** ติดตั้ง LibreNMS Monitoring System ด้วย Docker Compose

**⏱️ เวลา:** 15-20 นาที

### Step 1.1: เตรียม Environment

```bash
# Clone repository
git clone <repository-url>
cd wunca46buu

# ตรวจสอบ Docker
docker --version
docker-compose --version
```

**Expected Output:**
```
Docker version 20.10.x or higher
Docker Compose version v2.x.x or higher
```

### Step 1.2: เข้าไปยัง LibreNMS Directory

```bash
cd librenms
ls -la
```

**ไฟล์ที่ควรเห็น:**
- `docker-compose.yml` - Configuration file
- `.env.example` - ตัวอย่างไฟล์ environment variables
- `README.md` - คู่มือฉบับเต็ม

### Step 1.3: (Optional) ตั้งค่า Environment Variables

> 💡 **สำหรับ Workshop:** ข้ามขั้นตอนนี้ได้! Docker Compose จะใช้ค่า default ที่ปลอดภัยพอสำหรับการเรียนรู้
>
> 🔒 **สำหรับ Production:** ต้องทำขั้นตอนนี้! อ่านคู่มือในส่วน "Security Setup" ของ [librenms/README.md](librenms/README.md)

ถ้าต้องการเปลี่ยนค่า default (เช่น รหัสผ่าน, port):

```bash
# Copy ไฟล์ตัวอย่าง
cp .env.example .env

# แก้ไขค่าที่ต้องการ (optional)
nano .env

# ตัวอย่างค่าที่อาจต้องเปลี่ยน:
# - LIBRENMS_PORT=8000  (ถ้า port 8000 ถูกใช้แล้ว)
# - MYSQL_PASSWORD=librenms  (ถ้าต้องการรหัสผ่านอื่น)
```

**สำหรับ Workshop ให้ข้ามไปขั้นตอนถัดไป →**

### Step 1.4: Start LibreNMS Containers

```bash
# Start ทุก services (MariaDB, Redis, LibreNMS, Dispatcher)
docker-compose up -d

# ตรวจสอบสถานะ
docker-compose ps
```

**Expected Output:**
```
NAME                   STATUS    PORTS
librenms_db            Up        3306/tcp
librenms_redis         Up        6379/tcp
librenms               Up        0.0.0.0:8000->8000/tcp
librenms_dispatcher    Up        -
```

✅ **All 4 containers ต้องมีสถานะ "Up"**

### Step 1.5: ดู Logs (Optional)

```bash
# ดู logs เพื่อตรวจสอบการทำงาน
docker-compose logs -f librenms
```

**รอจนเห็น:**
```
[OK] Database migrations completed
[OK] LibreNMS is ready
```

**กด `Ctrl+C` เพื่อออกจากโหมดดู logs**

### Step 1.6: สร้าง Admin User

```bash
# สร้าง admin user สำหรับ login
docker exec librenms lnms user:add admin -p Admin@123 --role admin
```

**Expected Output:**
```
User admin added successfully
```

💡 **จดข้อมูล Login:**
- Username: `admin`
- Password: `Admin@123`

### Step 1.7: เข้าถึง LibreNMS Web Interface

1. เปิด Web Browser
2. เข้าไปที่: **http://localhost:8000**
3. Login ด้วย username และ password ที่สร้างไว้

**✅ Success Criteria:**
- [ ] เห็นหน้า Login page
- [ ] Login สำเร็จ
- [ ] เห็น Dashboard (ยังไม่มีอุปกรณ์)

### 📸 Screenshot Lab 1

Dashboard ควรแสดง:
```
Devices: 0
Ports: 0
Services: 0
```

### 🎉 LAB 1 Complete!

คุณได้ติดตั้ง LibreNMS สำเร็จแล้ว ตอนนี้พร้อมที่จะเพิ่มอุปกรณ์เข้ามา monitor!

---

## 🧪 LAB 2: ติดตั้งและรัน MikroTik RouterOS

**🎯 วัตถุประสงค์:** สร้าง MikroTik RouterOS VM และตั้งค่า network interfaces

**⏱️ เวลา:** 20-30 นาที

**📖 คู่มือฉบับเต็ม:** [mikrotik/README.md](mikrotik/README.md) (มีรายละเอียดทุกขั้นตอน)

### Step 2.1: ดาวน์โหลด RouterOS

1. ไปที่: https://mikrotik.com/download
2. เลือก **Cloud Hosted Router (CHR)**
3. เลือก **OVA** image
4. ดาวน์โหลดไฟล์ `.ova`

### Step 2.2: Import OVA ใน VirtualBox

1. เปิด **VirtualBox**
2. **File** → **Import Appliance...**
3. เลือกไฟล์ `.ova` ที่ดาวน์โหลด
4. ปรับแต่ง settings:
   - **Name:** `MikroTik-Router`
   - **RAM:** `256 MB` (แนะนำ)
   - **CPU:** `2 cores` (optional)
5. คลิก **Import**

### Step 2.3: ตั้งค่า Network Interfaces

**สร้าง Host-Only Network (ถ้ายังไม่มี):**

1. **File** → **Host Network Manager**
2. คลิก **Create**
3. ตั้งค่า:
   - IPv4 Address: `192.168.56.1`
   - IPv4 Network Mask: `255.255.255.0`

**ตั้งค่า VM Adapters:**

1. คลิกขวาที่ VM → **Settings** → **Network**
2. ตั้งค่าแต่ละ adapter:

| Adapter | Attached to | Name/Network | Purpose |
|---------|-------------|--------------|---------|
| **Adapter 1** | Host-only Adapter | vboxnet0 | Management (LibreNMS) |
| **Adapter 2** | NAT | - | Internet Access |
| **Adapter 3** | Internal Network | intnet1 | LAN 1 |
| **Adapter 4** | Internal Network | intnet2 | LAN 2 |

3. คลิก **OK**

### Step 2.4: Start VM และ Login

1. Start VM: **Start** → **Normal Start**
2. รอจนเห็น login prompt (~30 วินาที)

```
MikroTik RouterOS 7.x
Login: admin
Password: [กด Enter - ไม่มีรหัสผ่าน]
```

3. Login สำเร็จ → เห็น prompt:
```
[admin@MikroTik] >
```

### Step 2.5: ตั้งค่า IP Address สำหรับ Management

```bash
# ตั้ง IP สำหรับ ether1 (Host-Only)
[admin@MikroTik] > /ip address add address=192.168.56.10/24 interface=ether1

# ตรวจสอบ
[admin@MikroTik] > /ip address print
```

**Expected Output:**
```
0   192.168.56.10/24   192.168.56.0    ether1
```

### Step 2.6: ทดสอบ Connectivity

จากเครื่อง host (Terminal/PowerShell):

```bash
ping 192.168.56.10
```

**Expected Output:**
```
Reply from 192.168.56.10: bytes=32 time<1ms TTL=64
```

✅ **ถ้า ping ได้ = network configuration ถูกต้อง!**

### Step 2.7: เปิด SNMP Service

```bash
# เปิด SNMP
[admin@MikroTik] > /snmp set enabled=yes

# ตั้งค่า contact และ location
[admin@MikroTik] > /snmp set contact="admin@lab.local" location="Lab Network"

# ตรวจสอบ
[admin@MikroTik] > /snmp print
```

**Expected Output:**
```
enabled: yes
contact: admin@lab.local
location: Lab Network
...
```

### Step 2.8: ตรวจสอบ SNMP Community

```bash
# ดู community (default มี "public" อยู่แล้ว)
[admin@MikroTik] > /snmp community print
```

**Expected Output:**
```
0   name="public" addresses=0.0.0.0/0 security=none read-access=yes
    write-access=no
```

💡 **Community "public" พร้อมใช้งาน ไม่ต้องสร้างเพิ่ม**

### Step 2.9: ทดสอบ SNMP

จากเครื่อง host หรือ LibreNMS container:

```bash
# จาก LibreNMS container
docker exec librenms snmpwalk -v 2c -c public 192.168.56.10 system
```

**Expected Output:**
```
SNMPv2-MIB::sysDescr.0 = STRING: RouterOS CHR
SNMPv2-MIB::sysObjectID.0 = OID: SNMPv2-SMI::enterprises.14988.1
SNMPv2-MIB::sysUpTime.0 = Timeticks: (123456) 0:20:34.56
SNMPv2-MIB::sysContact.0 = STRING: admin@lab.local
SNMPv2-MIB::sysName.0 = STRING: MikroTik
SNMPv2-MIB::sysLocation.0 = STRING: Lab Network
```

✅ **ถ้าเห็น output เหล่านี้ = SNMP ทำงานถูกต้อง!**

### 🎉 LAB 2 Complete!

MikroTik RouterOS พร้อมให้ LibreNMS เข้ามา monitor แล้ว!

**Summary:**
- ✅ VM running
- ✅ IP: 192.168.56.10
- ✅ Ping ได้จาก host
- ✅ SNMP enabled
- ✅ Community: public

---

## 🧪 LAB 3: Monitor MikroTik บน LibreNMS

**🎯 วัตถุประสงค์:** เพิ่ม MikroTik เข้าสู่ LibreNMS และดูข้อมูล monitoring

**⏱️ เวลา:** 10-15 นาที

### Step 3.1: เพิ่มอุปกรณ์ผ่าน Web Interface

1. เปิด LibreNMS: **http://localhost:8000**
2. Login ด้วย `admin` / `Admin@123`
3. ไปที่ **Devices** → **Add Device** (มุมบนขวา)

### Step 3.2: กรอกข้อมูลอุปกรณ์

**Device Information:**
```
Hostname or IP:     192.168.56.10
SNMP Version:       v2c
Community:          public
Port:               161
```

**Optional Settings:**
```
Display Name:       MikroTik-Lab-Router
Hardware:           [ปล่อยว่าง - auto-detect]
OS:                 [ปล่อยว่าง - auto-detect]
```

**Other Options:**
- ✅ เปิด **ICMP Ping**
- ❌ ไม่ต้องเลือก **Force add**

### Step 3.3: Add Device

1. คลิก **Add Device**
2. รอ LibreNMS ทำการ:
   - ✅ ตรวจสอบ SNMP connectivity
   - ✅ Detect device type และ OS
   - ✅ Discovery interfaces และ sensors
   - ✅ สร้าง RRD files

**Expected Result:**
```
✅ Device added successfully!
   Device: MikroTik-Lab-Router (192.168.56.10)
   OS: RouterOS
   Hardware: CHR
```

3. คลิก **View Device** เพื่อดูรายละเอียด

### Step 3.4: ดูข้อมูลอุปกรณ์

คุณจะเห็น:

**Overview Tab:**
- Device Status: **Up** (สีเขียว)
- Uptime: เวลาที่ RouterOS ทำงาน
- Location: Lab Network
- Contact: admin@lab.local
- Hardware: CHR
- Version: RouterOS 7.x

**Health Tab:**
- CPU Usage graph
- Memory Usage graph
- Disk Usage
- System Uptime

**Ports Tab:**
- ether1 (Host-Only) - Status: Up
- ether2 (NAT) - Status: Up
- ether3 (Internal) - Status: Up
- ether4 (Internal) - Status: Up

**Graphs Tab:**
- Traffic In/Out graphs (5 min, 1 hour, 1 day, 1 week)
- Packet rates
- Error rates

### Step 3.5: รอให้ Poller เก็บข้อมูล

```bash
# ตรวจสอบ polling status
docker exec librenms lnms device:poll 192.168.56.10 -vvv
```

**Poller จะทำงานทุก 5 นาที (default)** เพื่อเก็บ metrics

### Step 3.6: ดู Dashboard

1. กลับไปที่ **Dashboard** (หน้าหลัก)
2. ตอนนี้จะเห็น:

```
Devices: 1 (Up: 1, Down: 0)
Ports: 4 (Up: 4, Down: 0)
```

3. ดู widgets ต่างๆ:
   - **Device Summary**
   - **Recent Events**
   - **Alert Summary**
   - **Port Usage**

### Step 3.7: สร้าง Traffic บน MikroTik (Optional)

เพื่อดูกราฟที่มีข้อมูล:

```bash
# SSH เข้า MikroTik
ssh admin@192.168.56.10

# Ping ไปที่ Internet (generate traffic)
[admin@MikroTik] > /ping 8.8.8.8 count=100

# หรือ
[admin@MikroTik] > /tool fetch url="http://speedtest.tele2.net/10MB.zip"
```

### Step 3.8: ตรวจสอบกราฟ

1. กลับไปที่ LibreNMS
2. **Devices** → **MikroTik-Lab-Router** → **Graphs**
3. เลือก **ether2** (NAT interface)
4. ดูกราฟ Traffic In/Out

**✅ ควรเห็นกราฟมี spike ขึ้นมา!**

### 🎉 LAB 3 Complete!

ยินดีด้วย! คุณได้สร้างระบบ Network Monitoring แบบสมบูรณ์แล้ว!

**Achievement Unlocked:**
- ✅ LibreNMS monitoring system
- ✅ MikroTik RouterOS as monitored device
- ✅ SNMP polling active
- ✅ Real-time graphs และ metrics
- ✅ Full network visibility

---

## 🧪 LAB 4: ดึงข้อมูลผ่าน LibreNMS API

**🎯 วัตถุประสงค์:** เรียนรู้การดึงข้อมูล monitoring ผ่าน RESTful API เพื่อนำไปประมวลผลและแสดงผลแบบ real-time

**⏱️ เวลา:** 30-40 นาที

**📖 คู่มือฉบับเต็ม:** [librenms-api/README.md](librenms-api/README.md) (มีตัวอย่าง code ครบถ้วน)

**🎓 ส่วนที่ 2 ของงานอบรม:** LAB นี้เป็นใจความสำคัญ - เรียนรู้การเข้าถึงข้อมูลผ่าน API เพื่อนำไปสร้างระบบแสดงผลแบบ custom

### Step 4.1: ตั้งค่า Environment Variables (สำหรับ Node.js Scripts)

> 💡 **สำหรับ Workshop:** ในขั้นตอนนี้เราจะใช้ `.env` file เพื่อเก็บ API Token อย่างปลอดภัย

```bash
# เข้าโฟลเดอร์ librenms-api
cd librenms-api

# ติดตั้ง dependencies (ถ้ายังไม่ได้ติดตั้ง)
npm install

# Copy ไฟล์ .env.example เป็น .env
cp .env.example .env
```

ไฟล์ `.env` จะมีหน้าตาแบบนี้:

```bash
API_URL=http://localhost:8000/api/v0
API_TOKEN=your-api-token-here
DEVICE_IP=192.168.56.10
```

**ยังไม่ต้องแก้ไขตอนนี้** - เราจะใส่ API Token ในขั้นตอนถัดไป

### Step 4.2: สร้าง API Token

1. Login เข้า LibreNMS: `http://localhost:8000`
2. คลิก **Username** (มุมบนขวา) → **My Settings**
3. เลือกแท็บ **API Settings**
4. คลิก **Create API Token**
5. ใส่ Description: `Lab API Token`
6. คลิก **Create Token**
7. **Copy token และเก็บไว้!** (จะแสดงครั้งเดียว)

```
Token: YOUR_TOKEN
```

### Step 4.3: บันทึก API Token ลงใน .env File

```bash
# แก้ไขไฟล์ .env
nano .env

# หรือใช้ text editor อื่นที่ชอบ
```

แก้บรรทัดนี้:
```bash
# Before
API_TOKEN=your-api-token-here

# After (ใส่ token ที่ได้จาก LibreNMS)
API_TOKEN=YOUR_TOKEN
```

บันทึกไฟล์ แล้วตรวจสอบ:
```bash
# ตรวจสอบว่าไฟล์ถูกต้อง (อย่าแสดง token ต่อหน้าคนอื่น!)
cat .env
```

> 🔒 **Security Note:** ไฟล์ `.env` ถูกป้องกันโดย `.gitignore` แล้ว จะไม่ถูก commit เข้า git

### Step 4.4: ทดสอบ API ด้วย cURL

```bash
# ดึงรายการอุปกรณ์ทั้งหมด
curl -H "X-Auth-Token: YOUR_TOKEN" \
  http://localhost:8000/api/v0/devices
```

**Expected Output:**
```json
{
  "status": "ok",
  "devices": [
    {
      "device_id": 1,
      "hostname": "192.168.56.10",
      "display": "MikroTik-Lab-Router",
      "os": "routeros",
      "status": 1
    }
  ]
}
```

### Step 4.5: ดึงข้อมูล MikroTik

```bash
# ดึงข้อมูลอุปกรณ์
curl -H "X-Auth-Token: YOUR_TOKEN" \
  http://localhost:8000/api/v0/devices/192.168.56.10

# ดึงข้อมูล ports
curl -H "X-Auth-Token: YOUR_TOKEN" \
  http://localhost:8000/api/v0/devices/192.168.56.10/ports
```

### Step 4.6: ทดสอบ Node.js Scripts (ใช้ .env file)

ตอนนี้เราจะรันสคริปต์ที่เตรียมไว้แล้ว ซึ่งจะอ่านค่าจากไฟล์ `.env` โดยอัตโนมัติ:

```bash
# ตรวจสอบสถานะ ether1
node check-ether1.js
```

**Expected Output:**
```
🔍 Checking ether1 status...

==================================================
📡 MikroTik ether1 Status
==================================================
Interface:      ether1
Description:    N/A
Alias:          N/A
Type:           ethernetCsmacd
Status:         UP ✅
Admin Status:   up
Speed:          1000 Mbps
MTU:            1500
MAC Address:    XX:XX:XX:XX:XX:XX
==================================================

📊 Traffic Statistics:
--------------------------------------------------
In Octets:      1.23 GB
Out Octets:     789.01 MB
In Packets:     1,234
Out Packets:    5,678
In Errors:      0
Out Errors:     0
--------------------------------------------------

✅ Check completed successfully
```

ทดสอบสคริปต์อื่นๆ:

```bash
# แสดงรายการอุปกรณ์ทั้งหมด
node list-devices.js

# Monitor แบบละเอียด
node mikrotik-monitor.js

# Monitor แบบ real-time (ทุก 5 วินาที)
node monitor-realtime.js
```

> 💡 **ข้อดีของการใช้ .env:**
> - ไม่ต้อง hardcode API Token ในโค้ด
> - ปลอดภัย - ไฟล์ .env ไม่ถูก commit
> - เปลี่ยน token ง่าย - แก้ที่เดียวใช้ได้ทุกสคริปต์
> - พร้อมสำหรับ Production

### 🎉 LAB 4 Complete!

คุณได้เรียนรู้การใช้ LibreNMS API แล้ว!

**New Skills Unlocked:**
- ✅ API authentication with tokens
- ✅ RESTful API concepts
- ✅ Programmatic data retrieval
- ✅ Node.js API integration
- ✅ Environment variables management (.env files)
- ✅ Secure credential storage

---

## 🧪 LAB 5: จัดเตรียมข้อมูลสำหรับ Platform 3D ด้วย Node-RED + MQTT

**🎯 วัตถุประสงค์:** จัดเตรียมและส่งข้อมูล monitoring พร้อม location data ผ่าน MQTT protocol เพื่อให้พร้อมนำไปแสดงผลบนแพลตฟอร์มแผนที่ 3 มิติ

**⏱️ เวลา:** 30-40 นาที

**📖 คู่มือฉบับเต็ม:** [nodered/README.md](nodered/README.md) (มี advanced flows และ troubleshooting)

**🎓 ส่วนที่ 2 ของงานอบรม - ใจความสำคัญ:**
LAB นี้เป็น**ขั้นตอนสำคัญ** - เรียนรู้การดึงข้อมูลจาก API, เพิ่ม location data (lat, long, altitude), และส่งผ่าน MQTT ให้พร้อมสำหรับนำไปแสดงผลบนแผนที่ 3 มิติ

🔗 [ข้อมูลงานอบรม](https://wunca46.uni.net.th/workshop-detail/15)

### Step 5.1: เตรียม Environment

```bash
# เข้าโฟลเดอร์ Node-RED
cd nodered

# สร้าง directory สำหรับ Node-RED data
mkdir -p nodered_data
```

> 💡 **สำหรับ Workshop:** Node-RED ไม่ต้องใช้ `.env` file เพราะเราจะตั้งค่าผ่าน UI
>
> 🔒 **สำหรับ Production:** ดูขั้นตอน Security Setup ใน [nodered/README.md](nodered/README.md)

### Step 5.2: เชื่อมต่อ Docker Network

```bash
# สร้าง network (ถ้ายังไม่มี)
docker network create monitoring_network

# Connect LibreNMS container เข้า network
docker network connect monitoring_network librenms
```

### Step 5.3: Start Node-RED

```bash
# Start Node-RED container
docker-compose up -d

# ตรวจสอบสถานะ
docker-compose ps
```

**Expected Output:**
```
NAME        STATUS    PORTS
nodered     Up        0.0.0.0:1880->1880/tcp, 0.0.0.0:1883->1883/tcp
```

### Step 5.4: เข้าถึง Node-RED

1. เปิด Web Browser
2. ไปที่: **http://localhost:1880**
3. คุณจะเห็น Node-RED Flow Editor

### Step 5.5: ติดตั้ง Aedes MQTT Broker

1. ใน Node-RED คลิก **Menu (≡)** → **Manage palette**
2. ไปที่แท็บ **Install**
3. ค้นหา `node-red-contrib-aedes`
4. คลิก **Install**
5. คลิก **Install** อีกครั้งเพื่อยืนยัน
6. รอจนการติดตั้งเสร็จ

### Step 5.6: เพิ่ม Aedes Broker Node

1. ลาก **aedes broker** node จาก palette มาวางใน workspace
2. Double-click เพื่อตั้งค่า:
   - **Name:** `MQTT Broker`
   - **Port:** `1883`
   - คลิก **Done**
3. คลิก **Deploy**

**หมายเหตุ:** MQTT Broker จะรันภายใน Node-RED ไม่ต้อง container แยก

### Step 5.7: สร้าง Flow

#### 1. เพิ่ม Inject Node

1. ลาก **inject** node มาวาง
2. Double-click:
   - **Name:** `Every 30 seconds`
   - **Repeat:** `interval` → `30` `seconds`
   - คลิก **Done**

#### 2. เพิ่ม HTTP Request Node

1. ลาก **http request** node มาวาง
2. Double-click:
   - **Name:** `Get mikrotik status`
   - **Method:** `GET`
   - **URL:** `http://librenms:8000/api/v0/ports?device_id=1&columns=port_id,ifName,ifOperStatus,ifAdminStatus,ifSpeed,ifMtu,ifPhysAddress,ifInOctets,ifOutOctets,ifInUcastPkts,ifOutUcastPkts,ifInErrors,ifOutErrors`
   - **Headers:** เพิ่ม header
     - **Name:** `X-Auth-Token`
     - **Value:** `your-api-token-here` (ใส่ API Token จาก LAB 4)
   - **Return:** `a parsed JSON object`
   - คลิก **Done**

**หมายเหตุ:**
- ⚠️ **สำคัญ:** ต้องใช้ `/api/v0/ports?device_id=1&columns=...` เพื่อให้ได้ข้อมูลครบถ้วน
- ❌ อย่าใช้ `/api/v0/devices/1/ports` (จะได้ข้อมูลไม่ครบ - เฉพาะ port_id และ ifName)

#### 3. เพิ่ม Function Node

1. ลาก **function** node มาวาง
2. Double-click:
   - **Name:** `Extract ether1 data`
   - **Function:** ใส่ code:

```javascript
// ตรวจสอบว่ามี payload หรือไม่
if (!msg.payload) {
    node.warn('❌ No payload received');
    return null;
}

// ตรวจสอบ API status
if (msg.payload.status !== 'ok') {
    node.warn('❌ API Error: ' + (msg.payload.message || 'Unknown'));
    return null;
}

// ตรวจสอบว่ามี ports array หรือไม่
if (!msg.payload.ports || !Array.isArray(msg.payload.ports)) {
    node.warn('❌ No ports array in response');
    return null;
}

const ports = msg.payload.ports;
const ether1 = ports.find(p => p.ifName === 'ether1');

if (!ether1) {
    node.warn('❌ ether1 not found. Available ports: ' + ports.map(p => p.ifName).join(', '));
    return null;
}

node.warn('✅ ether1 found: ' + ether1.ifOperStatus);

// ตำแหน่งของอุปกรณ์ (สำหรับแผนที่ 3D)
const location = {
    lat: 16.4322,        // Latitude
    long: 102.8236,      // Longitude
    altitude: 170        // ความสูง (เมตร)
};

msg.payload = {
    timestamp: new Date().toISOString(),
    device: {
        ip: '192.168.56.101',
        name: 'MikroTik-Router',
        location: location
    },
    interface: ether1.ifName,
    status: ether1.ifOperStatus,
    adminStatus: ether1.ifAdminStatus,
    speed: ether1.ifSpeed / 1000000,
    mtu: ether1.ifMtu,
    macAddress: ether1.ifPhysAddress,
    statistics: {
        inOctets: ether1.ifInOctets || 0,
        outOctets: ether1.ifOutOctets || 0,
        inPackets: ether1.ifInUcastPkts || 0,
        outPackets: ether1.ifOutUcastPkts || 0,
        inErrors: ether1.ifInErrors || 0,
        outErrors: ether1.ifOutErrors || 0
    }
};

msg.topic = 'mikrotik/ether1/status';
return msg;
```

   - คลิก **Done**

#### 4. เพิ่ม MQTT Output Node

1. ลาก **mqtt out** node มาวาง
2. Double-click:
   - **Server:** คลิก pencil icon
     - **Server:** `localhost`
     - **Port:** `1883`
     - คลิก **Add**
   - **Topic:** ปล่อยว่าง (ใช้จาก msg.topic)
   - **QoS:** `0`
   - **Retain:** ✅ เปิด
   - **Name:** `Publish to MQTT`
   - คลิก **Done**

#### 5. เพิ่ม Debug Node

1. ลาก **debug** node มาวาง
2. ต่อจาก Function node
3. ตั้งชื่อ: `Debug output`

### Step 5.8: เชื่อมต่อ Nodes

เชื่อมต่อตามลำดับ:
```
[Inject] → [HTTP Request] → [Function] → [MQTT Out]
                                   ↓
                              [Debug]
```

### Step 5.9: Deploy Flow

1. **Double-click** ที่ node **"Get ether1 status"** (HTTP Request)
2. ในส่วน **Headers** → แก้ไข `X-Auth-Token`
3. ใส่ API Token จาก LAB 4
4. คลิก **Done**

1. คลิกปุ่ม **Deploy** มุมบนขวา
2. รอจนเห็นข้อความ **"Successfully deployed"**

### Step 5.10: ทดสอบ Flow

1. คลิกที่ปุ่มสีน้ำเงินซ้ายมือของ Inject node
2. ดูใน **Debug** tab ขวามือ
3. ควรเห็น JSON output พร้อมข้อมูล ether1

**Expected Output:**
```json
{
  "timestamp": "2026-02-09T15:30:00.000Z",
  "device": {
    "ip": "192.168.56.10",
    "name": "MikroTik-Router",
    "location": {
      "lat": 16.4322,
      "long": 102.8236,
      "altitude": 170
    }
  },
  "interface": "ether1",
  "status": "up",
  "adminStatus": "up",
  "speed": 1000,
  "mtu": 1500,
  "macAddress": "XX:XX:XX:XX:XX:XX",
  "statistics": {
    "inOctets": 123456,
    "outOctets": 789012,
    "inPackets": 1234,
    "outPackets": 5678,
    "inErrors": 0,
    "outErrors": 0
  }
}
```

**หมายเหตุ:** Payload รวมข้อมูล location (lat, long, altitude) สำหรับแสดงบนแผนที่ 3D

### Step 5.11: ทดสอบ MQTT (Optional)

**วิธีที่ 1: ใช้ MQTT In Node**

1. ลาก **mqtt in** node มาวาง
2. ตั้งค่า Server: `localhost:1883`, Topic: `mikrotik/ether1/status`
3. ต่อกับ debug node
4. Deploy และทดสอบ

**วิธีที่ 2: ใช้ mosquitto_sub**

```bash
# Subscribe จาก host machine
mosquitto_sub -h localhost -t "mikrotik/ether1/status" -v
```

**Expected Output:**
```
mikrotik/ether1/status {"timestamp":"2026-02-09T15:30:00.000Z","device":{"ip":"192.168.56.10","name":"MikroTik-Router","location":{"lat":16.4322,"long":102.8236,"altitude":170}},...}
```

### 📍 แก้ไข Location Data

เปิดไฟล์ Function node และแก้ไขค่า `location` ตามตำแหน่งจริงของอุปกรณ์:

```javascript
const location = {
    lat: 16.4322,        // Latitude (ละติจูด)
    long: 102.8236,      // Longitude (ลองจิจูด)
    altitude: 170        // ความสูงจากระดับน้ำทะเล (เมตร)
};
```

**วิธีหาพิกัด:**
1. เปิด Google Maps
2. คลิกขวาที่ตำแหน่งที่ต้องการ
3. คลิก "What's here?"
4. คัดลอกพิกัด (เช่น `16.4322, 102.8236`)

**ตัวอย่างพิกัดในประเทศไทย:**
- มหาวิทยาลัยขอนแก่น: `lat: 16.4322, long: 102.8236, altitude: 170`
- กรุงเทพฯ: `lat: 13.7563, long: 100.5018, altitude: 15`
- เชียงใหม่: `lat: 18.8042, long: 98.9219, altitude: 310`

### 🎉 LAB 5 Complete!

ยินดีด้วย! คุณได้จัดเตรียมข้อมูลสำหรับแผนที่ 3D เรียบร้อยแล้ว!

**Achievement Unlocked:**
- ✅ Node-RED flow-based programming
- ✅ MQTT broker setup (Aedes) - รันภายใน Node-RED
- ✅ LibreNMS API integration ผ่าน Node-RED
- ✅ Real-time data streaming via MQTT
- ✅ Location data preparation (lat, long, altitude)
- ✅ **ข้อมูลพร้อมนำไปแสดงผลบนแผนที่ 3D**

**MQTT Payload Structure:**
```json
{
  "device": {
    "ip": "192.168.56.10",
    "name": "MikroTik-Router",
    "location": {
      "lat": 16.4322,      // สำหรับแสดงบนแผนที่
      "long": 102.8236,
      "altitude": 170
    }
  },
  "interface": "ether1",
  "status": "up",
  ...
}
```

**MQTT Topic Structure:**
```
mikrotik/ether1/status  → สถานะ ether1 พร้อม location
mikrotik/+/status       → สถานะทุก interface (wildcard)
mikrotik/#              → ทุก message จาก mikrotik
```

**การใช้งาน Location Data:**
- ข้อมูลพร้อมสำหรับนำไปแสดงตำแหน่งอุปกรณ์บนแผนที่ 3D
- รองรับการ visualize network topology แบบ geospatial
- รองรับ geolocation-based monitoring และ alerts
- สามารถเชื่อมต่อกับ 3D platform ต่างๆ ผ่าน MQTT

---

## 🎓 Lab Summary

### ส่วนที่ 1: พื้นฐาน Network Monitoring

1. **LAB 1:** ติดตั้ง LibreNMS monitoring system ด้วย Docker Compose (4 containers)
2. **LAB 2:** สร้าง MikroTik RouterOS VM พร้อม 4 network interfaces และเปิด SNMP
3. **LAB 3:** เชื่อมต่อและ monitor MikroTik ผ่าน LibreNMS

### ส่วนที่ 2: API Integration และ Data Preparation ⭐

4. **LAB 4:** ดึงข้อมูล monitoring ผ่าน RESTful API - **ใจความสำคัญ**
5. **LAB 5:** จัดเตรียมข้อมูลสำหรับ 3D Platform ด้วย Node-RED + MQTT - **ใจความสำคัญ**

### 🎯 จุดเด่นของงานอบรม

**ปฏิวัติการ Monitor** = เปลี่ยนจากการดูตัวเลข → **จัดเตรียมข้อมูลให้พร้อมแสดงบนแผนที่ 3 มิติ**

```
ข้อมูล LibreNMS → API → Node-RED → MQTT → พร้อมสำหรับ 3D Platform
(นามธรรม)                 (เพิ่ม location)  (ข้อมูลสำเร็จรูป)
```

**ผลลัพธ์:** ข้อมูล monitoring พร้อม location (lat, long, altitude) ส่งผ่าน MQTT แบบ real-time พร้อมนำไปแสดงผลบนแผนที่ 3 มิติ

### Metrics ที่ Monitor ได้

- ✅ Device uptime และ availability
- ✅ Interface status (Up/Down)
- ✅ Traffic In/Out (bandwidth usage)
- ✅ Packet rates (pps)
- ✅ Error และ discard counters
- ✅ CPU และ memory usage
- ✅ System information

### Network Protocols ที่ใช้

- **SNMP v2c:** สำหรับ polling metrics (UDP port 161)
- **ICMP:** สำหรับ ping monitoring
- **HTTP:** สำหรับ web interface (TCP port 8000)
- **SSH:** สำหรับ management (TCP port 22)
- **MQTT:** สำหรับ IoT messaging (TCP port 1883)

---

## 📚 คู่มือเพิ่มเติม

### 📖 [LibreNMS - คู่มือฉบับสมบูรณ์](librenms/README.md)

รายละเอียดครบถ้วนเกี่ยวกับ LibreNMS:
- 🏗️ Architecture diagrams (Mermaid)
- ⚙️ Configuration reference
- 🔧 Advanced commands
- 💾 Backup & restore
- 🔍 Troubleshooting (10+ ปัญหา)
- 🎯 Best practices

### 🔧 [MikroTik RouterOS - คู่มือฉบับสมบูรณ์](mikrotik/README.md)

รายละเอียดครบถ้วนเกี่ยวกับ MikroTik:
- 📦 Import & setup VirtualBox
- 🌐 Network configuration details
- 🔊 SNMP configuration (CLI + GUI)
- ➕ Device management
- 🔍 Troubleshooting (8+ ปัญหา)
- 🎯 Security best practices

### 🔌 [LibreNMS API - คู่มือฉบับสมบูรณ์](librenms-api/README.md)

รายละเอียดครบถ้วนเกี่ยวกับ LibreNMS API:
- 🔑 API Token creation และ management
- 🔌 Authentication และ connection
- 📡 API Endpoints reference
- 💻 ตัวอย่าง code (Python, JavaScript, cURL)
- 🎯 ดึงข้อมูล MikroTik ผ่าน API
- ⚠️ Error handling และ best practices
- 🔒 Security considerations

### 🔄 [Node-RED + MQTT - คู่มือฉบับสมบูรณ์](nodered/README.md)

รายละเอียดครบถ้วนเกี่ยวกับ Node-RED และ MQTT:
- 🏗️ Architecture overview (LibreNMS → Node-RED → MQTT)
- 🐳 Docker Compose setup (Node-RED + Mosquitto)
- 🔄 Flow-based programming concepts
- 📡 MQTT topics และ payload structure
- 💻 ตัวอย่าง Flows (basic + advanced)
- 🎯 Real-time monitoring และ alerts
- 🐛 Troubleshooting และ best practices

---

## 🔧 คำสั่งที่ใช้บ่อย

### LibreNMS (Docker)

```bash
# Start/Stop services
docker-compose up -d
docker-compose stop
docker-compose restart

# ดู logs
docker-compose logs -f librenms

# เข้า container shell
docker exec -it librenms bash

# เพิ่มอุปกรณ์
docker exec librenms lnms device:add <ip> -c <community>

# Poll ทันที
docker exec librenms lnms device:poll <ip> -vvv

# Validate configuration
docker exec librenms lnms validate
```

### MikroTik RouterOS

```bash
# SSH login
ssh admin@192.168.56.10

# ดู interfaces
/interface print

# ดู IP addresses
/ip address print

# ตรวจสอบ SNMP
/snmp print
/snmp community print

# ดู system resources
/system resource print

# Backup config
/export file=backup
```

### VirtualBox

```bash
# Start VM (headless)
VBoxManage startvm "MikroTik-Router" --type headless

# Stop VM
VBoxManage controlvm "MikroTik-Router" poweroff

# List VMs
VBoxManage list vms

# Show VM info
VBoxManage showvminfo "MikroTik-Router"
```

---

## 🆘 Troubleshooting

### ปัญหาที่พบบ่อย

#### 1. LibreNMS ไม่สามารถ poll MikroTik

**ตรวจสอบ:**
```bash
# Test ping
docker exec librenms ping -c 3 192.168.56.10

# Test SNMP
docker exec librenms snmpwalk -v 2c -c public 192.168.56.10 system

# ดู poller logs
docker-compose logs -f dispatcher
```

**แก้ไข:**
- ตรวจสอบว่า MikroTik SNMP enabled: `/snmp print`
- ตรวจสอบ community string: `/snmp community print`
- ตรวจสอบ network connectivity

#### 2. MikroTik ไม่ได้รับ IP Address

**แก้ไข:**
```bash
# SSH เข้า MikroTik (ผ่าน VirtualBox console)
[admin@MikroTik] > /ip address add address=192.168.56.10/24 interface=ether1
[admin@MikroTik] > /ip address print
```

#### 3. Graphs ไม่แสดงข้อมูล

**รอ 5-10 นาที** เพื่อให้ poller เก็บข้อมูลพอ

```bash
# Force poll ทันที
docker exec librenms lnms device:poll 192.168.56.10 -vvv
```

**ดูคู่มือ Troubleshooting แบบละเอียดที่:**
- [LibreNMS Troubleshooting](librenms/README.md#การแก้ไขปัญหา)
- [MikroTik Troubleshooting](mikrotik/README.md#ตรวจสอบและแก้ไขปัญหา)

---

## 🎯 Next Steps

หลังจากจบ Lab แล้ว คุณสามารถ:

### 1. เพิ่มอุปกรณ์เพิ่มเติม
- สร้าง MikroTik VM ตัวที่ 2
- เพิ่ม Linux server
- เพิ่ม Windows machine (ถ้ามี SNMP)

### 2. ตั้งค่า Alerts
- Alert เมื่อ device down
- Alert เมื่อ bandwidth สูง
- Alert เมื่อ CPU/Memory สูง

### 3. สร้าง Custom Dashboards
- Dashboard สำหรับ overview
- Dashboard ต่อ team/location
- Widget ต่างๆ ตามต้องการ

### 4. Advanced Configuration
- ตั้งค่า SNMP v3 (secure)
- API integration
- External alerting (Email, Slack, Discord)
- Backup automation

---

## 📞 Resources

### Official Documentation
- [LibreNMS Docs](https://docs.librenms.org/)
- [MikroTik Wiki](https://wiki.mikrotik.com/)
- [Docker Docs](https://docs.docker.com/)
- [VirtualBox Manual](https://www.virtualbox.org/manual/)

### Community
- [LibreNMS Community](https://community.librenms.org/)
- [LibreNMS Discord](https://discord.gg/librenms)
- [MikroTik Forum](https://forum.mikrotik.com/)

---

## 📝 License

โปรเจคนี้ใช้งานสำหรับการศึกษาและพัฒนา

- LibreNMS: GPL v3.0
- MikroTik RouterOS: Commercial License

---

<div align="center">

## 🎉 Congratulations!

**คุณได้เรียนรู้การ "ปฏิวัติการ Monitor" แล้ว!**

### 🗺️ จากนี้ไป...

คุณได้**จัดเตรียมข้อมูล Network ให้พร้อมสำหรับนำไปแสดงผลบนแผนที่ 3 มิติ**

ข้อมูลที่คุณได้จัดเตรียม:
- 📍 ตำแหน่งอุปกรณ์จริง (lat, long, altitude)
- 📊 สถานะและ metrics แบบ real-time
- 🔄 ส่งผ่าน MQTT protocol
- 🌐 พร้อมเชื่อมต่อกับ 3D platform ต่างๆ

**ข้อมูลพร้อมใช้งานสำหรับ:**
- แสดงตำแหน่งบนแผนที่ 3D
- เปลี่ยนสี/ไอคอนตามสถานะ (🟢 Up / 🔴 Down)
- Visualize network topology แบบ geospatial
- Alert และ monitoring แบบ real-time

*ดูเอกสารเพิ่มเติม:*
- [📖 LibreNMS Full Guide](librenms/README.md)
- [🔧 MikroTik Full Guide](mikrotik/README.md)
- [🔌 API Integration Guide](librenms-api/README.md)
- [🗺️ 3D Map Visualization Guide](nodered/README.md)

---

**🎓 งานอบรม:** ปฏิวัติการ Monitor (W013)
**🔗 ข้อมูลเพิ่มเติม:** [wunca46.uni.net.th/workshop-detail/15](https://wunca46.uni.net.th/workshop-detail/15)

**Happy Visualizing! 🚀🗺️**

</div>
