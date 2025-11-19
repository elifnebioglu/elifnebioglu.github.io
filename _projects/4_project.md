---
layout: page
title: Prompt-Based Song Recommendation System
description: NLP; sentiment analysis; LLM fine-tuning; music recommendation
img: assets/img/3.jpg
importance: 4
category: lab
---

This project is my **undergraduate capstone thesis**, in which I designed a system that recommends songs based on **free-form user prompts**.  
The system integrates **sentiment analysis**, **LLM-based semantic modeling**, and a lightweight web application.  
The work combines classical NLP techniques with modern large-language-model fine-tuning.

Full thesis (English version) coming soon.

Some key points:

- Developed a **prompt-based recommendation pipeline** that interprets user input semantically and emotionally.  
- Performed **sentiment and mood analysis** using ChatGPT and structured prompt engineering.  
- Fine-tuned **Mistral-7B** on a custom dataset to better align music descriptors with user intentions.  
- Built a responsive **React + JavaScript** web interface to deliver real-time recommendations.  
- Evaluated system accuracy via human feedback and mood–song consistency metrics.

---

## System Overview

The system aims to understand *how a user feels* or *what atmosphere they want*, and translate this into a playlist.  
The architecture includes:

- A **frontend interface** for natural-language queries  
- A backend that performs **sentiment classification**, **embedding extraction**, and **semantic matching**  
- A fine-tuned **Mistral-7B** model that improves alignment between prompts and music descriptors  
- A curated feature-rich **song database** with genre, mood, and lyrical metadata

<div class="row justify-content-sm-center">
    <div class="col-sm-7 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/grad_project/architecture.png" title="System architecture" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    High-level structure of the prompt-based music recommendation system.
</div>

---

## Sentiment & Semantic Understanding

The system analyzes user text using:

- **Sentiment polarity** (positive, neutral, negative)  
- **Emotional tone** (e.g., calm, energetic, melancholic)  
- **Contextual cues** (e.g., “studying”, “night walk”, “heartbreak”, “dance vibe”)  
- **LLM-generated embeddings** for deeper semantic meaning

These features are combined to rank songs based on **mood compatibility** and **semantic similarity**.

---

## Fine-Tuning Mistral-7B

To improve performance beyond zero-shot LLMs, we fine-tuned **Mistral-7B** on:

- song descriptions  
- mood tags  
- emotional keywords  
- user-prompt–song relevance pairs  

Fine-tuning helped the model:

- generate more accurate song descriptors  
- better understand ambiguous or metaphorical prompts  
- avoid over-generic recommendations

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/grad_project/fine_tuning.png" title="Fine-tuning process" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Fine-tuning pipeline for aligning LLM outputs with music metadata.
</div>

---

## Web Interface

A simple React web interface allows users to:

- Type any natural-language request  
- View recommended tracks instantly  
- Explore song mood attributes and metadata  
- Interact with a clean, mobile-responsive UI

The interface communicates with the backend through a lightweight API.

---

## Results

- Human evaluators rated recommendations as **highly aligned** with the intended mood.  
- The fine-tuned model improved **semantic matching accuracy** compared to baseline models.  
- The system performed especially well for:  
  - emotional prompts (e.g., “I feel nostalgic”)  
  - situation-based prompts (e.g., “songs for a snowy evening”)  
  - activity-based prompts (e.g., “focus music”)  

Limitations include dataset size and the need for more diverse musical genres.

---

## Discussion & Future Work

Future improvements include:

- Expanding the song dataset with audio-based features  
- Adding multi-modal embeddings (lyrics + audio)  
- Incorporating reinforcement learning from user feedback  
- Deployment as a public web app or Spotify plugin  

This project demonstrates how **LLMs, sentiment analysis, and classical recommendation methods** can be combined to build intuitive, mood-aware interactive systems.

---
