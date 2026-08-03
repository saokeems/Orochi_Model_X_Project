# Use Case Diagram: Digi9Craft

วิเคราะห์จาก Business Model Canvas เพื่อแปลงเป็น Use Case Diagram สำหรับระบบ Digi9Craft

## 1. ผู้ใช้งานระบบ (Actors)
1. **Customer (ลูกค้า):** สถานศึกษา, ลูกค้าที่ต้องการเสื้อตามเทศกาล, ลูกค้างาน Custom, ลูกค้า E-book, ลูกค้าองค์กร
2. **Admin/Team (ทีมงาน Digi9Craft):** ทีมงานที่ดูแลระบบ, อัปเดตสินค้า, ทำการตลาด, และประสานงาน
3. **Partner (พาร์ทเนอร์/พันธมิตร):** ร้านรับสกรีน, ร้านทำของที่ระลึก, นักออกแบบ

## 2. ยูสเคสหลัก (Key Use Cases)

### ส่วนของ Customer (ลูกค้า)
* **UC01: เลือกซื้อสินค้าดิจิทัล** (Browse & Buy Digital Products เช่น E-book, เทมเพลต)
* **UC02: สั่งทำงานออกแบบเฉพาะ** (Order Custom Design สำหรับลายเสื้อ หรือของที่ระลึก)
* **UC03: ชำระเงิน** (Payment - รองรับการซื้อขาด และการจ่ายค่าบริการ)
* **UC04: ดาวน์โหลดไฟล์ทันที** (Instant Download สำหรับสินค้าดิจิทัลหลังชำระเงิน)
* **UC05: ติดต่อสอบถาม/ให้ข้อเสนอแนะ** (Contact & Feedback ผ่าน Line OA, Email)

### ส่วนของ Admin/Team (ทีมงาน)
* **UC06: จัดการและอัปเดตสินค้าดิจิทัล** (Manage & Update Digital Products)
* **UC07: รับบรีฟและจัดการงาน Custom** (Manage Custom Orders)
* **UC08: ประสานงานกับพาร์ทเนอร์** (Coordinate with Partners สำหรับงานผลิต)
* **UC09: วิเคราะห์ข้อมูลผู้ใช้** (Analyze User Data สำหรับปรับปรุงบริการ)

### ส่วนของ Partner (พันธมิตร)
* **UC10: รับออเดอร์ผลิต** (Receive Production Orders)
* **UC11: แจ้งสถานะการผลิต/ส่งมอบ** (Update Production Status)

## 3. แผนภาพ Use Case (Mermaid Diagram)

```mermaid
flowchart LR
    %% กำหนดรูปแบบ Node
    classDef actor fill:#f9f9f9,stroke:#333,stroke-width:2px;
    classDef usecase fill:#e1f5fe,stroke:#0288d1,stroke-width:2px,rx:20,ry:20;

    %% Actors
    Customer(["🧑‍💻 Customer (ลูกค้า)"]):::actor
    Admin(["🛠️ Admin (ทีมงาน)"]):::actor
    Partner(["🤝 Partner (พันธมิตร)"]):::actor

    %% System Boundary
    subgraph Digi9Craft["ระบบ Digi9Craft (Digital Product & Custom Service)"]
        direction TB
        UC01(["UC01: เลือกซื้อสินค้าดิจิทัล"]):::usecase
        UC02(["UC02: สั่งทำงานออกแบบเฉพาะ"]):::usecase
        UC03(["UC03: ชำระเงิน"]):::usecase
        UC04(["UC04: ดาวน์โหลดไฟล์ทันที"]):::usecase
        UC05(["UC05: ติดต่อสอบถาม"]):::usecase
        
        UC06(["UC06: จัดการและอัปเดตสินค้า"]):::usecase
        UC07(["UC07: รับบรีฟและจัดการงาน Custom"]):::usecase
        UC08(["UC08: ประสานงานกับพาร์ทเนอร์"]):::usecase
        UC09(["UC09: วิเคราะห์ข้อมูลผู้ใช้"]):::usecase
        
        UC10(["UC10: รับออเดอร์ผลิตและส่งมอบ"]):::usecase
    end

    %% Relationships - Customer
    Customer --> UC01
    Customer --> UC02
    Customer --> UC03
    Customer --> UC04
    Customer --> UC05

    %% Dependencies (Includes)
    UC01 -.-> |"include"| UC03
    UC02 -.-> |"include"| UC03
    UC03 -.-> |"include"| UC04

    %% Relationships - Admin
    Admin --> UC06
    Admin --> UC07
    Admin --> UC08
    Admin --> UC09

    %% Relationships - Partner
    Partner --> UC10
    
    %% Cross interactions
    UC08 -.-> |"ส่งออเดอร์"| UC10
```
