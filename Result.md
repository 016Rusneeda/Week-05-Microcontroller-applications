### คำถามทบทวน

1. **Docker Commands**: คำสั่ง `docker-compose up -d` และ `docker-compose exec esp32-dev bash` ทำอะไร?
   
   ตอบ :
   - `docker-compose up -d` คำสั่งนี้จะทำการสร้างและเริ่ม container ตามที่กำหนดไว้ในไฟล์ docker-compose.yml โดยที่:
   
       `up`: เป็นคำสั่งที่ใช้ในการสร้าง (create) และเริ่มต้น (start) services ใน Docker Compose

       `-d` (detached mode): เป็น flag ที่บอกให้ Docker รัน container ใน background โดยไม่ผูกติดกับ terminal ที่เราใช้งานอยู่ ทำให้เรายังสามารถใช้ terminal นั้นได้ต่อไปในขณะที่ container ทำงานอยู่

   - `docker-compose exec esp32-dev bash` ใช้สำหรับ เข้าถึง และ รันคำสั่ง ภายใน container ที่กำลังทำงานอยู่เพื่อให้เราสามารถจัดการหรือแก้ไขไฟล์ต่างๆ ได้ตามต้องการ


3. **ESP-IDF Tools**: เครื่องมือไหนจาก Lab4 ที่จะใช้ในการ build โปรแกรม ESP32?
   
   ตอบ : idf.pu build
   
4. **New Tools**: เครื่องมือใหม่ที่ติดตั้ง (tree, htop) ใช้ทำอะไร?
   
   ตอบ :
   - tree : ใช้สำหรับแสดงโครงสร้างไดเรกทอรีและไฟล์ต่างๆ ในรูปแบบที่เหมือนต้นไม้
   - htop เป็นเครื่องมือ interactive process viewer หรือโปรแกรมตรวจสอบระบบแบบโต้ตอบ ซึ่งใช้สำหรับมอนิเตอร์สถานะการทำงานของระบบแบบเรียลไทม์
 
5. **Architecture Focus**: การศึกษา ESP32 architecture แตกต่างจากการทำ arithmetic ใน Lab4 อย่างไร?

   ตอบ :
   - โดย ESP32 architecture จะศึกษาโครงสร้างภายในของไมโครคอนโทรลเลอร์ (ระบบ dual-core, memory mapping, cache system)
   - ส่วน arithmetic Lab4  คือการดำเนินการทางคณิตศาสตร์ (บวก ลบ คูณ หาร)

### ผลลัพธ์ที่คาดหวัง
- [ ] สร้างโฟลเดอร์ ESP32-Architecture-Lab เรียบร้อย
- [ ] คัดลอกหรือสร้าง docker-compose.yml ได้สำเร็จ
- [ ] รัน Docker container ได้ปกติ (เหมือน Lab4)
- [ ] เข้า container และใช้ ESP-IDF ได้ (คุ้นเคยจาก Lab4)
- [ ] ติดตั้งเครื่องมือเพิ่มเติม (tree, htop) สำเร็จ
- [ ] สร้าง directories สำหรับการทดลอง architecture เรียบร้อย

### 🤖 ข้อดีของการใช้เครื่องมือที่คุ้นเคย

**เหตุผลที่ใช้ docker-compose.yml เหมือน Lab4:**
- **ไม่ต้องเรียนรู้ใหม่**: ใช้คำสั่งเดิมที่รู้จักแล้ว
- **ประหยัดเวลา**: ไม่เสียเวลาอธิบาย Docker setup ใหม่
- **โฟกัส ESP32**: เน้นที่เนื้อหา architecture ไม่ใช่เครื่องมือ
- **ความมั่นใจ**: ใช้สิ่งที่ work แล้วจาก Lab4

### การบันทึกผลการทดลอง 

**Table 2.1: Memory Address Analysis**

| Memory Section | Variable/Function | Address (ที่แสดงออกมา) | Memory Type |
|----------------|-------------------|----------------------|-------------|
| Stack | stack_var | 0x3ffb4550 | SRAM |
| Global SRAM | sram_buffer | 0x3ffb16ac | SRAM |
| Flash | flash_string | 0x3f407b48 | Flash |
| Heap | heap_ptr | 0x3ffb5264 | SRAM |

**Table 2.2: Memory Usage Summary**

| Memory Type | Free Size (bytes) | Total Size (bytes) |
|-------------|-------------------|--------------------|
| Internal SRAM | 380096 | 520,192 |
| Flash Memory | 2097152 | varies |
| DMA Memory | 303096 | varies |

### คำถามวิเคราะห์ (ง่าย)

1. **Memory Types**: SRAM และ Flash Memory ใช้เก็บข้อมูลประเภทไหน?
   ตอบ :
   - SRAM : ใช้สำหรับเก็บข้อมูลที่เปลี่ยนแปลงบ่อยและต้องเข้าถึงได้อย่างรวดเร็ว เช่น ตัวแปร  **ข้อมูลจะหายไปเมื่อปิดไฟ
   - Flash Memory: ใช้สำหรับเก็บข้อมูลถาวร เช่น โค้ดโปรแกรม (firmware) ที่เราเขียนและคอมไพล์แล้ว, ข้อมูลที่ตั้งค่าไว้ (configuration data), และ ไฟล์ระบบ ** ข้อมูลยังคงอยู่แม้ไม่มีไฟเลี้ยง
3. **Address Ranges**: ตัวแปรแต่ละประเภทอยู่ใน address range ไหน?
   ตอบ :
   บน ESP32, ตัวแปรจะถูกจัดเก็บในหน่วยความจำประเภท SRAM และถูกแบ่งออกเป็นหลายส่วน:
   - Global/Static Variables: จัดเก็บในส่วนของ .data หรือ .bss ของ SRAM
   - Local Variables: จัดเก็บในส่วนของ Stack
   - Dynamically Allocated Variables: จัดเก็บในส่วนของ Heap
5. **Memory Usage**: ESP32 มี memory ทั้งหมดเท่าไร และใช้ไปเท่าไร?
   ตอบ :
   -SRAM : ทั้งหมดประมาณ 520 KB (Kilobytes) ซึ่งใช้เป็นหน่วยความจำหลักสำหรับโปรแกรมที่กำลังรันอยู่
   -Flash : โดยทั่วไปมีตั้งแต่ 4 MB (Megabytes) ขึ้นไป Flash ใช้เก็บโค้ดโปรแกรมและข้อมูลถาวร

---
