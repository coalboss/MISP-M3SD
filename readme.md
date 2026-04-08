<div align="center">

# MISP-M3SD

### A Large-Scale Multimodal, Multi-Scenario, and Multilingual Dataset  
### for Robust Speaker Diarization

[![Hugging Face Dataset](https://img.shields.io/badge/🤗%20Dataset-MISP--M3SD-yellow)](https://huggingface.co/datasets/Igor97/MISP-M3SD)
[![Task](https://img.shields.io/badge/Task-Speaker%20Diarization-orange)]()
[![Modality](https://img.shields.io/badge/Modality-Audio--Visual-blue)]()
[![Languages](https://img.shields.io/badge/Languages-16-success)]()
[![Scenarios](https://img.shields.io/badge/Scenarios-14-informational)]()
[![Hours](https://img.shields.io/badge/Duration-770.55h-red)]()

**🤗 Dataset Page:** [https://huggingface.co/datasets/Igor97/MISP-M3SD](https://huggingface.co/datasets/Igor97/MISP-M3SD)

</div>

---
## ✨ Introduction

**MISP-M3SD** is a large-scale **multimodal**, **multi-scenario**, and **multilingual** dataset for **robust speaker diarization**, constructed from in-the-wild online videos.

It contains more than **770 hours** of synchronised audio-visual recordings, covering **14 scenarios** and **16 languages**. The dataset is designed to support the development of speaker diarization systems with stronger **cross-domain generalization** under realistic conditions, including:

- background noise  
- reverberation  
- overlapping speech  
- off-screen speech  
- motion blur  
- unstable speaker visibility  
- camera switching  

This repository mainly introduces the **construction pipeline** used to build MISP-M3SD, including:

- multilingual multi-scenario media acquisition
- data cleaning and preprocessing
- audio-visual synchronisation detection
- face detection, tracking, and lip ROI extraction
- cross-modal consistency-guided annotation

For dataset description, released files, and access information, please also refer to the **Hugging Face dataset page**: [Igor97/MISP-M3SD](https://huggingface.co/datasets/Igor97/MISP-M3SD).

---

## 📌 Table of Contents

- [Pipeline Overview](#-pipeline-overview)
- [Environment and Dependencies](#-environment-and-dependencies)
- [Self-Build Pipeline](#-self-build-pipeline)
- [Released Dataset Files](#-released-dataset-files)
- [Notes](#-notes)
- [Citation](#-citation)

---

## 🧭 Pipeline Overview

The construction pipeline of **MISP-M3SD** consists of the following stages:

### 1. Media Acquisition
Retrieve candidate multi-speaker videos from public online platforms using multilingual and multi-scenario keywords.

### 2. Scene Detection and Segmentation
Segment long videos into clips with relatively stable visual scenes for downstream processing.

### 3. Audio-Visual Synchronisation Detection
Filter out clips with dubbing, off-screen narration, or severe audio-visual desynchronization.

### 4. Face Detection, Tracking, and Lip ROI Extraction
Detect visible speakers, track face trajectories, and extract lip ROIs for multimodal analysis.

### 5. Cross-Modal Consistency-Guided Annotation
Generate final diarization annotations using multi-branch diarization systems, consistency-based quality control, and selective manual verification.

---

