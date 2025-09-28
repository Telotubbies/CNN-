# 3D-Print Defect Classification (ResNet-9, PyTorch)

โปรเจ็กต์นี้ใช้โมเดล Convolutional Neural Network (ResNet-9) สำหรับการจำแนกข้อผิดพลาดที่เกิดขึ้นระหว่างการพิมพ์สามมิติจากภาพขนาด 256×256 เป้าหมายเพื่อช่วยตรวจจับความผิดพลาด เช่น งานพิมพ์ปกติ (Good) การขาดกันวัสดุ (Under-Extrusion) การเกิดเส้นใย (Stringing) และ(Spaghetti)

## โครงสร้างrepo
- **general_data** : เก็บข้อมูลเมทาดาทาเพิ่มเติม  
- **splits** : ไฟล์ train.csv, val.csv, test.csv สำหรับแบ่งชุดข้อมูล  
- **class_list.txt** และ **labels.txt** : รายการคลาสและคำอธิบายป้ายกำกับ  
- **resnet9_safe_trainV4.ipynb** : โน้ตบุ๊กหลักเวอร์ชันล่าสุด ใช้ Focal Loss  
- **resnet9_safe_trainV3.ipynb** : เวอร์ชันก่อนหน้า  
- **resnet9_testv1.ipynb**, **resnet9_testv2.ipynb** : โน้ตบุ๊กสำหรับทดสอบและประเมินผล  
- **README.md** : คำอธิบายโปรเจ็กต์  

## คลาสและป้ายกำกับ
คลาสดิบที่ใช้ในชุดข้อมูลคือ  
- 0 = Good  
- 1 = Under-Extrusion  
- 2 = Stringing  
- 4 = Spaghetti  

โดยจะถูกแมปเข้าสู่ดัชนี 0–3 สำหรับใช้กับ Loss function  

## วิธีใช้งาน
1. เตรียมไฟล์ splits ให้ครบ train.csv, val.csv, test.csv  
2. เทรนโมเดลโดยใช้ **resnet9_safe_trainV4.ipynb** ซึ่งตั้งค่าให้ใช้ Focal Loss สำหรับแก้ปัญหาชุดข้อมูลไม่สมดุล และรองรับ Mixed Precision Training  

## ไฮเปอร์พารามิเตอร์ (ค่าเริ่มต้น v4)
- ขนาดภาพ 256x256
- Batch size 128  
- Optimizer Adam (learning rate 1e-3)  
- จำนวน epoch 20–40  
- Data augmentation เช่น Flip และ ColorJitter  
- Loss function ใช้ Focal Loss  

## การแก้ปัญหา Class Imbalance
- ใช้ Focal Loss (เปิดใช้แล้วใน v4)  
- ใช้ class weight ใน CrossEntropyLoss  
- ใช้ WeightedRandomSampler เพื่อ oversampling  

## Changelog
- **v4** : เพิ่ม Focal Loss และปรับ DataLoader/AMP  
- **v3** : เพิ่มการทำ augmentation  
- **v2** : แยก train/val/test และเพิ่มการประเมินผล  
- **v1** : เวอร์ชันตั้งต้น ResNet-9  

## License
MIT License สามารถแก้ไขและนำไปใช้ต่อได้ตามต้องการ  
