---
layout: page
title: Empirical Bayes VAEs Excess Risk Bounds
description: VAEs; empirical Bayes; excess risk theory; generative models
img: assets/img/3.jpg
importance: 4
category: lab
---

This project explores **Empirical Bayes Variational Autoencoders (EBVAEs)** through both theory and practice.  
We study the excess risk bounds introduced by Tang & Yang (2021), and experimentally evaluate how learning an expressive latent prior and decoder noise parameter affects VAE performance.  


Some key points:

- Reproduced and interpreted the **oracle-style excess risk bounds** for EBVAEs.  
- Implemented four VAE variants to test the effect of **learning the latent covariance** and **decoder noise parameter**.  
- Demonstrated that **learning the noise parameter improves EBVAE loss**, even on MNIST.  
- Analyzed reconstruction loss, KL divergence, and generative quality across the four EBVAE variants.

---

## Empirical Bayes Variational Autoencoders (EBVAEs)

EBVAEs generalize standard VAEs by **learning the prior distribution** π(z) instead of assuming a fixed Gaussian.  
This empirical Bayes viewpoint allows encoder, decoder, and prior parameters to be optimized jointly:

\[
(\hat p, \hat q, \hat \pi) = \arg\min_{p,q,\pi} \frac{1}{n}\sum_{i=1}^n m(p,q,\pi,x_i)
\]

The theoretical work provides a **high-probability excess risk bound** that quantifies the gap between:

- the empirical minimizer of the EBVAE loss, and  
- the population-level optimal model.

<div class="row justify-content-sm-center">
    <div class="col-sm-7 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/bayesian_dl/ebvae_theory.png" title="EBVAE theoretical structure" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The EBVAE framework augments VAEs with a learned prior, enabling tighter theoretical guarantees.
</div>

Key theoretical insights:

- Learning the prior helps reduce **approximation error**.  
- Model complexity (encoder/decoder/prior families) controls **estimation error** via Rademacher complexity.  
- For expressive enough neural families and smooth data distributions, the **approximation error can vanish**.

---

## Applications to Gaussian VAEs

We examine the theory in the case where:

- the encoder outputs a full covariance Gaussian,  
- the decoder outputs a quantized Gaussian with learned σ,  
- and the prior belongs to a Gaussian family satisfying regularity conditions.

Under these assumptions, the paper shows:

- The EBVAE estimator achieves a **log(n)-rate excess risk bound**.
- Learning the full covariance matrix can reduce approximation error but may hurt estimation error if overly complex.

---

## Experiments on MNIST

We implement **four types of EBVAEs**, each resembling a standard VAE but with different learned components:

1. **Standard VAE** (diagonal latent covariance, fixed decoder noise)  
2. **Learned latent covariance**  
3. **Learned decoder noise parameter σ**  
4. **Learned covariance + learned noise**

To speed up computation, images are downsampled to **14×14**, and models are trained on **1000 samples**.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/bayesian_dl/ebvae_reconstruction.png" title="EBVAE Reconstructions" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Reconstructions produced by four EBVAE variants.
</div>

Findings:

- **Learning the noise parameter** improves reconstruction quality and EBVAE loss.  
- **Learning the full covariance** slightly improves KL divergence but worsens EBVAE loss.  
- VAEs with learned covariance tend to produce **overly similar samples** due to latent space irregularities.

---

## Results & Discussion

<div class="row justify-content-sm-center">
    <div class="col-sm-7 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/bayesian_dl/ebvae_metrics.png" title="Reconstruction & KL metrics" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Reconstruction loss and KL divergence for the four EBVAE configurations.
</div>

Summary:

- Best EBVAE loss: **model with learned noise**  
- Best KL divergence: **model with learned covariance**  
- Best generative quality: **standard VAE** and **noise-learning VAE**

These results support the theoretical insight that **both the covariance structure and decoder noise affect the estimation/approximation trade-off**.  

Future directions:

- Testing on more complex datasets  
- Trying non-Gaussian priors (e.g., mixtures)  
- Investigating regularization strategies to stabilize learned covariance models

---
