# 🗺️ จัดเตรียมข้อมูลสำหรับ Platform 3D: Node-RED + MQTT Integration

**ส่วนที่ 2 ของงานอบรม: ปฏิวัติการ Monitor**

คู่มือการติดตั้ง Node-RED และตั้งค่า Flow-based Programming เพื่อดึงข้อมูลจาก LibreNMS API และส่งต่อผ่าน MQTT พร้อม location data เพื่อจัดเตรียมข้อมูลสำหรับ Platform 3D

> 🎯 **จุดประสงค์หลัก:** เปลี่ยนข้อมูล Network จากตัวเลขนามธรรม → **ข้อมูลที่พร้อมใช้สำหรับ Platform 3D**

---

## 📋 สารบัญ

- [ภาพรวม](#ภาพรวม)
- [Architecture](#architecture)
- [ติดตั้ง Node-RED](#ติดตั้ง-node-red)
- [ตั้งค่า MQTT Broker](#ตั้งค่า-mqtt-broker)
- [ตั้งค่า Flow](#ตั้งค่า-flow)
- [ทดสอบระบบ](#ทดสอบระบบ)
- [MQTT Topics](#mqtt-topics)
- [Troubleshooting](#troubleshooting)

---

## 🎯 ภาพรวม

### สิ่งที่จะได้เรียนรู้

- ✅ ติดตั้ง Node-RED ด้วย Docker
- ✅ ตั้งค่า MQTT Broker ใน Node-RED (ไม่ต้อง container แยก)
- ✅ สร้าง Flow สำหรับดึงข้อมูล API
- ✅ ตั้งค่า Inject node ให้ทำงานทุก 1 นาที
- ✅ ส่งข้อมูลผ่าน MQTT protocol
- ✅ เพิ่ม location data สำหรับแผนที่ 3D

### Use Case

**Scenario:** ระบบ IoT ที่ต้องการจัดเตรียมข้อมูล network monitoring แบบ real-time พร้อม location data สำหรับ Platform 3D

```
LibreNMS API → Node-RED → MQTT Broker (Aedes) → 3D Platform (ทีมอื่น)
```

**ตัวอย่างการใช้งาน:**
- 🗺️ จัดเตรียมข้อมูลสถานะ network แบบ real-time
- 📍 เพิ่ม location data (lat, long, altitude) ให้อุปกรณ์
- 🚨 Alert system ที่ทำงานผ่าน MQTT
- 📊 ข้อมูลพร้อมใช้สำหรับ Dashboard visualization
- 💾 Data logging และ analytics

### 🎓 ส่วนหนึ่งของงานอบรม

คู่มือนี้เป็นส่วนหนึ่งของงานอบรม **"ปฏิวัติการ Monitor: เปลี่ยนข้อมูล Network ให้เห็นภาพบนแผนที่ 3 มิติ"**

🔗 รายละเอียดเพิ่มเติม: [https://wunca46.uni.net.th/workshop-detail/15](https://wunca46.uni.net.th/workshop-detail/15)

---

## 🏗️ Architecture

### System Overview

```
┌─────────────────────────────────────────────────────────────┐
│                    Docker Network                            │
│                                                              │
│  ┌──────────────┐      ┌────────────────────────────────┐  │
│  │  LibreNMS    │      │       Node-RED                 │  │
│  │  (API)       │◄─────┤  ┌──────────┐  ┌───────────┐  │  │
│  │  :8000       │ HTTP │  │ Flow     │  │   Aedes   │  │  │
│  └──────────────┘      │  │ Logic    │─►│   MQTT    │  │  │
│         │              │  └──────────┘  │  Broker   │  │  │
│         │              │                 └───────────┘  │  │
│         │              │      :1880           :1883     │  │
└─────────┼──────────────┴──────────┬────────────┬────────┴──┘
          │                         │            │
          ▼                         ▼            ▼
    API Requests             Web Interface  MQTT Clients
```

**หมายเหตุ:** MQTT Broker รันภายใน Node-RED container เดียวกัน ไม่ต้องรัน Mosquitto แยก

### Data Flow

```
[Timer: Every 30s]
        │
        ▼
[HTTP Request Node]
   GET /api/v0/ports?device_id=1&columns=port_id,ifName,ifOperStatus,...
        │
        ▼
[Function Node]
   - Parse JSON
   - Extract ether1 data
   - Add location data
   - Format message
        │
        ▼
[MQTT Output Node]
   Topic: mikrotik/ether1/status
   Payload: {"status": "up", "speed": 1000, "location": {...}}
        │
        ▼
[Aedes MQTT Broker]
   - Store & Forward
   - Publish to subscribers
        │
        ▼
[MQTT Subscribers]
   - 3D Platform
   - Dashboards
   - Other applications
```

---

## 🔑 ใส่ Credentials หลังเปิด Node-RED ครั้งแรก

repo นี้ **ไม่เก็บ** `flows_cred.json` และ `.config.runtime.json` (Node-RED สร้างไฟล์เหล่านี้ให้เองตอนเริ่มทำงาน)
หลัง `docker compose up -d` และ import flow แล้ว ให้ใส่ค่าของเครื่องตัวเอง:

1. ดับเบิลคลิก node **http request** ที่เรียก LibreNMS API → ใส่ API token ของ LibreNMS ที่สร้างใน Lab 4
2. ดับเบิลคลิก config node **mqtt-broker** → ใส่ username/password ถ้า broker ของคุณเปิด auth
3. กด **Deploy**

> ห้าม commit `nodered_data/flows_cred.json` หรือ `nodered_data/.config.runtime.json` คู่กัน
> เพราะไฟล์หลังมี `_credentialSecret` ที่ใช้ถอดรหัสไฟล์แรกได้

## 🔒 Security Setup (สำหรับ Production)

> ⚠️ **สำคัญ:** Configuration ที่อยู่ใน `docker-compose.yml` ออกแบบมาสำหรับ Workshop/การเรียนรู้เท่านั้น
>
> **ปัญหาด้านความปลอดภัย:**
> - ไม่มี Authentication สำหรับ Node-RED UI
> - MQTT Broker ไม่มี username/password
> - ไม่มี SSL/TLS encryption
> - Port เปิดให้ทุกคนเข้าถึงได้

### วิธีตั้งค่าที่ปลอดภัย

#### 1. สร้างไฟล์ .env

```bash
# Copy ไฟล์ตัวอย่าง
cp .env.example .env

# แก้ไขไฟล์ .env (ถ้าต้องการเปลี่ยน port)
nano .env
```

#### 2. ตั้งรหัสผ่านสำหรับ Node-RED UI

```bash
# เข้าไปใน container
docker exec -it nodered bash

# สร้าง password hash
node-red admin hash-pw

# คุณจะได้ hash เช่น: $2b$08$xyz...
# Copy hash นี้ไว้
```

แก้ไขไฟล์ `settings.js`:

```bash
# แก้ไขไฟล์
docker exec -it nodered vi /data/settings.js
```

เพิ่มส่วนนี้:

```javascript
adminAuth: {
    type: "credentials",
    users: [{
        username: "admin",
        password: "$2b$08$xyz...",  // paste hash ที่ได้
        permissions: "*"
    }]
}
```

Restart Node-RED:

```bash
docker restart nodered
```

#### 3. ตั้งค่า MQTT Authentication

เมื่อสร้าง Aedes broker node ใน Node-RED:

1. เปิด Aedes broker node settings
2. ไปที่แท็บ **Authenticate**
3. เพิ่ม Function สำหรับตรวจสอบ username/password:

```javascript
// Authenticate callback
function authenticate(client, username, password, callback) {
    // ตัวอย่างง่ายๆ (ควรใช้ database จริง)
    const validUsers = {
        'mqtt_user': 'secure_password_here'
    };

    const pwd = password.toString();
    if (validUsers[username] && validUsers[username] === pwd) {
        callback(null, true);
    } else {
        callback(null, false);
    }
}
```

4. Deploy flow

#### 4. เปิดใช้งาน SSL/TLS (แนะนำสำหรับ Production)

สร้าง certificate:

```bash
openssl req -x509 -newkey rsa:4096 \
  -keyout key.pem -out cert.pem \
  -days 365 -nodes
```

ใน Aedes node settings:
- เปิด **SSL/TLS**
- Upload `cert.pem` และ `key.pem`
- เปลี่ยน port เป็น 8883 (MQTTS)

#### 5. จำกัดการเข้าถึง

แก้ไขใน `.env`:

```bash
# จำกัดให้เข้าถึงเฉพาะ localhost
NODERED_PORT=127.0.0.1:1880
MQTT_PORT=127.0.0.1:1883
```

หรือใช้ Firewall:

```bash
# Ubuntu/Debian
sudo ufw allow from 192.168.56.0/24 to any port 1880
sudo ufw allow from 192.168.56.0/24 to any port 1883
```

### ✅ Security Checklist

ก่อนใช้งาน Production ตรวจสอบ:

- [ ] ตั้งรหัสผ่านสำหรับ Node-RED UI
- [ ] เปิดใช้งาน MQTT authentication
- [ ] พิจารณาใช้ SSL/TLS สำหรับ MQTT
- [ ] จำกัดการเข้าถึง port ด้วย Firewall
- [ ] สำรองข้อมูล flows และ settings
- [ ] อัปเดต Node-RED และ nodes เป็นเวอร์ชันล่าสุด

### 🎓 สำหรับ Workshop

**ไม่ต้องกังวล!** สำหรับการเรียนรู้ คุณสามารถข้ามขั้นตอนด้านบนและใช้งานได้เลยโดยไม่ต้องตั้ง authentication

---

## 🚀 ติดตั้ง Node-RED

### Step 1: เตรียม Environment

```bash
# เข้าโฟลเดอร์
cd nodered

# สร้าง directory สำหรับ Node-RED data
mkdir -p nodered_data
```

### Step 2: สร้าง Docker Network (ถ้ายังไม่มี)

```bash
# สร้าง Docker network เดียวกับ LibreNMS
docker network create monitoring_network

# หรือถ้ามีอยู่แล้ว ให้ LibreNMS join network นี้
docker network connect monitoring_network librenms
```

### Step 3: Start Node-RED

```bash
# Start Node-RED container
docker-compose up -d

# ตรวจสอบสถานะ
docker-compose ps
```

**Expected Output:**
```
NAME        IMAGE                      STATUS      PORTS
nodered     nodered/node-red:latest    Up          0.0.0.0:1880->1880/tcp, 0.0.0.0:1883->1883/tcp
```

### Step 4: เข้าใช้งาน Node-RED

เปิดเว็บเบราว์เซอร์:
```
http://localhost:1880
```

คุณจะเห็น Node-RED Editor interface!

---

## 🔧 ตั้งค่า MQTT Broker

ใช้ **Aedes MQTT Broker** ซึ่งรันภายใน Node-RED โดยไม่ต้อง container แยก

### Step 1: ติดตั้ง Aedes Node

1. ใน Node-RED คลิก **Menu (≡)** → **Manage palette**
2. ไปที่แท็บ **Install**
3. ค้นหา `node-red-contrib-aedes`
4. คลิก **Install**
5. คลิก **Install** อีกครั้งเพื่อยืนยัน
6. รอจนการติดตั้งเสร็จ (ประมาณ 30 วินาที)

### Step 2: เพิ่ม Aedes Broker Node

1. หลังจากติดตั้งเสร็จ ใน **Node Palette** (ซ้ายมือ) จะเห็น node ใหม่ชื่อ **aedes broker**
2. ลาก **aedes broker** node มาวางใน workspace
3. Double-click เพื่อตั้งค่า:
   - **Name:** `MQTT Broker`
   - **Port:** `1883`
   - **Options:**
     - ✅ เปิด **Publish persistent**
   - คลิก **Done**

### Step 3: Deploy MQTT Broker

1. คลิกปุ่ม **Deploy** (มุมบนขวา)
2. MQTT Broker จะเริ่มทำงานทันที

**หมายเหตุ:** Aedes broker node ไม่ต้องต่อกับ node อื่น สามารถวางไว้แยกในมุมของ workspace ได้

**✅ ตอนนี้คุณมี MQTT Broker ที่รันใน Node-RED แล้ว!**

---

## 🔄 ตั้งค่า Flow

### Flow Overview

Flow ที่จะสร้างประกอบด้วย:
1. **Inject Node** - Trigger ทุก 1 นาที
2. **HTTP Request Node** - เรียก LibreNMS API
3. **Function Node** - ประมวลผล JSON
4. **MQTT Output Node** - ส่งข้อมูลไปยัง MQTT broker
5. **Debug Node** - แสดงผลใน Debug panel

### Step 1: เพิ่ม Inject Node

1. ลาก **inject** node จาก palette มาวาง
2. Double-click เพื่อตั้งค่า:
   - **Name:** `Every 30 seconds`
   - **Repeat:** `interval`
   - **Every:** `30` `seconds`
   - คลิก **Done**

### Step 2: เพิ่ม HTTP Request Node

1. ลาก **http request** node มาวาง
2. Double-click เพื่อตั้งค่า:
   - **Name:** `Get mikrotik status`
   - **Method:** `GET`
   - **URL:** `http://librenms:8000/api/v0/ports?device_id=1&columns=port_id,ifName,ifOperStatus,ifAdminStatus,ifSpeed,ifMtu,ifPhysAddress,ifInOctets,ifOutOctets,ifInUcastPkts,ifOutUcastPkts,ifInErrors,ifOutErrors`
   - **Headers:** คลิก **+ add** เพื่อเพิ่ม header
     - **Name:** `X-Auth-Token`
     - **Value:** `your-api-token-here` (ใส่ API Token จริง)
   - **Return:** `a parsed JSON object`
   - คลิก **Done**

**หมายเหตุ:**
- ใช้ `librenms` แทน `localhost` เพราะอยู่ใน Docker network เดียวกัน
- ⚠️ **สำคัญ:** ต้องใช้ `/api/v0/ports?device_id=1&columns=...` พร้อมระบุ columns ทั้งหมด
- ❌ อย่าใช้ `/api/v0/devices/1/ports` (จะได้ข้อมูลไม่ครบ)

### Step 3: เพิ่ม Function Node

1. ลาก **function** node มาวาง
2. Double-click เพื่อตั้งค่า:
   - **Name:** `Extract ether1 data`
   - **Function:** ใส่ code นี้:

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

### Step 4: เพิ่ม MQTT Output Node

1. ลาก **mqtt out** node มาวาง
2. Double-click เพื่อตั้งค่า:
   - **Server:** คลิก pencil icon เพื่อเพิ่ม broker
     - **Server:** `localhost` (เพราะ Aedes broker รันใน container เดียวกัน)
     - **Port:** `1883`
     - **Client ID:** ปล่อยว่าง (auto-generate)
     - คลิก **Add**
   - **Topic:** ปล่อยว่าง (ใช้จาก msg.topic)
   - **QoS:** `0`
   - **Retain:** ✅ เปิดเพื่อเก็บ last message
   - **Name:** `Publish to MQTT`
   - คลิก **Done**

### Step 5: เพิ่ม Debug Node (Optional)

1. ลาก **debug** node มาวาง
2. ต่อจาก Function node (ก่อน MQTT out)
3. Double-click:
   - **Output:** `complete msg object`
   - **Name:** `Debug output`
   - คลิก **Done**

### Step 6: เชื่อมต่อ Nodes

เชื่อมต่อ nodes ตามลำดับ:

```
[Inject] → [HTTP Request] → [Function] → [MQTT Out]
                                   ↓
                              [Debug]
```

1. คลิกที่ output port (จุดขวา) ของ Inject node
2. ลากไปที่ input port (จุดซ้าย) ของ HTTP Request node
3. ทำแบบเดียวกันสำหรับ nodes อื่นๆ

### Step 7: Deploy Flow

1. คลิกปุ่ม **Deploy** (มุมบนขวา)
2. เลือก **Full** (deploy ทั้งหมด)
3. คลิก **Deploy**

**ถ้าสำเร็จ:** จะเห็นข้อความ "Successfully deployed"

---

## 🧪 ทดสอบระบบ

### 1. ทดสอบ Flow

1. คลิกที่ปุ่ม (square) ทางซ้ายของ Inject node เพื่อทดสอบทันที
2. ดูผลลัพธ์ใน **Debug** panel (ด้านขวา - คลิก bug icon)

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
  "macAddress": "00:11:22:33:44:aa",
  "statistics": {
    "inOctets": 1234567,
    "outOctets": 987654,
    "inPackets": 10000,
    "outPackets": 8000,
    "inErrors": 0,
    "outErrors": 0
  }
}
```

### 2. ทดสอบ MQTT Broker

#### วิธีที่ 1: ใช้ MQTT In Node ใน Node-RED

1. ลาก **mqtt in** node มาวาง
2. Double-click:
   - **Server:** เลือก broker เดียวกับที่ตั้งไว้ (`localhost:1883`)
   - **Topic:** `mikrotik/ether1/status`
   - **QoS:** `0`
   - คลิก **Done**
3. ลาก **debug** node มาต่อ
4. Deploy
5. คลิกปุ่มทดสอบที่ Inject node
6. ดู debug output จาก mqtt in node

#### วิธีที่ 2: ใช้ mosquitto-clients ภายนอก

```bash
# Subscribe to topic (จาก host machine)
mosquitto_sub -h localhost -t "mikrotik/ether1/status" -v

# คลิกปุ่มทดสอบใน Node-RED
# ควรเห็นข้อมูลปรากฏใน terminal
```

**Expected Output:**
```
mikrotik/ether1/status {"timestamp":"2026-02-09T15:30:00.000Z","interface":"ether1",...}
```

### 3. ทดสอบ Auto-Trigger

รอ 1 นาที และตรวจสอบว่า Flow ทำงานอัตโนมัติ:

```bash
# Monitor MQTT messages
mosquitto_sub -h localhost -t "mikrotik/#" -v

# ควรเห็นข้อมูลเข้ามาทุก 1 นาที
```

---

## 📡 MQTT Topics

### Topic Structure

```
mikrotik/
├── ether1/
│   ├── status          # สถานะและข้อมูลทั้งหมด
│   ├── uptime          # (optional) uptime
│   └── alerts          # (optional) alerts
├── ether2/
│   └── status
└── device/
    └── info            # (optional) device information
```

### Topic: `mikrotik/ether1/status`

**Payload Format:**
```json
{
  "timestamp": "2026-02-09T15:30:00.000Z",
  "device": {
    "ip": "192.168.56.10",
    "name": "MikroTik-Router",
    "location": {
      "lat": 16.4322,        // Latitude (WGS84)
      "long": 102.8236,      // Longitude (WGS84)
      "altitude": 170        // ความสูงจากระดับน้ำทะเล (เมตร)
    }
  },
  "interface": "ether1",
  "status": "up",
  "adminStatus": "up",
  "speed": 1000,
  "mtu": 1500,
  "macAddress": "00:11:22:33:44:aa",
  "statistics": {
    "inOctets": 1234567,
    "outOctets": 987654,
    "inPackets": 10000,
    "outPackets": 8000,
    "inErrors": 0,
    "outErrors": 0
  }
}
```

**การใช้งานกับแผนที่ 3D:**
- `device.location.lat` - Latitude (ละติจูด) ใช้ระบบพิกัด WGS84
- `device.location.long` - Longitude (ลองจิจูด) ใช้ระบบพิกัด WGS84
- `device.location.altitude` - ความสูงจากระดับน้ำทะเล (เมตร)

**ตัวอย่างตำแหน่ง:**
- มหาวิทยาลัยขอนแก่น: `lat: 16.4322, long: 102.8236, altitude: 170`
- กรุงเทพฯ (เสาชิงช้า): `lat: 13.7563, long: 100.5018, altitude: 15`
- เชียงใหม่ (ดอยสุเทพ): `lat: 18.8042, long: 98.9219, altitude: 1073`

**💡 Tips:**
- หาพิกัดจาก Google Maps: คลิกขวา → เลือก "What's here?"
- ใช้ GPS app บนมือถือเพื่อหาพิกัดแม่นยำ
- Altitude สามารถดูจาก Google Earth

### Subscribe ใน MQTT Client

```bash
# ติดตั้ง mosquitto-clients (ถ้ายังไม่มี)
# Ubuntu/Debian
sudo apt-get install mosquitto-clients

# macOS
brew install mosquitto

# Windows: Download from mosquitto.org

# Subscribe to topic
mosquitto_sub -h localhost -t "mikrotik/ether1/status" -v

# Subscribe to all mikrotik topics
mosquitto_sub -h localhost -t "mikrotik/#" -v
```

---

## 🔄 Advanced Flows

### Flow 1: Alert when ether1 is down

เพิ่ม **switch** node หลัง Function node:

```javascript
// Switch node condition
if (msg.payload.status === 'down') {
    return [msg, null]; // Output 1: Alert
} else {
    return [null, msg]; // Output 2: Normal
}
```

จากนั้นต่อ output 1 ไป MQTT node ที่ส่งไป topic `mikrotik/alerts/ether1`

### Flow 2: Calculate bandwidth usage

```javascript
// Function node
const prevOctets = context.get('prevOctets') || {};
const prevTime = context.get('prevTime') || Date.now();

const ports = msg.payload.ports;
const ether1 = ports.find(p => p.ifName === 'ether1');

if (!ether1) return null;

const currentTime = Date.now();
const timeDiff = (currentTime - prevTime) / 1000; // seconds

let bandwidth = null;

if (prevOctets.in !== undefined) {
    const inDiff = ether1.ifInOctets - prevOctets.in;
    const outDiff = ether1.ifOutOctets - prevOctets.out;

    bandwidth = {
        inMbps: (inDiff * 8 / timeDiff / 1000000).toFixed(2),
        outMbps: (outDiff * 8 / timeDiff / 1000000).toFixed(2)
    };
}

// Store current values
context.set('prevOctets', {
    in: ether1.ifInOctets,
    out: ether1.ifOutOctets
});
context.set('prevTime', currentTime);

if (bandwidth) {
    msg.payload = {
        timestamp: new Date().toISOString(),
        interface: 'ether1',
        bandwidth: bandwidth
    };
    msg.topic = 'mikrotik/ether1/bandwidth';
    return msg;
}

return null;
```

### Flow 3: Multiple devices monitoring

```javascript
// Function node
const devices = [
    { ip: '192.168.56.10', name: 'MikroTik-1' },
    { ip: '192.168.56.11', name: 'MikroTik-2' }
];

const messages = [];

devices.forEach(device => {
    messages.push({
        url: `http://librenms:8000/api/v0/devices/${device.ip}/ports`,
        headers: {
            'X-Auth-Token': 'your-token-here'
        },
        deviceName: device.name
    });
});

return [messages]; // Send array of messages
```

---

## 🐛 Troubleshooting

### ปัญหา: ได้ข้อมูล undefined หรือ null

**อาการ:**
- Debug output แสดง `ifOperStatus: undefined`
- หรือ `✅ ether1 found: undefined`

**สาเหตุ:**
- ใช้ API endpoint ผิด `/api/v0/devices/1/ports` (ได้ข้อมูลไม่ครบ)
- ไม่ได้ระบุ `columns` parameter

**แก้ไข:**
เปลี่ยนไปใช้ endpoint ที่ถูกต้อง:
```
http://librenms:8000/api/v0/ports?device_id=1&columns=port_id,ifName,ifOperStatus,ifAdminStatus,ifSpeed,ifMtu,ifPhysAddress,ifInOctets,ifOutOctets,ifInUcastPkts,ifOutUcastPkts,ifInErrors,ifOutErrors
```

**เหตุผล:**
- `/api/v0/devices/{id}/ports` = ข้อมูลพื้นฐานเท่านั้น (port_id, ifName)
- `/api/v0/ports?device_id={id}&columns=...` = ข้อมูลครบตาม columns ที่ระบุ

---

### ปัญหา: Node-RED ไม่สามารถเชื่อมต่อ LibreNMS

**อาการ:**
```
Error: getaddrinfo ENOTFOUND librenms
```

**แก้ไข:**
1. ตรวจสอบว่าทั้ง 2 containers อยู่ใน network เดียวกัน:
   ```bash
   docker network inspect monitoring_network
   ```

2. ถ้าไม่ได้อยู่ใน network เดียวกัน:
   ```bash
   docker network connect monitoring_network librenms
   docker network connect monitoring_network nodered
   ```

3. Restart Node-RED:
   ```bash
   docker-compose restart
   ```

### ปัญหา: Aedes broker ไม่ทำงาน

**อาการ:**
- MQTT out node แสดง status "disconnected"
- ไม่สามารถ publish message ได้

**แก้ไข:**
1. ตรวจสอบว่า Aedes broker node ถูก deploy แล้ว
2. ตรวจสอบ port 1883 ไม่ถูกใช้งานโดยโปรแกรมอื่น:
   ```bash
   # Windows
   netstat -ano | findstr :1883

   # Linux/Mac
   lsof -i :1883
   ```
3. ตรวจสอบ MQTT out node config:
   - Server: `localhost`
   - Port: `1883`
4. Restart Node-RED container:
   ```bash
   docker-compose restart
   ```

### ปัญหา: HTTP Request returns 401

**อาการ:**
```
Error: Unauthorized (401)
```

**แก้ไข:**
1. ตรวจสอบ API Token ถูกต้องหรือไม่
2. ตรวจสอบ Header format:
   ```
   X-Auth-Token: your-actual-token
   ```
3. สร้าง Token ใหม่ใน LibreNMS:
   - เข้า Web UI → My Settings → API Settings
   - หรือใช้ command:
   ```bash
   docker exec librenms lnms user:add-token admin
   ```

---

### ปัญหา: HTTP Request returns 404

**อาการ:**
```
Error: Not Found (404)
```

**สาเหตุ:** ใช้ API endpoint ผิด

**แก้ไข:**
เปลี่ยนจาก `/api/v0/device/1/ports` (ไม่มี s) → `/api/v0/devices/1/ports` (มี s)

หรือใช้ endpoint ที่แนะนำ:
```
/api/v0/ports?device_id=1&columns=...
```

### ปัญหา: No data in MQTT

**แก้ไข:**
1. ตรวจสอบ Debug node output
2. ตรวจสอบ Function node ทำงานถูกต้อง (คลิก debug ที่ Function node)
3. ตรวจสอบ Aedes broker status (ควรแสดง "connected")
4. ใช้ mqtt in node เพื่อ subscribe และดูว่ามีข้อมูลหรือไม่

### ปัญหา: Cannot subscribe from external client

**อาการ:**
```bash
mosquitto_sub -h localhost -t "test" -v
# Connection refused
```

**แก้ไข:**
1. ตรวจสอบว่า port 1883 exposed ใน docker-compose.yml:
   ```yaml
   ports:
     - "1883:1883"
   ```
2. Restart Node-RED:
   ```bash
   docker-compose restart
   ```
3. ตรวจสอบ firewall:
   ```bash
   # Allow port 1883
   sudo ufw allow 1883/tcp
   ```

---

## 📚 Export/Import Flow

### Export Flow

1. เลือก nodes ที่ต้องการ (Ctrl+A เพื่อเลือกทั้งหมด)
2. **Menu (≡)** → **Export**
3. เลือก **Selected nodes** หรือ **Current flow**
4. คลิก **Copy to clipboard** หรือ **Download**

### Import Flow

1. **Menu (≡)** → **Import**
2. Paste JSON หรือเลือกไฟล์
3. คลิก **Import**

### Example Flow JSON

ดูไฟล์ `flow-example.json` ในโฟลเดอร์นี้

---

## 🎯 Best Practices

### 1. Security

**✅ DO:**
- ใช้ API Token แทน username/password
- จำกัด network access (ใช้ Docker network)
- เปลี่ยน API token เป็นระยะ
- ใช้ TLS/SSL ใน production

**❌ DON'T:**
- Hard-code credentials ใน Flow
- เปิด MQTT port ออก Internet โดยไม่มี authentication
- ใช้ `allow_anonymous` ใน production

### 2. Performance

- ใช้ QoS 0 สำหรับ non-critical messages
- Enable MQTT retain สำหรับ status messages
- Monitor Node-RED memory usage
- ใช้ context storage สำหรับ state management

### 3. Monitoring

- เปิด Debug nodes ในระหว่างพัฒนา
- ปิด Debug nodes ใน production
- Monitor container logs:
  ```bash
  docker-compose logs -f nodered
  ```
- ตั้งค่า alerts สำหรับ Flow errors

---

## 📖 Resources

### Official Documentation
- [Node-RED Docs](https://nodered.org/docs/)
- [Aedes MQTT Broker](https://github.com/moscajs/aedes)
- [MQTT Protocol](https://mqtt.org/)
- [LibreNMS API](https://docs.librenms.org/API/)

### Node-RED Flows
- [Node-RED Flow Library](https://flows.nodered.org/)
- [Aedes Examples](https://flows.nodered.org/?term=aedes)

### Tools
- [MQTT Explorer](http://mqtt-explorer.com/) - GUI MQTT client
- [MQTTX](https://mqttx.app/) - Modern MQTT client

---

## 📝 Summary

### 🎯 ความสำเร็จของคุณ

คุณได้เรียนรู้การ**ปฏิวัติการ Monitor** แล้ว:
- ✅ ติดตั้ง Node-RED ด้วย Docker (single container)
- ✅ ตั้งค่า MQTT Broker ใน Node-RED (Aedes)
- ✅ สร้าง Flow สำหรับดึงข้อมูล API แบบ real-time
- ✅ เพิ่ม location data (lat, long, altitude) สำหรับแผนที่ 3D
- ✅ ส่งข้อมูลผ่าน MQTT protocol
- ✅ Subscribe และรับข้อมูลจาก MQTT
- ✅ ข้อมูลพร้อมใช้สำหรับ Platform 3D

### 🗺️ Data Pipeline ที่คุณสร้างเสร็จแล้ว

**จากข้อมูล Network นามธรรม → ข้อมูลพร้อมใช้สำหรับ 3D Platform**

```
LibreNMS (Monitor) → API → Node-RED → MQTT → พร้อมใช้
     ↓                ↓         ↓         ↓         ↓
  ตัวเลข          ดึงข้อมูล   ประมวลผล   ส่งข้อมูล   Data Ready
```

**ข้อมูลที่พร้อมใช้:**
- 📍 Location data พร้อมพิกัดและความสูง
- 🎨 สถานะ real-time (Up/Down) พร้อมใช้
- 📊 Network data structure ที่เป็นมาตรฐาน
- 🚨 Real-time data stream ผ่าน MQTT

### ⚡ ข้อดีของ Architecture นี้

- 🎯 **Simple:** ใช้ container เดียว (ง่ายต่อการจัดการ)
- ⚡ **Fast:** Performance ดี (ไม่ต้องข้าม network)
- 💾 **Lightweight:** ใช้ resource น้อย (ไม่มี Mosquitto container)
- 🔄 **Flexible:** ปรับเปลี่ยน Flow ได้ง่าย
- 🗺️ **Data Ready:** ข้อมูลพร้อมใช้สำหรับ Platform 3D ต่าง ๆ

---

### 🎓 ขอบคุณที่เข้าร่วมงานอบรม

**ปฏิวัติการ Monitor: เปลี่ยนข้อมูล Network ให้เห็นภาพบนแผนที่ 3 มิติ (W013)**

🔗 [wunca46.uni.net.th/workshop-detail/15](https://wunca46.uni.net.th/workshop-detail/15)

---

**Happy Data Preparing! 🚀📊**

*Last Updated: 2026-02-09*
