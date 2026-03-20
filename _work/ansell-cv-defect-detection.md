---
title: "Real-Time Defect Detection for Glove Packaging"
category: "Professional"
role: "Intern Data Scientist, Ansell Lanka Pvt Ltd"
date: 2024-03-01
excerpt: "Developed a computer vision system using YOLO for real-time defect detection in medical glove packaging lines."
header:
  overlay_image: /assets/images/DefectDetection.png
  overlay_filter: 0.3
tags: [Computer Vision, YOLO, Python, OpenCV, IoT, YOLOv8, EasyOCR, CUDA, PyQt, Socket Programming]
layout: single
author_profile: true
read_time: true
share: true
related: true
---

At **Ansell Lanka**, I contributed to the development and testing of a **real-time defect detection system** for medical glove packaging lines using **YOLO-based object detection**. The system — known internally as the **Outer Pack Automated Optical Inspection (AOI) Project** — was integrated with MultiVac packing machines across three supermarket packaging lines: **SPL2, SPL3, and SPL4**.

---

## System Architecture

The deployed system consisted of three tightly integrated components:

- **Camera & Electronic Panel** — captures real-time images of glove packages on the conveyor belt
- **Python-based inference software** — classifies defects per group of 12 packages
- **Industrial Printer** — marks defective packs via Ethernet connection with the IPC (Industrial Personal Computer), enabling targeted removal

---

## Defect Classes

The system was designed to classify three types of defects in medical glove outer packaging:

1. **Top print smudging** — unclear or smudged print on the top surface of the package
2. **Bottom print smudging** — unclear or smudged print on the bottom surface
3. **Seal integrity failures** — including air bubbles inside the seal, packaging material caught in the seal, and text printed incorrectly on the seal region

---

## Key Technical Contributions

### Data Preprocessing & Lightning Equalization
Implemented image preprocessing to equalize lighting conditions across camera frames. This was critical to prevent the thresholding algorithm (used for caught-in-seal detection) from producing false positives due to lighting variation across the packaging line.

### Synthetic Dataset Generation
Air bubble defects inside seals were rare in production, creating a significant **class imbalance** problem. To address this:
- Extracted defect regions from the limited real images using graphics software
- Wrote a Python script to **composite and blend** extracted defects onto clean seal images at varied positions and opacities
- Generated a balanced synthetic dataset sufficient to train a robust YOLOv8 model

### YOLOv8 Model Training — Seal Integrity Detection
Trained and validated a **YOLOv8 object detection model** specifically for detecting air bubble defects in seal regions. The model was trained on the synthetically augmented dataset and evaluated against real production samples.

### Text Detection & Erasure Pipeline
Used a **pretrained EasyOCR model** to detect text regions on seal areas. Detected text bounding boxes were erased from the image before the caught-in-seal thresholding step, preventing false detections caused by printed text patterns. This pipeline was later **optimized for speed** to meet real-time performance requirements.

### Rule-Based Empty Pack Detection
Implemented a rule-based algorithm to distinguish between **empty packages** and those containing gloves — a prerequisite for accurate defect classification downstream.

### Socket Programming — Industrial Printer Interface
Developed a **Python class** to manage Ethernet communication between the IPC and the industrial printer. The class exposed functions for:
- Initiating and maintaining connections
- Reading from and writing to the printer
- Updating the print job dynamically for each group of 12 packs based on software inference results

### Synchronization Testing
Conducted two-phase synchronization testing to verify that defect marks were printed on the exact package flagged by the model:
1. Replaced model inferences with **known test patterns** and validated printer output
2. End-to-end testing with **live model inferences** on the packaging line

### Performance Optimization
Profiled and optimized the multithreaded software architecture to eliminate bottlenecks. Drew **time diagrams** to trace processing time across pipeline stages. Key process times measured after optimization (seconds):

| Pipeline Stage | Before | After |
|---|---|---|
| Defect on top & return coords | 2.0865 | 0.66415 |
| Defect on bottom & return coords | 1.0252 | 0.1191 |
| Correct position | 0.011 | 0.0346 |
| **Total** | — | **~0.823** |

### Physical Validation
Conducted side-by-side comparison between the system's defect classifications and those made by experienced production line workers to validate real-world accuracy and calibrate rejection thresholds.

---

## Technologies & Tools

**Libraries & Frameworks:**
- Python, PyQt (GUI)
- YOLOv8 (object detection)
- EasyOCR (pretrained OCR model)
- OpenCV, Pillow (image processing)
- NumPy, Keras (ML utilities)
- CUDA (GPU acceleration)

**Development Tools:**
- PyCharm IDE
- Visual Studio IDE

---

## Impact

- Automated manual defect inspection across three packaging lines, reducing reliance on human visual inspection
- Achieved real-time inference within ~0.82 seconds per batch of 12 packages after optimization
- Contributed to a **research paper** published by the Ansell AI Research Team on this system
- Authored technical documentation including a user manual and IQ (Installation Qualification) document
- Assisted in **training production staff** and replicating the development environment for the SPL3 line

---

<div style="margin-top: 3rem; padding-top: 2rem; border-top: 1px solid #ddd; text-align: center;">
  <a href="/projects/#professional-projects" style="font-weight: 500; color: #0d341c; text-decoration: none;">← See All Projects</a>
</div>
