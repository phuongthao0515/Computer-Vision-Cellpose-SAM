# Cellpose-SAM: Superhuman Generalization for Cellular Segmentation

**A reimplementation and analysis of the Cellpose-SAM framework for cell instance segmentation, combining the Segment Anything Model (SAM) with Cellpose's flow-field formulation.**

---

## Team Members - CC01 - Computer Vision (C03057)

1. **Le Thi Phuong Thao** (Student ID: 2252757)  
2. **Nguyen Thuy Tien** (Student ID: 2252806)

---

## Overview

Cell segmentation is a fundamental task in biomedical image analysis, enabling researchers to quantify cell populations, measure morphological features, track cellular dynamics, and analyze protein expression at single-cell resolution. However, accurate *instance segmentation* of biological cells remains challenging due to overlapping structures, weak or invisible boundaries, and large morphological variability across imaging modalities.

This project investigates the integration of **SAM’s pretrained visual representations** with **Cellpose’s geometry-driven flow-field segmentation**. The resulting **Cellpose-SAM** framework addresses the key limitations of both parent models:

### Limitations of Existing Approaches

- **SAM (Segment Anything Model)**
  - Requires per-object prompts
  - Lacks automatic instance separation for touching cells
  - Provides no topological guarantees for dense segmentation

- **Cellpose**
  - Limited generalization due to small training dataset (~70K objects)
  - Sensitivity to imaging variations
  - Dependency on accurate cell size estimation

---

## Core Idea: Complementary Integration

The Cellpose-SAM framework resolves these challenges by combining the strengths of both paradigms:

- **Foundation Model Representations**
  - Leverages SAM’s ViT-L encoder (~305M parameters)
  - Pretrained on 1B+ masks for strong generalization across domains
  - Transfers robust visual features to diverse microscopy modalities

- **Geometry-Driven Flow-Field Segmentation**
  - Each pixel predicts a vector pointing toward its instance center
  - Touching and overlapping cells are separated via geometric convergence
  - Enforces topological consistency through gradient tracking

- **Human-in-the-Loop Fine-Tuning**
  - Reduces annotation effort from hundreds of images to a handful of corrections
  - Enables rapid domain adaptation with minimal labeled data

The resulting system achieves segmentation accuracy approaching the **human-consensus bound**, outperforming individual human annotators while dramatically reducing annotation cost.

---

## Key Features

### 1. Flow-Field Segmentation
Unlike traditional boundary- or pixel-wise classification methods, flow-field representation encodes instance identity through directional information. Each pixel’s vector points toward its cell center, and instance boundaries naturally emerge where vector fields diverge.

- Robust to weak or invisible boundaries  
- Successfully separates heavily touching cells  
- Achieves **IoU = 0.976 at 95% overlap** in experimental evaluations  

---

### 2. Foundation Model Integration
SAM’s Vision Transformer encoder provides rich, transferable visual knowledge learned from millions of images.

Architectural adaptations for cellular segmentation include:
- Reduced resolution (256 × 256)
- Smaller patch size (8 × 8)
- Global attention
- Transposed convolution for dense output

These modifications preserve SAM’s representational power while meeting the requirements of high-resolution cellular microscopy.

---

### 3. Sample-Efficient Adaptation
The human-in-the-loop paradigm shifts training from full annotation to *correction-based learning*.

- Effective fine-tuning with as few as **5 training images**
- Achieved **76.1% Average Precision** with minimal supervision
- Strong robustness to domain shifts across cell types and imaging conditions

---

### 4. Robust Instance Separation
The gradient tracking algorithm ensures that every foreground pixel converges to exactly one sink point.

- Enforces strict topological consistency
- Prevents fragmented masks and merged instances
- Eliminates common failure modes of pixel-wise classifiers

---

### 5. 2D and 3D Segmentation Support
Cellpose-SAM supports both planar and volumetric microscopy data:

- **2D segmentation** for standard microscopy images
- **3D segmentation** via:
  - Unified 3D flow fields across orthogonal planes, or
  - Slice-wise 2D segmentation followed by spatial stitching

This flexibility enables application across a wide range of biological imaging workflows.

---

## Summary

Cellpose-SAM demonstrates that **foundation models and domain-specific geometric priors are fundamentally complementary**. Neither SAM nor Cellpose alone achieves the performance enabled by their thoughtful integration. This work highlights a general paradigm for biomedical image analysis: combining large-scale pretrained representations with task-specific inductive biases to achieve robust, accurate, and accessible solutions.
