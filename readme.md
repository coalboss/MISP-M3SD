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

The construction pipeline of **MISP-M3SD** consists of the following three stages:

### Stage 1: Multilingual Multi-Scenario Media Acquisition
Candidate videos with explicit multi-speaker interaction are collected from public online platforms using a multilingual multi-scenario keyword lexicon.

### Stage 2: Data Cleaning and Preprocessing
The collected videos are filtered and processed through shot detection and segmentation, audio/video extraction and normalisation, audio-visual synchronisation detection, face detection and tracking, and lip ROI extraction.

### Stage 3: Cross-Modal Consistency-Guided Annotation
Final diarization annotations are generated through a multi-branch framework with consistency-based quality control and selective manual verification.

---

### Basic Tools

- [FFmpeg](https://ffmpeg.org/) for audio/video extraction and normalisation
- [PySceneDetect](https://github.com/Breakthrough/PySceneDetect) for scene detection
- [SyncNet](https://github.com/joonson/syncnet_python) for audio-visual synchronization detection
- [RetinaFace](https://github.com/bubbliiiing/retinaface-pytorch) for face detection
- [deep-sort-realtime](https://github.com/levan92/deep_sort_realtime) for face tracking
- [MediaPipe](https://github.com/google-ai-edge/mediapipe) for facial landmark estimation and lip ROI extraction

### Diarization Systems

- [3D-Speaker](https://github.com/modelscope/3D-Speaker) for audio-only speaker diarization
- [MISP AVSD baseline](https://github.com/mispchallenge/misp2022_baseline/tree/main/track1_AVSD) for audio-visual speaker diarization

---

---

## 🚀 Self-Build Pipeline

## Stage 1: Multilingual Multi-Scenario Media Acquisition

To obtain diverse source media for speaker diarization, we first define a multilingual and multi-scenario taxonomy and then perform keyword-based retrieval and crawling on public online platforms.

You may first need to prepare your own API access or platform-specific crawling configuration.

```bash
python local/search_video.py
```

## Stage 2: Data Cleaning and Preprocessing

This stage corresponds to the **data cleaning and preprocessing** stage in the paper, including:

- shot detection and segmentation
- audio/video extraction and normalisation
- audio-visual synchronisation detection
- face detection and tracking
- lip ROI extraction

### Step 2.1: Scene Detection and Segmentation

To improve the reliability of downstream face detection and tracking, long videos are first segmented into shorter clips with relatively stable visual scenes.

```bash
python local/segment.py
```

### Step 2.2: Audio/Video Extraction and Normalisation

Use FFmpeg to extract and normalise audio/video streams.

> In our released pipeline, the audio stream is normalised to **44.1 kHz**, and the visual stream is normalised to **720p / 25 fps**.

You can implement this step with FFmpeg-based scripts according to your local setup.

### Step 2.3: Audio-Visual Synchronisation Detection

This step filters out clips with severe audio-visual mismatch, dubbing, or off-screen narration, and keeps clips with reliable speaking-face correspondence.

```bash
bash syncnet_python-master/download_model.sh
python syncnet_python-master/run.py
python local/synv_result.py
```

### Step 2.4: Face Detection, Tracking, and Lip ROI Extraction

To support audio-visual speaker diarization, high-quality face trajectories and lip ROIs are extracted from synchronised clips.

We use:

- **RetinaFace** for face detection
- **DeepSORT** for face tracking
- **MediaPipe** for lip landmark localisation and ROI extraction

```bash
cd retinaface_pytorch
# Download the RetinaFace model files according to the README
cd ..
pip3 install deep-sort-realtime
pip install mediapipe
cd deep-person-reid
python setup.py develop
cd ..
python local/face_detection_tracking.py
```

## Stage 3: Cross-Modal Consistency-Guided Annotation

After obtaining synchronised waveforms and lip ROI sequences, we generate speaker diarization annotations through a cross-modal consistency-guided framework consisting of three pre-trained branches:

- **VOSD**: visual-only speaker diarization
- **AOSD**: audio-only speaker diarization
- **AVSD**: audio-visual speaker diarization

The core idea is to use cross-modal consistency as a quality-control signal.

- When both modalities are reliable, the three branches are expected to produce consistent predictions.
- When one modality is degraded, the AVSD branch is expected to align more closely with the more reliable unimodal branch.
- When the AOSD and VOSD branches agree but the AVSD branch conflicts, the sample is treated as a low-confidence cross-modal inconsistency and routed to **selective manual verification**.

In our implementation:

- AOSD can be built on [3D-Speaker](https://github.com/modelscope/3D-Speaker)
- VOSD and AVSD can be built on the [MISP AVSD baseline](https://github.com/mispchallenge/misp2022_baseline/tree/main/track1_AVSD)

Researchers can also replace these systems with stronger alternatives.

## 📝 Notes

- This repository mainly focuses on the **dataset construction pipeline** rather than model training.
- Some scripts may require users to manually modify file paths and environment settings.
- Availability of source videos depends on the status of the original platforms.
- The released RTTM file contains the **final annotations** used in the dataset release.

