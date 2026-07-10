# FOSSEE_internship
Fine grained visual taxonomy for object classification in industrial robotics :Lego brick sorting with YOLOv8.

# Lego Vision Sorter

Fine-grained visual taxonomy for object classification in industrial robotics a computer vision pipeline that distinguishes near identical Lego brick variants (same shape, subtle color/design differences) for robotic sorting decisions. Built during a FOSSEE (IIT Bombay) internship under the Industrial Robotics and Automation domain.

## Problem

Standard classification (ImageNet style) separates categories with large visual differences cats vs. airplanes. This project tackles the harder intra family case: objects sharing shape and scale, where the discriminating signal (color under variable lighting, subtle structural geometry, stud/design detail) is small and easily lost to noise. The test case is Lego bricks across 11 classes; the target use case is a perception layer for a Dobot Magician robotic sorting arm.

## Pipeline

```
capture frame → detect object → classify object → output decision to actuator
```

- Data collection: custom OpenCV script, live webcam feed, keyboard-controlled capture (single-frame and 10-frame burst modes), fixed diffuse lighting, hardcoded crop window for background consistency.
- Phase 1 MobileNetV2: transfer-learning baseline, 3-class color classifier (red/black/white), >90% accuracy by epoch 10. Validated the pipeline before scaling up.
- Phase 2 YOLOv8n: single-pass detection + classification across 11 classes, trained on Google Colab (T4 GPU), nano variant chosen deliberately for eventual edge deployment.
- Live inference: real-time webcam inference with a calibrated confidence threshold (0.45) and a running per-class detection counter for sorting counts.

## Stack

Python 3.11 · TensorFlow 2.21 / Keras · OpenCV 4.13 · YOLOv8 (Ultralytics) · LabelImg · Google Colab (T4 GPU) · Logitech C270 webcam

## Results

11 classes, 1,827 images total. Overall mAP50: **0.757**.

| Status | Classes | mAP50 |
| Production-ready | Red, Black, Blue, Yellow, Green, Orange, Tan | 0.85 – 0.94 |
| Needs retraining | White, Flat plate, Pearl, Grey | 0.39 – 0.61 |

The four weaker classes were traced to specific, documented root causes rather than model failure: annotation noise (loose bounding boxes on low-contrast objects), scale mismatch (training/inference zoom mismatch), and insufficient discriminative signal under the current lighting setup (pearl/grey). Fixes are data and lighting problems, not architecture problems a lower confidence threshold was tested and rejected as a workaround since it spikes false positives across the whole pipeline.

## Key engineering decisions

- YOLOv8n (nano) chosen over larger variants specifically for edge deployment viability, not just training speed.
- Production confidence threshold (0.45) fixed after explicit threshold sweep analysis; failing classes are fixed by retraining on corrected data, not by lowering the global threshold.
- Debugging spanned hardware (camera enumeration), OS (macOS TCC camera permissions), environment (Python/TensorFlow version compatibility), data (annotation quality), and inference layers each documented with root cause, not just the fix.

## Next steps

- Actuation loop: connect classifier output to the Dobot sorting arm.
- Synthetic data generation (Nvidia Omniverse / Isaac Sim) for the four failing classes.
- Contrastive embedding + uncertainty-aware inference to reject ambiguous inputs instead of misclassifying.
- Edge deployment: ONNX export → TensorRT engine on Jetson Nano/Orin, with latency/FPS benchmarking.

## Acknowledgment

Developed under the guidance of Prof. Jayendran Venkateswaran (IEOR, IIT Bombay) as part of a FOSSEE internship (NMEICT, Ministry of Education, Government of India).
