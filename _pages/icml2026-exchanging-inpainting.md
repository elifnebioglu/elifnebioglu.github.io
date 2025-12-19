---
layout: page
permalink: /publications/icml2026-exchanging-inpainting/
title: Exchanging Inpainting Context
description: Revealing Global-Artifact Bias in AI-Generated Image Detectors
nav: false
---

## Authors

**Elif Nebioğlu**, Emirhan Bilgiç, Adrian Popescu

## Overview

This work exposes a fundamental vulnerability in modern AI-generated image detectors by identifying a global filter-like artifact induced by diffusion-based inpainting. We propose **Inpainting Exchange**, a simple post-processing operation that removes this global artifact while preserving semantic edits, causing state-of-the-art detectors to fail dramatically.

## The Problem

When diffusion-based inpainting edits even a small region, the entire image undergoes iterative denoising, introducing a subtle but consistent global transformation. Many detectors rely heavily on this global artifact rather than the inpainted content itself.

## Our Solution: Inpainting Exchange

Given a real image $x$, its inpainted version $\tilde{x}$, and mask $M$, the exchanged image is:

$$x^{\mathrm{ex}} = M \odot \tilde{x} + (1-M) \odot x$$

This operation removes the global denoising artifact while preserving the edited region.

## Key Contributions

1. We identify and analyze a global filter-like artifact induced by diffusion-based inpainting.
2. We propose Inpainting Exchange, a simple yet effective attack against state-of-the-art AI image detectors.
3. We demonstrate that existing detectors exhibit a strong bias toward global artifacts rather than edited regions.
4. We introduce a 150K-image benchmark consisting of real, inpainted, and exchanged inpainted images.
5. We show that our method also serves as a high-quality qualitative image editing technique.

## Experimental Results

### Commercial Detectors

- **Sightengine**: 87.8% accuracy on standard inpainted images → 54.4% on exchanged images (AUC: 0.96 → 0.61)
- **Hive**: 95% accuracy on standard inpainted images → 55% on exchanged images (AUC: 0.97 → 0.58)

### Open-Source Detectors

- **CrossEfficientViT**: Near-random performance (~50% accuracy, AUC < 0.5)
- **UniversalFakeDetect**: 50% accuracy at default threshold, optimal AUC ~0.71
- **De-fake**: Below 50% accuracy on exchanged dataset

### Training Results

Models trained on our exchanged dataset achieve better generalization (77.95% accuracy, 0.861 AUC) and superior localization (Mean IoU: 0.481) compared to models trained on standard datasets.

## Dataset

We introduce a 150K-image benchmark extending Semi-Truths:
- 50K real images
- 50K standard inpainted images  
- 50K exchanged inpainted images

## Our Claim

AI-generated image detectors are not robust and quite vulnerable to simple attacks, especially when the image is not fully generated but partially generated (or manipulated / inpainted). Even when a small part of the image is inpainted, a "filter" is added to the image, making it obvious that it's AI-generated. So, AI-generated detectors are detecting it as AI-generated, easily. 

Two problems arise:

1. **What if we only inpaint the image's small part and exchange the rest with the original?** So that (i) we would not be detected by detectors, (ii) it would potentially be a better editing method, since the rest of the image wouldn't be affected by inpainting.

2. **The AI-generated image detectors shouldn't label the inpainted images as fully AI-generated, as they do.** They should label them as "manipulated/inpainted" or "partially generated" images. Notably, even the strongest AI-generated image detectors, which differentiate between the inpainted and the fully-generated images, are deceived when we replace the non-inpainted area, and they falsely label those images as fully real.

## Publication

**Status**: Coming Soon  
**Conference**: International Conference on Machine Learning (ICML) 2026

