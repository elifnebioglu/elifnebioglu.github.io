layout: page
title: Plug-and-Play Split Gibbs Sampler for Inverse Imaging
description: diffusion models; Bayesian inference; plug-and-play; inverse problems
img: assets/img/3.jpg
importance: 4
category: ml
---

This project investigates the **Plug-and-Play Split Gibbs Sampler (PnP-SGS)** for solving inverse imaging problems, using **Denoising Diffusion Probabilistic Models (DDPMs)** as deep generative priors.  
The work was completed jointly with Bruna Lopes, as part of a course project on generative models.  
Full report available here: [[pdf]](../assets/pdf/Generative_models.pdf){:target="_blank"}.

Some key points:

- Implemented the full PnP-SGS framework enabling **posterior sampling and uncertainty quantification**.
- Compared DDPM-based prior with other denoisers such as **DnCNN**, analyzing robustness and failure modes.
- Evaluated performance on **CelebA** and **brain tumor MRI** datasets for deblurring tasks.
- Benchmarked against **Diffusion Posterior Sampling (DPS)** and observed stronger fidelity to ground truth.
- Provided visualizations of reconstruction quality, FID/PSNR metrics, and sampling trajectories for both x-space and z-space.

---

## Plug-and-Play Split Gibbs Sampler with DDPM Priors

The project focuses on embedding DDPMs into a **Bayesian hierarchical model** by introducing an auxiliary variable \(z\). This formulation allows alternating sampling:

- Sampling \(x\) from a **Gaussian conditional posterior** \(p(x\mid y,z)\)
- Sampling \(z\) using the **DDPM reverse process** as a stochastic denoiser

<div class="row justify-content-sm-center">
    <div class="col-sm-7 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/generative_models/pnp_sgs_diagram.png" title="PnP-SGS architecture" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Bayesian structure of PnP-SGS with alternating sampling over x and z.
</div>

Main contributions:

- Reproduced the PnP-SGS results for **Gaussian and motion blur**.
- Demonstrated that DDPM priors enable stable posterior sampling across varying noise scales.
- Showed that PnP-SGS preserves **Bayesian uncertainty advantages** while improving reconstruction sharpness.

---

## Experiments on CelebA and Brain Tumor MRI

The framework was tested on two datasets:

- **CelebA** (face images)
- **Brain Tumor MRI** (medical imaging), resized to \(256\times256\)

Evaluation included **PSNR** and visual comparison of x- and z-chains during sampling.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/generative_models/mri_deblur.png" title="MRI deblurring" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Sample reconstruction on a brain MRI image using PnP-SGS.
</div>

Findings:

- PnP-SGS achieved **PSNR values comparable to reported FFHQ results**.
- The DDPM prior struggled with structural features absent from the training distribution (e.g., cortical folds in MRI), suggesting potential benefits from fine-tuning.

---

## Comparison with Diffusion Posterior Sampling (DPS)

A detailed comparison was conducted between **PnP-SGS** and **DPS**:

- PnP-SGS produced reconstructions **closer to ground truth**, with fewer hallucinated details.
- DPS was faster but introduced artifacts in textured regions.
- PnP-SGS provided **natural uncertainty quantification**, unlike DPS.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/generative_models/dps_compare.png" title="DPS vs PnP-SGS" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Comparison of DPS reconstructions vs. PnP-SGS.
</div>

---

## Using Alternative Priors (DnCNN)

To test plug-and-play flexibility, the DDPM prior was replaced with **DnCNN**.  
Observations:

- DnCNN diverged at **higher noise levels**, due to its fixed-noise training regime.
- PnP-SGS remained stable with DDPM but required parameter adjustments for DnCNN.
- A hybrid approach (PnP-PGD initialization → PnP-SGS) slightly improved robustness.

---
