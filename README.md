# YOLO26n-Based-Bowling-scoring-System-for-RC-Car

A mobile computer vision application that records RC-car bowling sessions, performs fully offline AI inference directly on the device, tracks bowling pin state transitions over time, analyzes RC-car movement trajectories, and exports annotated result videos with scoring overlays and temporal event history.

The system combines a lightweight YOLO26n object detector with a hybrid React Native and native Android architecture optimized for mobile edge deployment.

---

# Features

- Fully offline mobile AI inference
- Native Android video processing pipeline
- Custom-trained YOLO26n object detector
- Detection of:
  - RC car
  - Bowling ball
  - Standing bowling pins
  - Fallen bowling pins
- Temporal pin state analysis
- Fall-order assignment and timestamping
- RC-car path visualization
- Automatic annotated output video generation
- Structured result summaries and metadata
- GPU-accelerated mobile rendering pipeline

---

# System Architecture

```text
┌────────────────────────────────────────────────────┐
│          React Native / Expo Frontend             │
│                                                    │
│  - Record Screen                                   │
│  - Processing Screen                               │
│  - Result Playback Screen                          │
└────────────────────┬───────────────────────────────┘
                     │
                     ▼
┌────────────────────────────────────────────────────┐
│        React Native Bridge / Expo Modules         │
│                                                    │
│  Exposes asynchronous native processing APIs       │
└────────────────────┬───────────────────────────────┘
                     │
                     ▼
┌────────────────────────────────────────────────────┐
│         Native Android CV Processing Layer         │
│                                                    │
│  - Video decoding                                  │
│  - Frame extraction                                │
│  - TensorFlow Lite inference                       │
│  - Tracking and scoring                            │
│  - Overlay rendering                               │
│  - Video encoding                                  │
└────────────────────┬───────────────────────────────┘
                     │
                     ▼
┌────────────────────────────────────────────────────┐
│          TensorFlow Lite YOLO26n Model             │
└────────────────────────────────────────────────────┘
```

---

# Processing Pipeline

```text
User records bowling video
        ↓
Raw MP4 video stored locally
        ↓
Video frames decoded natively
        ↓
Frames resized and preprocessed
        ↓
YOLO26n TensorFlow Lite inference
        ↓
Detections passed into tracking pipeline
        ↓
Standing → fallen transitions detected
        ↓
Car trajectory coordinates accumulated
        ↓
Annotated overlays rendered
        ↓
Encoded MP4 output generated
        ↓
Results returned to React Native UI
```

---

# Technology Stack

| Layer | Technology |
|---|---|
| Mobile Frontend | React Native |
| Framework | Expo |
| Language | TypeScript |
| Native Backend | Kotlin |
| AI Runtime | TensorFlow Lite |
| Detection Model | YOLO26n |
| Video Decoding | MediaCodec |
| Video Encoding | MediaMuxer |
| Rendering Pipeline | OpenGL ES 2.0 |
| Camera Interface | expo-camera |
| Navigation | Expo Router |

---

# Model Information

## Detection Classes

| ID | Class |
|---|---|
| 0 | ball |
| 1 | car |
| 2 | fallen-pins |
| 3 | standing-pins |

---

## Model Configuration

| Property | Value |
|---|---|
| Architecture | YOLO26n |
| Input Resolution | 640 × 640 |
| Deployment Format | TensorFlow Lite Float32 |
| Parameters | 2.37M |
| GFLOPs | 5.2 |

---

## Final Performance

| Metric | Value |
|---|---|
| Precision | 0.909 |
| Recall | 0.880 |
| mAP50 | 0.915 |
| mAP50-95 | 0.744 |

---

# Tracking and Scoring Logic

The application uses a lightweight rule-based temporal tracking system.

The tracking pipeline:
- matches detections across frames
- maintains stable pin identities
- detects standing-to-fallen transitions
- assigns fall order
- tracks RC-car movement trajectory

Tracking relies on:
- IoU matching
- center-distance matching
- temporal persistence logic

A pin is confirmed as fallen only when:
- the class changes from standing to fallen
- confidence remains stable
- the transition persists across multiple frames

---

# Output Video

The generated annotated video includes:
- color-coded bounding boxes
- pin IDs
- fall-order labels
- timestamps
- RC-car trajectory path
- scoring overlays
- event history timeline

The final output is encoded directly on-device as an MP4 video.

---

# Build and Run

## Requirements

- Node.js 18+
- Android SDK
- Java 17+
- Android device or emulator
- Expo CLI

---

## Setup

```bash
# Clone repository
git clone <YOUR_REPOSITORY_URL>

cd <PROJECT_NAME>

# Install dependencies
cd app
npm install

# Generate native Android project
npx expo prebuild --platform android

# Run on Android device
npx expo run:android
```

---

# TensorFlow Lite Model Setup

Place the TensorFlow Lite model inside:

```text
app/android/app/src/main/assets/
```

Then rebuild the application.

---

# Challenges

Main challenges encountered during development included:
- duplicate detections
- false fallen-pin predictions
- motion blur
- severe pin occlusion
- React Native processing bottlenecks
- mobile video encoding stability

These issues were mitigated using:
- native Kotlin processing
- temporal hysteresis logic
- optimized tracking logic
- improved threshold tuning
- OpenGL-based rendering pipelines

---

# Future Improvements

Potential future improvements include:
- real-time live inference
- transformer-based tracking
- improved occlusion handling
- quantization-aware training
- GPU/NPU delegate optimization
- cross-platform iOS support

---

# Conclusion

This project demonstrates a fully offline mobile computer vision pipeline capable of:
- object detection
- temporal tracking
- scoring analysis
- trajectory visualization
- annotated video generation

using lightweight deep learning models deployed entirely on mobile edge hardware.