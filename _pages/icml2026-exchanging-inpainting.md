---
layout: page
permalink: /publications/icml2026-exchanging-inpainting/
title: Exchanging Inpainting Context
description: Revealing Global-Artifact Bias in AI-Generated Image Detectors
nav: false
---

## Authors

**Elif Nebioğlu**, **Emirhan Bilgiç**, Adrian Popescu

*Equal contribution indicated by (*)

## Abstract

Modern AI-generated image detectors achieve strong performance by exploiting artifacts shared across generative models. In this work, we show that for diffusion-based inpainting, a dominant cue is not the edited region itself but a global, filter-like artifact introduced by full-image denoising and autoencoding. We propose a simple post-processing operation, **Inpainting Exchange**, which preserves the inpainted region while restoring all non-edited pixels from the original image, thereby removing the global denoising footprint. This operation dramatically degrades the accuracy of frequency-based and CLIP-based detectors, often approaching chance-level performance. We complement our attack with a mathematical analysis of spectral bias in diffusion models, saliency maps revealing detector attention bias, and a 150K-image benchmark extending Semi-Truths. Our results expose a fundamental vulnerability in current detection paradigms.

## Introduction

AI-generated image detection is a central problem in machine learning. Recent detectors report impressive generalization, but we argue this is often due to **global shortcuts** rather than semantic understanding. In diffusion-based inpainting, even small edits involve full-image denoising and VAE reconstruction, inducing a subtle global transformation. We introduce **Inpainting Exchange** to eliminate this artifact, revealing that many detectors fail when forced to look only at the edited content.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/original.png" class="img-fluid rounded z-depth-1" zoomable=true caption="(a) Original Image" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/semitruths.png" class="img-fluid rounded z-depth-1" zoomable=true caption="(b) Standard Inpainting" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/ourinpainted.png" class="img-fluid rounded z-depth-1" zoomable=true caption="(c) Inpainting Exchange (Ours)" %}
    </div>
</div>
<div class="caption">
    <strong>Figure 1. Overview of Inpainting Exchange.</strong> Standard inpainting (b) introduces a global denoising footprint across the entire image. Our method (c) restores the original pixels outside the mask, isolating the generative edit while looking visually identical to the naked eye.
</div>

## Key Contributions

1. We identify a global filter-like artifact induced by diffusion-based inpainting.
2. We provide a rigorous mathematical proof showing that standard inpainting shifts the spectral distribution of unmasked pixels, while our method minimizes the KL-divergence from the real distribution.
3. We propose Inpainting Exchange, a simple attack that highlights detector reliance on global traces.
4. We demonstrate that existing detectors exhibit a strong bias toward these global artifacts via a 150K-image benchmark.

## Background: Latent Diffusion Inpainting

Let $x \in \mathbb{R}^{H \times W \times 3}$ be a real image and $M \in \{0,1\}^{H \times W}$ be a binary mask where $1$ denotes the region to inpaint.

Latent Diffusion Models (LDMs) operate in a compressed latent space $\mathcal{Z}$. The process involves an encoder $\mathcal{E}$, a decoder $\mathcal{D}$, and a denoiser $\epsilon_\theta$. The inpainted image $\tilde{x}$ is generated via:

$$\tilde{x} = \mathcal{D}(\text{ReverseDiffusion}(\mathcal{E}(x), M))$$

Crucially, even if the latent diffusion process perfectly preserves the unmasked latents, the final image is reconstructed via the decoder $\mathcal{D}(\cdot)$, affecting the entire spatial domain.

<div class="row mt-3">
    <div class="col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/publications/icml2026/main_figure.jpeg" class="img-fluid rounded z-depth-1" zoomable=true caption="Figure 2. Overview of the Inpainting Exchange pipeline. Standard inpainting applies a full-image denoising and VAE reconstruction pass. Our method restores the original pixels outside the mask; the difference map is strictly localized." %}
    </div>
</div>

## Theoretical Analysis

### Spectral Bias in VAE Reconstruction

We model a real image $x \in \mathcal{X}$ as a superposition of a semantic signal $s$ and stochastic sensor noise $n$ (e.g., photon shot noise, PRNU), such that $x = s + n$, where $n \sim \mathcal{N}(0, \Sigma_{noise})$.

**Theorem (Variance Contraction in VAEs):** Let $\mathcal{T}$ be an autoencoder trained to minimize a reconstruction objective dominated by the $L_2$ norm (MSE). Assuming the latent code $z = \mathcal{E}(x)$ effectively captures the semantic signal $s$ but is independent of the stochastic high-frequency noise $n$ due to the information bottleneck, the reconstruction $\tilde{x} = \mathcal{T}(x)$ satisfies:

$$S_{\tilde{x}}(\omega) \le S_x(\omega) \quad \forall \omega \in \Omega_{high}$$

where $\Omega_{high}$ denotes the frequency band dominated by sensor noise $n$, and $S_x(\omega) = \mathbb{E}[|\mathcal{F}[x](\omega)|^2]$ is the expected spectral power spectrum.

**Intuition:** The $L_2$-optimal reconstruction approximates the conditional expectation $\tilde{x} \approx \mathbb{E}[x \mid z]$. Since sensor noise $n$ is stochastic and non-semantic, it is not encoded in $z$, leading to variance contraction in high-frequency bands.

### Inpainting Exchange and Divergence Minimization

**Theorem (Divergence Reduction via Exchange):** Let $P_{std}$ be the distribution of standard inpainted images and $P_{ex}$ be the distribution of images generated via Inpainting Exchange. Under the assumption that standard inpainting introduces a non-zero spectral shift in the background, we have:

$$D_{KL}(P_{real} || P_{ex}) < D_{KL}(P_{real} || P_{std})$$

**Key Insight:** By copying background pixels exactly from the real image, Inpainting Exchange forces $P_{ex}(x_{bg}) \equiv P_{real}(x_{bg})$, reducing the KL divergence to zero for the background region. The total divergence is then minimized to only the conditional divergence of the foreground, whereas standard inpainting suffers from divergence across the entire spatial domain.

## Inpainting Exchange Method

Given a real image $x$, its inpainted version $\tilde{x}$, and mask $M$, the exchanged image is:

$$x^{\mathrm{ex}} = M \odot \tilde{x} + (1-M) \odot x$$

This operation removes the global denoising artifact while preserving the edited region.

<div class="row mt-3">
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/mask.jpeg" class="img-fluid rounded z-depth-1" zoomable=true caption="(a) Mask" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/semi_inp.jpeg" class="img-fluid rounded z-depth-1" zoomable=true caption="(b) Std. GradCAM" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/ours_inp.jpeg" class="img-fluid rounded z-depth-1" zoomable=true caption="(c) Ours GradCAM" %}
    </div>
</div>
<div class="caption">
    <strong>Figure 3. Visualization of detector attention via GradCAM.</strong> Standard inpainting (b) triggers global attention, while ours (c) localizes it.
</div>

## Dataset Construction

We extend the Semi-Truths dataset to construct a **150K-image benchmark** across 4 datasets: CelebA-HQ, Cityscapes, OpenImages, and SUN-RGBD. The dataset consists of:

- 50K real images ($x$)
- 50K standard inpainted images ($\tilde{x}$)
- 50K exchanged inpainted images ($x^{ex}$)

All images are paired with masks, enabling controlled evaluation of detector behavior. Inpainting is performed using 3 models: Kandinsky, OpenJourney, and StableDiffusion_v4.

## Experimental Results

### Performance on Open-Source Detectors

Most open-source detectors struggle with inpainted images, often performing near random chance. Our Inpainting Exchange attack further degrades this performance:

**CrossEfficientViT:**
- Semi-Truths: 50.54% accuracy, 0.6608 F1, 0.4535 AUC
- **Ours (Exchange):** 50.47% accuracy, 0.6600 F1, **0.4456 AUC**

**De-fake:**
- Semi-Truths: 49.85% accuracy, 0.3325 F1, 0.5113 AUC
- **Ours (Exchange):** **47.20% accuracy**, **0.2715 F1**, **0.4214 AUC**

**UniversalFakeDetect** (with optimal threshold tuning):
- Semi-Truths: 66.06% accuracy, 0.7155 AUC
- **Ours (Exchange):** 65.70% accuracy, **0.7089 AUC**

### Commercial API Evaluation

Commercial detectors perform well on standard inpainting but suffer a catastrophic drop under our Inpainting Exchange attack:

| Metric | Sightengine (Semi) | **Sightengine (Ours)** | Hive (Semi) | **Hive (Ours)** |
|--------|-------------------|------------------------|-------------|-----------------|
| Accuracy | 87.80% | **54.40%** | 95.00% | **55.00%** |
| Recall | 0.778 | **0.111** | 0.900 | **0.100** |
| F1-Score | 0.864 | **0.196** | 0.950 | **0.180** |
| AUC-ROC | 0.960 | **0.607** | 0.970 | **0.580** |

*Results on N=1000 images*

This confirms our hypothesis: commercial models likely utilize high-frequency noise analysis (like PRNU or sensor noise logic) which is globally disrupted by the VAE in standard inpainting but restored in our exchange method.

### Model Robustness and Generalization

We trained three detector architectures (ResNet-50, EfficientNet, and ViT) on both standard Semi-Truths images and our "Exchanged" images. Key findings:

**ResNet-50:**
- Training on Semi-Truths → Testing on Semi-Truths: 90.30% accuracy, 0.9681 AUC
- Training on Semi-Truths → Testing on **Ours**: 64.34% accuracy, 0.6632 AUC (significant drop)
- Training on **Ours** → Testing on Semi-Truths: 73.95% accuracy, 0.8085 AUC
- Training on **Ours** → Testing on **Ours**: 77.95% accuracy, **0.8610 AUC**

**EfficientNet:**
- Training on Semi-Truths → Testing on **Ours**: 66.18% accuracy, 0.6044 AUC
- Training on **Ours** → Testing on **Ours**: **78.90% accuracy**, **0.8748 AUC**

**ViT:**
- Training on Semi-Truths → Testing on **Ours**: 65.50% accuracy, 0.6387 AUC
- Training on **Ours** → Testing on **Ours**: 67.20% accuracy, **0.7846 AUC**

**Key Insight:** Models trained on standard data are not robust, failing catastrophically when the global artifact is removed. Training on exchanged images forces the model to focus on features within the mask, improving generalization.

<div class="row mt-3">
    <div class="col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/publications/icml2026/more_localizations.jpeg" class="img-fluid rounded z-depth-1" zoomable=true caption="Figure 6. Additional localization examples showing detector attention via GradCAM. (a) Mask indicates the inpainted region. (b) Standard inpainting triggers global attention across the image. (c) Our Inpainting Exchange method localizes attention to the actual edited region." %}
    </div>
</div>

### Localization Results

Training on "Exchanged" images forces the model to focus on the actual edited region rather than background noise. This results in significantly better localization metrics:

**ResNet-50:**
- Training on Semi-Truths → Testing on Semi-Truths: 0.4246 mIoU, 74.70% pixel accuracy, 0.2941 mAP
- Training on **Ours** → Testing on **Ours**: **0.4812 mIoU**, **81.43% pixel accuracy**, **0.3942 mAP**

**EfficientNet:**
- Training on **Ours** → Testing on **Ours**: **0.4783 mIoU**, **82.47% pixel accuracy**, **0.4072 mAP**

**ViT:**
- Training on **Ours** → Testing on **Ours**: **0.4408 mIoU**, **81.96% pixel accuracy**, **0.2918 mAP**

### Spectral Analysis

To validate our theoretical findings, we analyze the frequency domain differences between real and generated images. We compute the Mean Squared Error (MSE) between the averaged magnitude spectrum of real images and their manipulated counterparts:

<div class="row mt-3">
    <div class="col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/publications/icml2026/fft_analysis.jpeg" class="img-fluid rounded z-depth-1" zoomable=true caption="Figure 4. Frequency domain analysis comparing real images, standard inpainting, and our Inpainting Exchange method. The FFT magnitude spectra reveal the global spectral artifacts introduced by standard inpainting, which are eliminated in our approach." %}
    </div>
</div>

| Dataset | Semi-Truths (MSE × 1000) | **Ours (MSE × 1000)** |
|---------|-------------------------|----------------------|
| CelebA-HQ | 0.2293 | **0.2048** |
| Cityscapes | 0.6980 | **0.2070** |
| OpenImages | 0.5419 | **0.0716** |
| SUN-RGBD | 0.9286 | **0.0775** |

Standard inpainting introduces a significant spectral shift, particularly in complex scene datasets like SUN-RGBD and Cityscapes. Our method drastically reduces this gap.

<div class="row mt-3">
    <div class="col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/publications/icml2026/more_differences.jpeg" class="img-fluid rounded z-depth-1" zoomable=true caption="Figure 5. Comparison of standard inpainting versus Inpainting Exchange. From left to right: Original image, Mask, Standard Inpainting, Difference (Original - Inpainting), Inpainting Exchange (Ours), and Difference (Original - Ours). The difference maps reveal that standard inpainting introduces global artifacts across the entire image, while our method produces differences only within the masked region." %}
    </div>
</div>

## Conclusion

We expose a fundamental vulnerability in AI detectors: a reliance on global denoising artifacts rather than local content. By removing these artifacts via Inpainting Exchange, we show that current detectors are significantly less robust than previously thought. The mathematical analysis confirms that standard inpainting pipelines introduce a global spectral shift, which acts as a shortcut for detection.

Our work motivates the development of more robust, content-aware detection methods that focus on the actual edited regions rather than global artifacts.

## Publication

**Status**: Coming Soon  
**Conference**: International Conference on Machine Learning (ICML) 2026

## Impact Statement

This work highlights security vulnerabilities in AI-generated image detectors. We release our dataset to support the development of more robust, content-aware detection methods.
