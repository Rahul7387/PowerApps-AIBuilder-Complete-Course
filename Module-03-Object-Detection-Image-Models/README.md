# Module 03 — AI Builder: Object Detection & Image Models


---

## 🎯 Learning Objectives
- Understand object detection vs image classification
- Prepare a quality image dataset
- Train and publish a custom object detection model
- Build a mobile inventory app using camera input

---

## 📖 Theory

### Object Detection vs Image Classification

| | Object Detection | Image Classification |
|---|---|---|
| What it does | Finds AND locates objects | Identifies image type |
| Output | Bounding boxes + labels | Single label |
| Use case | Count items on shelf | Is this a cat or dog? |

> **Trainer insight:** Object detection is the model I've deployed most often in manufacturing and retail — inventory counting, quality control, safety compliance (is the worker wearing a helmet?). All without custom code.

### Dataset Requirements

| Parameter | Minimum | Recommended |
|---|---|---|
| Images per object tag | 15 | 50+ |
| Resolution | 256×256 px | 640×480+ |
| File size | — | < 6 MB |
| Formats | JPEG, PNG, BMP | — |

**The critical variation rule:**
- Different angles: front, side, 45°, top-down
- Different lighting: bright, dim, natural, artificial
- Different backgrounds: shelf, table, floor, outdoors
- Include partial and occluded objects

### Common Dataset Mistakes (I see every time)
1. All images from same location → fails in real environment
2. Only "perfect" images → fails on worn/dirty items
3. Too few negative examples → false positives everywhere
4. Images too similar → model memorizes instead of generalizes

### Confidence Score Strategy
- > 90% → Use result directly
- 70-90% → Flag for review
- 50-70% → Route to human verification
- < 50% → Treat as not detected

---

## ✅ Best Practices

1. **Collect images in the actual deployment environment** — lab photos won't work on factory floor
2. **Include "negative" images** — images WITHOUT the target object
3. **Use 3+ different camera/device types** for better generalization
4. **Set confidence threshold in your app** — reject results below 70%
5. **Test with live camera before go-live** — desktop images differ from mobile
6. **Retrain monthly** — add production failure images to improve model

---

## 🔬 Hands-On Lab — Inventory Scanner App

### Scenario
Warehouse team scans product shelves, counts items per product type, logs counts to SharePoint.

### Phase 1 — Train the Model

#### Step 1 — Prepare Dataset
Collect 15+ images each for 3 product types (e.g., Red bottle, Blue box, Yellow can).
Include different backgrounds and angles.

#### Step 2 — Create Object Detection Model
1. AI Builder → **+ New model** → **Object detection**
2. Add tags: `RedProduct`, `BlueProduct`, `YellowProduct`

#### Step 3 — Upload, Label, Train
1. Upload all images
2. For each image: draw tight bounding boxes around each product → assign tag
3. Label ALL objects in EVERY image
4. **Train** → wait 20-30 min
5. Review: Precision > 85%, Recall > 80%
6. **Publish**

### Phase 2 — Build Canvas App

#### Step 4 — Create Phone App
1. Power Apps → **Canvas app from blank** → Phone format
2. Name: `Inventory Scanner`

#### Step 5 — Camera + AI
Add Camera control. Button "Take Photo":
```powerfx
Set(varPhoto, Camera1.Photo)
```

Analysis button:
```powerfx
Set(varResults, 'Object detector'.Predict(varPhoto));
Set(varRedCount, CountIf(varResults.Predictions, tags.tagName = "RedProduct"));
Set(varBlueCount, CountIf(varResults.Predictions, tags.tagName = "BlueProduct"));
Set(varYellowCount, CountIf(varResults.Predictions, tags.tagName = "YellowProduct"))
```

Display counts:
```powerfx
"Red: " & varRedCount & " | Blue: " & varBlueCount & " | Yellow: " & varYellowCount
```

Save button:
```powerfx
Patch(InventoryCounts, Defaults(InventoryCounts), {
    ScanDate: Now(),
    ScannedBy: User().FullName,
    RedCount: varRedCount,
    BlueCount: varBlueCount,
    YellowCount: varYellowCount
})
```

#### Step 6 — Test on Mobile
1. Publish app → open Power Apps on phone
2. Take photo of products → tap Analyze → verify counts
3. Tap Save → check SharePoint list

---

## 📋 POC Summary

**Project:** Smart Inventory Scanner
**Business value:** Eliminates manual inventory counting, reduces stockout risk

*Next: [Module 04 — Prediction & Classification →](../Module-04-Prediction-Classification/README.md)*
