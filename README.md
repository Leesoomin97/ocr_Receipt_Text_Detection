팀 레포: https://github.com/AIBootcamp16/d-ocr-3


# Receipt OCR Text Detection  
## High-Recall Production Model

> Final production-ready text detection model optimized for small and dense receipt text

---

## Overview

본 모델은 영수증 이미지에서 **작은 텍스트를 최대한 놓치지 않는 것 (High Recall)**을 목표로 설계되었습니다.

- 작은 텍스트 적극 검출
- Precision 붕괴 방지
- 실제 OCR 파이프라인 적용 가능 수준의 안정성 확보

---

## Design Objective

**Maximize Recall for small & dense receipt text while keeping Precision stable**

영수증 데이터 특성:

- 글자 크기 다양
- 밀집된 텍스트 구조
- 작은 폰트 다수 포함

→ 고해상도 입력 + 검출 민감도 조정이 핵심 전략

---

## Model Architecture

### Backbone
- ResNet-34  
- 깊은 feature extraction을 통해 작은 텍스트 표현력 강화

### Detection Head
- DBNet (Differentiable Binarization)  
- 구조는 유지하고, 입력 해상도 및 후처리 중심으로 성능 개선

---

## Core Improvements

### 1️⃣ High-Resolution Input

| Setting | Value |
|----------|--------|
| Input Size | **1024 × 1024** |

- 작은 글자가 feature map에서 더 명확하게 표현  
- 밀집 텍스트 분리 성능 향상  

---

### 2️⃣ Post-processing Tuning

| Parameter | Value |
|------------|-------|
| box_thresh | **0.25** |
| max_candidates | 500 |
| use_polygon | True |

- 약한 텍스트 신호까지 검출  
- 평가 방식(CLEval)과 완전 일치  

---

### 3️⃣ Loss Re-weighting

| Parameter | Value |
|------------|-------|
| prob_map_loss_weight | **7.0** |

- 텍스트 영역 학습 강화  
- 작은 글자 confidence 부족 문제 완화  

---

## Memory Optimization

고해상도 입력으로 인한 GPU 메모리 부담 해결:

| Setting | Value |
|----------|--------|
| Batch Size | 8 |
| Gradient Accumulation | 2 |
| Effective Batch Size | 16 |

- OOM 방지  
- 학습 안정성 유지  

---

## Data Augmentation

외부 데이터 증강 없이 내부 증강만 사용:

- RandomBrightnessContrast  
- CLAHE  
- RandomRotate90  

- 조명 변화 대응  
- 대비 강화  
- 회전 강건성 확보  

---

## Training Configuration

| Item | Setting |
|------|---------|
| Optimizer | AdamW |
| Scheduler | CosineAnnealingLR |
| Epochs | 20 |

---

## Performance Comparison

### 🔹 Baseline (10 Epoch, Public Score)

```
H-Mean    : 0.8818
Precision : 0.9651
Recall    : 0.8194
```

### 🔹 Final Model (Test)

```
H-Mean    : 0.9599
Precision : 0.9558
Recall    : 0.9659
```

---

## Improvement Analysis

| Metric     | Baseline | Final Model | Δ (Change) |
|------------|----------|------------|------------|
| H-Mean     | 0.8818   | **0.9599** | **+0.0781** |
| Precision  | 0.9651   | 0.9558     | -0.0093 |
| Recall     | 0.8194   | **0.9659** | **+0.1465** |

### Interpretation

- Recall +0.1465 대폭 상승 → 작은 텍스트 검출 능력 크게 개선  
- Precision은 소폭 감소했지만 0.95 이상 유지 → 과검출 문제 통제  
- H-Mean +0.0781 상승 → 전체 검출 성능 향상  

---

## Summary

고해상도 입력과 검출 민감도 튜닝을 통해  
작은 영수증 텍스트 Recall을 극대화한 DBNet 기반 OCR 모델
