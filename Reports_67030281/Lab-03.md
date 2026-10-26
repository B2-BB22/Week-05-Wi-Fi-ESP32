## 6. ตารางบันทึกผลการทดลอง (Experiment Results)

ให้นักศึกษาบันทึกผลลัพธ์จากการสังเกตใน Serial Console ลงในตารางต่อไปนี้:

### 6.1 ตารางสรุปเปรียบเทียบผลการทดลองในระดับ Link Layer

| ข้อการทดลอง | สถานการณ์ทดสอบ | Event ที่ได้รับ | ผลการผูกสัมพันธ์ Link Layer | ค่า Association ID (AID) ที่ได้ | Reason Code (ถ้ามี) |
| :---: | :--- | :---: | :---: | :---: | :--- |
| **5.3.1** | ร้องขอ Auth & Assoc กับ AP มีอยู่จริง | WIFI_EVENT_STA_CONNECTED  | สำเร็จ  | 42150 | - |
| **5.3.2** | ร้องขอ Auth & Assoc กับ AP ไม่มีอยู่จริง | WIFI_EVENT_STA_DISCONNECTED | ล้มเหลว | - | 201 / 0xC9 (WIFI_REASON_NO_AP_FOUND) |

### 6.2 บันทึกข้อมูล Link Layer จาก Event `WIFI_EVENT_STA_CONNECTED` (ข้อ 5.3.1)

| พารามิเตอร์ Link Layer | ค่าที่อ่านได้จริงจาก Forensic Log |
| :--- | :--- |
| **SSID** | Boll0 |
| **BSSID (MAC Address)** | 88:36:6C:10:4A:F5 |
| **Channel** | 6 |
| **Auth Mode Enum** | 3 |
| **Association ID (AID)** | 34680  |

---

## 7. คำถามท้ายการทดลอง (Post-Lab Questions)

1. **Association ID (AID)** คืออะไร มีบทบาทอย่างไรใน Phase 3 และส่งคืนมาในโครงสร้างข้อมูลตัวแปรใด?
```
Association ID คือหมายเลขประจำตัวที่ Access Point กำหนดให้กับ Station แต่ละเครื่องเมื่อทำการ Association สำเร็จ เพื่อใช้ระบุตัวตนและจัดการการส่งข้อมูลภายในเครือข่าย BSS โดยค่านี้จะถูกส่งคืนมาในสมาชิกตัวแปรชื่อ aid ของโครงสร้างข้อมูล
```
2. เหตุใดการเชื่อมต่อ Wi-Fi ความปลอดภัยแบบ WPA2-PSK จึงสามารถผ่าน Phase 2 (Authentication) และ Phase 3 (Association) จนเกิด Event `WIFI_EVENT_STA_CONNECTED` ได้สำเร็จ แม้ผู้ใช้จะป้อนรหัสผ่าน (Password) ผิด?
```
เพราะในช่วง Phase 2 และ 3 จะเป็นการยืนยันตัวตนแบบ Open System Authentication และเชื่อมต่อระดับ Link-Layer เบื้องต้น ซึ่งยังไม่มีการตรวจสอบรหัสผ่าน การตรวจสอบรหัสผ่านที่แท้จริงจะไปเกิดขึ้นในขั้นตอนการเข้ารหัส (4-Way Handshake) ภายหลัง อุปกรณ์จึงสามารถผ่านเฟสเหล่านี้จนเกิด Event เชื่อมต่อสำเร็จได้แม้รหัสจะผิด
```
3. หาก Router มีการตั้งค่า **MAC Address Filtering** (อนุญาตเฉพาะ MAC ที่ลงทะเบียน) ESP32 จะล้มเหลวในเฟสใด และจะส่ง Disconnect Reason Code ใดออกมา?
```
ESP32 จะล้มเหลวตั้งแต่ เฟส 2 (Authentication) ทันที เนื่องจาก AP จะตรวจสอบ MAC Address และปฏิเสธการยืนยันตัวตน โดยมักจะส่ง Disconnect Reason Code ออกมาเป็น WIFI_REASON_AUTH_FAIL
```
4. สรุปความแตกต่างสำคัญระหว่างจุดสิ้นสุดของ **Phase 3 (Link-Layer Connected)** กับจุดสิ้นสุดของ **Phase 5 (IP Address Assigned)**
```
จุดสิ้นสุด Phase 3: เกิด Event WIFI_EVENT_STA_CONNECTED หมายถึงอุปกรณ์เชื่อมต่อคลื่นวิทยุและเป็นส่วนหนึ่งของเครือข่ายไร้สายสำเร็จแล้ว แต่ยังไม่สามารถรับส่งข้อมูลอินเทอร์เน็ตได้

จุดสิ้นสุด Phase 5: เกิด Event IP_EVENT_STA_GOT_IP หมายถึงอุปกรณ์ได้รับการแจกหมายเลข IP จาก DHCP Server เรียบร้อยแล้ว พร้อมใช้งานเครือข่ายและอินเทอร์เน็ตอย่างสมบูรณ์
```