Computer Network Laboratory Experiments

เอกสารสรุปหัวข้อการทดลองและการตั้งค่าระบบเครือข่ายคอมพิวเตอร์ (Network Lab Exercises)

1. DHCP (Dynamic Host Configuration Protocol)[cite: 1]
- วัตถุประสงค์: ศึกษาและจำลองการแจกจ่ายการตั้งค่าเครือข่ายอัตโนมัติให้แก่โฮสต์
- ขอบเขตการทดลอง:
  การตั้งค่า DHCP Pool, Default Gateway และ DNS Server บนเราเตอร์หรือสวิตช์ L3
  การกำหนด Address Exclusions (IP ที่สงวนไว้)
  การตรวจสอบสถานะการรับ IP Address และ Lease Time ของเครื่องลูกข่าย

2. Inter-VLAN Routing[cite: 1]
- วัตถุประสงค์: ศึกษาการแบ่งกลุ่มเครือข่ายเสมือน (VLAN) และการเชื่อมต่อสื่อสารข้าม VLAN
- ขอบเขตการทดลอง:
  การสร้างและแบ่งกลุ่ม VLAN บน Layer 2 Switch
  การตั้งค่าพอร์ต Access และ Trunk (IEEE 802.1Q)
  การกำหนดเส้นทางข้าม VLAN แบบ Router-on-a-Stick (Sub-interfaces) หรือ Switch Layer 3 (SVI)
  การทดสอบการเชื่อมต่อ (Ping / Traceroute) ระหว่าง VLAN ต่างกลุ่ม

3. NAT (Network Address Translation)[cite: 1]
- วัตถุประสงค์: ศึกษาการแปลงแอดเดรสระหว่าง Private IP ภายในและ Public IP ภายนอก
- ขอบเขตการทดลอง:
  การตั้งค่า Static NAT, Dynamic NAT และ PAT (NAT Overload)
  การกำหนดทิศทางอินเทอร์เฟซ Inside และ Outside
  การใช้ Access Control List (ACL) ควบคุมทราฟฟิกที่จะทำ NAT
  การตรวจสอบตารางแปลงแอดเดรสด้วยคำสั่ง show ip nat translations

4. OSPF (Open Shortest Path First)[cite: 1]
- วัตถุประสงค์: ศึกษาการทำงานของโปรโตคอลการกำหนดเส้นทางแบบ Dynamic Routing ชนิด Link-State
- ขอบเขตการทดลอง:
  การเปิดใช้งาน OSPF และการประกาศเครือข่าย (Network Statement / Wildcard Mask)
  การกำหนดค่า Single-Area OSPF (Area 0) และการตั้งค่า Router ID
  การสร้างความสัมพันธ์กับเราเตอร์เพื่อนบ้าน (Neighbor Adjacency)
  การตรวจสอบตารางเส้นทาง (Routing Table) และค่า Cost ของเส้นทาง

ซอฟต์แวร์และเครื่องมือที่ใช้
Cisco Packet Tracer 