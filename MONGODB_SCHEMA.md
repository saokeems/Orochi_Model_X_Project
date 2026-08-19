# MongoDB Schema Design: Digi9Craft

จากการออกแบบ ER Diagram แบบ Relational Database สามารถนำมาปรับใช้กับ MongoDB ซึ่งเป็น NoSQL Database (Document-oriented) ได้ โดยอาศัยหลักการ **Embedding (ฝังข้อมูล)** และ **Referencing (อ้างอิงข้อมูล)** เพื่อลดความซ้ำซ้อนและเพิ่มประสิทธิภาพในการคิวรี (Query)

หลักการที่ใช้ในการแปลงจาก ER Diagram เป็น MongoDB Schema:
1. **One-to-One (1:1) และ One-to-Few (1:N ที่ N ไม่เยอะ)**: แนะนำให้ใช้ **Embedding** เพื่อให้สามารถดึงข้อมูลได้ใน Query เดียว
2. **One-to-Many (1:N ที่ N เยอะมาก) หรือ Many-to-Many (M:N)**: แนะนำให้ใช้ **Referencing (อ้างอิง ObjectId)**

---

## สรุป Collection ทั้งหมด (MongoDB Collections)
เราสามารถรวบตารางบางส่วนจาก ER Diagram เข้าด้วยกันได้ ดังนี้:
1. `users` - เก็บข้อมูลผู้ใช้งาน
2. `products` - เก็บข้อมูลสินค้า
3. `orders` - เก็บข้อมูลคำสั่งซื้อ (รวม `Order_Item`, `Payment`, `Custom_Request` ไว้ใน Document เดียวกัน)
4. `partners` - เก็บข้อมูลพาร์ทเนอร์
5. `production_orders` - เก็บข้อมูลสั่งผลิต (แยกออกมาเพื่อความสะดวกในการจัดการของฝั่งพาร์ทเนอร์)
6. `feedbacks` - เก็บข้อมูล Feedback

---

## โครงสร้าง Schema (Mongoose/JSON Schema Format)

### 1. Collection: `users`
```json
{
  "_id": "ObjectId",
  "first_name": "String",
  "last_name": "String",
  "email": "String (Unique)",
  "phone": "String",
  "user_type": "String (Enum: ['customer', 'admin'])",
  "created_at": "Date"
}
```

### 2. Collection: `products`
```json
{
  "_id": "ObjectId",
  "name": "String",
  "description": "String",
  "product_type": "String (Enum: ['ebook', 'template', 'custom', 'souvenir'])",
  "price": "Number",
  "file_url": "String",
  "stock_status": "String (Enum: ['available', 'out_of_stock'])",
  "created_at": "Date",
  "admin_id": "ObjectId (Ref: 'users')"
}
```

### 3. Collection: `orders` 
*(ทำการ Embed `order_items`, `payment`, และ `custom_request` เข้ามาไว้ด้วยกันเลย เพราะข้อมูลเหล่านี้มักจะถูกเรียกใช้พร้อมกับ Order)*
```json
{
  "_id": "ObjectId",
  "user_id": "ObjectId (Ref: 'users')",
  "order_date": "Date",
  "total_amount": "Number",
  "order_status": "String (Enum: ['pending', 'confirmed', 'processing', 'completed', 'cancelled'])",
  "order_type": "String (Enum: ['digital_purchase', 'custom_order'])",
  
  // Embedding: Order_Item (1:N - มีได้หลายสินค้าใน 1 ออเดอร์)
  "items": [
    {
      "product_id": "ObjectId (Ref: 'products')",
      "quantity": "Number",
      "unit_price": "Number"
    }
  ],
  
  // Embedding: Payment (1:1 - การชำระเงินของออเดอร์นี้)
  "payment": {
    "amount": "Number",
    "payment_method": "String (Enum: ['credit_card', 'promptpay', 'bank_transfer'])",
    "payment_status": "String (Enum: ['pending', 'success', 'failed'])",
    "payment_date": "Date",
    "revenue_type": "String (Enum: ['one_time', 'service_fee', 'commission', 'subscription'])"
  },
  
  // Embedding: Custom_Request (1:1 - หากออเดอร์นี้เป็นงาน Custom)
  "custom_request": {
    "brief_detail": "String",
    "design_type": "String (Enum: ['shirt_design', 'souvenir', 'template', 'other'])",
    "request_status": "String (Enum: ['received', 'designing', 'review', 'approved', 'sent_to_partner'])",
    "deadline": "Date"
  }
}
```

### 4. Collection: `partners`
```json
{
  "_id": "ObjectId",
  "name": "String",
  "contact_info": "String",
  "partner_type": "String (Enum: ['screen_shop', 'souvenir_shop', 'designer', 'cms'])",
  "status": "String (Enum: ['active', 'inactive'])"
}
```

### 5. Collection: `production_orders`
*(มีการอ้างอิงถึง `order_id` และ `partner_id`)*
```json
{
  "_id": "ObjectId",
  "order_id": "ObjectId (Ref: 'orders')", 
  "partner_id": "ObjectId (Ref: 'partners')",
  "production_status": "String (Enum: ['sent', 'in_production', 'completed', 'delivered'])",
  "commission_amount": "Number",
  "sent_date": "Date",
  "completed_date": "Date"
}
```

### 6. Collection: `feedbacks`
```json
{
  "_id": "ObjectId",
  "user_id": "ObjectId (Ref: 'users')",
  "order_id": "ObjectId (Ref: 'orders') (Nullable)",
  "channel": "String (Enum: ['line_oa', 'email', 'social_media', 'website'])",
  "message": "String",
  "created_at": "Date"
}
```
