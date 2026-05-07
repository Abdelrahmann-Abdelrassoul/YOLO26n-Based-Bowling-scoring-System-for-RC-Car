# Model Training Report — RC-Car Bowling Detection System

## Project Title

**YOLO26n-Based Real-Time Object Detection and Pin State Analysis for RC-Car Bowling**

---

## 1. Project Overview

This report presents the complete training pipeline, dataset preparation process, optimization strategy, evaluation, and deployment preparation for a custom object detection model designed for an RC-car bowling analysis system.

The developed model detects and classifies bowling-related objects in recorded videos, including:

* Bowling ball
* RC car
* Standing bowling pins
* Fallen bowling pins

The trained model is integrated into a mobile application for offline edge inference and is used to:

* Detect bowling pins in real time
* Track pin states over time
* Detect standing-to-fallen transitions
* Render tracking overlays and movement paths
* Export annotated result videos

The final model was trained using the Ultralytics YOLO framework with a YOLO26n architecture and optimized for mobile deployment through TensorFlow Lite conversion. 

---

# 2. Objectives

The primary objectives of the training pipeline were:

* Develop a lightweight object detector suitable for mobile edge deployment
* Achieve accurate pin-state classification under dynamic motion
* Detect small objects reliably in indoor bowling environments
* Maintain real-time inference speed
* Support post-processing tracking and scoring logic
* Enable offline operation without cloud dependency

---

# 3. Dataset Preparation

## 3.1 Dataset Source

The dataset was collected and annotated using Roboflow. The dataset contains labeled bowling scenes captured from mobile-recorded videos.

The dataset includes four object classes:

| Class ID | Class Name    |
| -------- | ------------- |
| 0        | ball          |
| 1        | car           |
| 2        | fallen-pins   |
| 3        | standing-pins |

---

## 3.2 Dataset Splitting

The exported dataset was reorganized into a clean YOLO directory structure using a custom preprocessing pipeline. 

The dataset was split using the following ratios:

| Split      | Ratio |
| ---------- | ----- |
| Training   | 70%   |
| Validation | 20%   |
| Testing    | 10%   |

The splitting process:

* matched images with labels
* shuffled samples randomly
* copied files into YOLO-compatible folders
* generated a new `data.yaml` configuration file

---

## 3.3 Dataset Statistics

### Training Set

| Class         | Instances |
| ------------- | --------- |
| ball          | 2323      |
| car           | 1828      |
| fallen-pins   | 8327      |
| standing-pins | 16681     |

Total training boxes: **29159**

---

### Validation Set

| Class         | Instances |
| ------------- | --------- |
| ball          | 210       |
| car           | 177       |
| fallen-pins   | 896       |
| standing-pins | 1762      |

Total validation boxes: **3045**

---

### Test Set

| Class         | Instances |
| ------------- | --------- |
| ball          | 112       |
| car           | 78        |
| fallen-pins   | 404       |
| standing-pins | 742       |

Total test boxes: **1336**

---

# 4. Data Augmentation

The dataset already contained augmentation generated through Roboflow preprocessing. Additional lightweight augmentations were applied automatically during training using Ultralytics augmentation pipelines.

The augmentations included:

* Blur
* Brightness variation
* Rotation
* HSV augmentation
* Translation
* Mosaic augmentation
* Horizontal flipping
* Random erasing

The training configuration used:

* `augment=False` manually, while internal Ultralytics preprocessing still applied controlled augmentations
* `mosaic=1.0`
* `translate=0.1`
* `scale=0.5`
* `fliplr=0.5`

Additional Albumentations preprocessing included:

* Blur
* MedianBlur
* CLAHE
* ToGray



---

# 5. Model Architecture

## 5.1 Selected Model

The selected architecture was:

# YOLO26n

YOLO26n is a lightweight nano-scale object detector optimized for:

* fast inference
* edge deployment
* reduced parameter count
* low memory consumption

The architecture was selected because the target deployment environment was a mobile device requiring real-time inference.

---

## 5.2 Model Summary

| Property   | Value     |
| ---------- | --------- |
| Layers     | 260       |
| Parameters | 2,505,360 |
| GFLOPs     | 5.8       |
| Classes    | 4         |

The final fused inference model contained:

* 122 layers
* 2,375,616 parameters
* 5.2 GFLOPs



---

# 6. Transfer Learning Strategy

Transfer learning was used to accelerate convergence and improve generalization.

The model initialization:

* started from pretrained YOLO26n weights
* transferred 606 out of 708 pretrained layers

This allowed the model to:

* reuse low-level visual features
* reduce training time
* improve detection stability
* improve small-object detection

---

# 7. Training Configuration

## 7.1 Hardware Environment

| Component | Value              |
| --------- | ------------------ |
| Platform  | Kaggle             |
| GPU       | Tesla T4           |
| CUDA      | CUDA 12.8          |
| Framework | Ultralytics 8.4.46 |
| PyTorch   | 2.10.0             |



---

## 7.2 Hyperparameters

| Parameter                  | Value   |
| -------------------------- | ------- |
| Epochs                     | 100     |
| Early Stopping Patience    | 25      |
| Image Size                 | 640     |
| Batch Size                 | 16      |
| Optimizer                  | AdamW   |
| Initial Learning Rate      | 0.001   |
| Final Learning Rate Factor | 0.01    |
| Weight Decay               | 0.0005  |
| Cosine LR Scheduling       | Enabled |
| Mixed Precision            | Enabled |
| Workers                    | 2       |

Training command: 

---

# 8. Training Process Analysis

The model demonstrated stable convergence throughout training.

## 8.1 Early Epoch Performance

| Epoch | mAP50 | mAP50-95 |
| ----- | ----- | -------- |
| 1     | 0.590 | 0.407    |
| 5     | 0.830 | 0.629    |
| 10    | 0.872 | 0.671    |

The model rapidly improved during early epochs due to transfer learning initialization.

---

## 8.2 Mid-Training Performance

Between epochs 20–50:

* localization became more stable
* false positives decreased
* pin classification improved significantly

Representative metrics:

| Epoch | mAP50 | mAP50-95 |
| ----- | ----- | -------- |
| 25    | 0.903 | 0.718    |
| 37    | 0.911 | 0.734    |
| 50    | 0.906 | 0.733    |

---

## 8.3 Final Training Performance

Best performance was achieved at epoch 71.

| Metric    | Value |
| --------- | ----- |
| Precision | 0.909 |
| Recall    | 0.880 |
| mAP50     | 0.915 |
| mAP50-95  | 0.744 |

Training stopped automatically using Early Stopping after no improvement for 25 epochs.



---

# 9. Final Evaluation Results

## 9.1 Overall Metrics

| Metric    | Value |
| --------- | ----- |
| Precision | 0.909 |
| Recall    | 0.880 |
| mAP50     | 0.915 |
| mAP50-95  | 0.744 |

---

## 9.2 Per-Class Results

| Class         | Precision | Recall | mAP50 | mAP50-95 |
| ------------- | --------- | ------ | ----- | -------- |
| ball          | 0.853     | 0.872  | 0.886 | 0.637    |
| car           | 0.961     | 0.853  | 0.915 | 0.707    |
| fallen-pins   | 0.867     | 0.835  | 0.894 | 0.749    |
| standing-pins | 0.957     | 0.961  | 0.963 | 0.882    |

The standing-pins class achieved the strongest performance due to:

* higher dataset frequency
* clearer spatial structure
* lower ambiguity

Fallen pins were more difficult because of:

* motion blur
* occlusion
* varied orientations



---

# 10. Inference Performance

## 10.1 Validation Speed

| Stage       | Time   |
| ----------- | ------ |
| Preprocess  | 0.2 ms |
| Inference   | 2.2 ms |
| Postprocess | 2.3 ms |

The model achieved extremely fast inference suitable for mobile deployment and edge execution.



---

# 11. Deployment Preparation

## 11.1 TensorFlow Lite Conversion

Multiple deployment formats were evaluated:

* Quantized TFLite
* Float16 TFLite
* Standard TFLite

Quantized models produced unacceptable accuracy degradation and unstable predictions during edge inference.

As a result:

* quantization was discarded
* the final deployment used standard TensorFlow Lite conversion without aggressive quantization

This preserved:

* pin-state accuracy
* tracking stability
* confidence reliability



---

# 12. Video Processing Pipeline

The final inference pipeline operates as follows:

1. Record video
2. Extract frames
3. Run YOLO inference
4. Track pins across frames
5. Detect standing-to-fallen transitions
6. Render overlays and tracking visuals
7. Export annotated video



---

# 13. Pin Tracking Logic

A custom tracking and pin-state management pipeline was implemented on top of YOLO detections.

The tracking system:

* matches detections across frames
* maintains persistent pin identities
* tracks standing/fallen state transitions
* records fall order and timestamps

Matching was performed using:

* bounding-box center distance
* IoU overlap scoring
* temporal persistence

The system also:

* maintains pin memory
* removes stale tracks
* records fall history
* renders car movement paths



---

# 14. Challenges Encountered

Several major challenges were encountered during development.

## 14.1 Duplicate Detections

The model occasionally produced:

* multiple bounding boxes on the same pin
* overlapping detections
* duplicated fallen-pin predictions

This required:

* improved confidence thresholds
* additional matching logic
* duplicate suppression handling

---

## 14.2 False Fallen Pin Detection

Standing pins were sometimes incorrectly classified as fallen due to:

* partial occlusion
* motion blur
* difficult viewing angles

This affected:

* scoring accuracy
* fall timeline consistency

---

## 14.3 Tracking Instability

Tracking became unstable when:

* pins overlapped
* pins moved rapidly
* objects exited and re-entered the frame

Additional temporal matching logic was introduced to reduce identity switching.



---

# 15. Advantages of the Final System

The final model achieved several important strengths:

* Lightweight architecture suitable for mobile devices
* High standing-pin detection accuracy
* Fast inference speed
* Fully offline execution
* Real-time tracking support
* Temporal pin-state analysis
* Edge deployment compatibility
* Stable video annotation pipeline

---

# 16. Limitations

Despite strong overall performance, some limitations remain:

* fallen pins remain more difficult than standing pins
* severe occlusion can reduce tracking stability
* duplicate detections may still appear in crowded scenes
* rapid motion may cause temporary pin-state inconsistencies

---

# 17. Future Improvements

Potential future enhancements include:

* larger and more diverse dataset collection
* temporal deep-learning models for video understanding
* transformer-based object tracking
* dedicated re-identification modules
* improved fall confirmation logic
* mobile GPU acceleration optimization
* quantization-aware training for better TFLite compression

---

# 18. Conclusion

This project successfully developed a lightweight YOLO26n-based object detection system for RC-car bowling analysis and mobile deployment.

The final model achieved:

* 0.915 mAP50
* 0.744 mAP50-95
* real-time inference speed
* stable mobile deployment capability

The system was able to:

* detect bowling-related objects
* classify pin states
* track pins over time
* detect fall events
* generate annotated output videos

The final deployment demonstrates the feasibility of combining lightweight deep learning models with custom tracking pipelines for fully offline mobile computer vision applications.
