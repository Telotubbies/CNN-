## Imbalance Results & Fixes
### Current test snapshot
- Accuracy: **0.7829**
- Balanced Accuracy: **0.5971**
- Top-2 Accuracy: **0.9369**
- Macro-F1: **0.6439**
- Weighted-F1: **0.7748**

Per-class (precision / recall / F1 / support)
- 0 (Good): **0.78 / 0.71 / 0.74 / 1893**
- 1 (Under-Extrusion): **0.78 / 0.92 / 0.84 / 2328**
- 2 (Stringing): **0.81 / 0.55 / 0.65 / 532**
- 4 (Spaghetti): **0.74 / 0.22 / 0.34 / 78**

Key takeaway: **minority classes (2, 4) are under-detected**, especially class 4 (recall 0.22). Accuracy alone hides this; rely on **Balanced Accuracy / Macro-F1**.

### What we do for class imbalance (no code)
1) **Loss design**
   - Use **Focal Loss** (γ≈2.0) to upweight hard/rare samples.
   - Set **α per-class** from inverse frequency (train set).
2) **Sampling**
   - **Weighted sampling** (oversample minority) ที่ DataLoader (train เท่านั้น)
   - Validation/Test คง distribution จริง เพื่อรายงานผลไม่ลำเอียง
3) **Augmentation policy (minority-first)**
   - เพิ่มความแรงของ augmentation ให้คลาส **2** และ **4** (crop/rotate/brightness/contrast)
   - ระวังอย่าให้ transform ทำลายสัญญาณ defect
4) **Metrics & model selection**
   - เลือกโมเดลด้วย **Macro-F1** และ **Balanced Accuracy** (ไม่ใช้ accuracy อย่างเดียว)
   - รายงาน **confusion matrix (normalized)** และ **per-class PR/RC/F1**
   - เก็บ **Top-2 accuracy** เป็นตัวชี้ near-miss
5) **Thresholding & calibration (optional)**
   - ถ้ามี logits ลองปรับ **per-class threshold** หรือ **temperature scaling** เพื่อดัน recall ของ minority
6) **Operational notes**
   - คลาส 4 หายาก ควรเพิ่ม **exposure per epoch** ผ่าน sampler/oversample
   - ติดตาม **PR/RC per-class** ระหว่างเทรน
   - เมื่อข้อมูลใหญ่ขึ้น ค่อย ๆ ลด oversampling แล้วพึ่ง **Focal Loss** มากขึ้น

### Suggested default (short)
- **Focal Loss**: γ=2.0, **α** จาก inverse class freq  
- **Weighted sampling**: train loader เท่านั้น  
- **Early-stop / select**: ใช้ **Macro-F1 (val)**  
- **Report**: Accuracy, Balanced Acc., Top-2, per-class PR/RC/F1, normalized CM

### Known failure modes
- Oversampling มากไป → overfit minority (ดู val F1 ของคลาส 2/4)
- Augmentation สีแรงเกินไปบน artifacts ขนาดเล็ก → สัญญาณหาย
- ตัดสินจาก accuracy อย่างเดียว → minority collapse
