---
layout: page
title: Overlapping Community Detection in Complex Networks
description: SLPA, COPRA, Graph Neural Networks; LFR benchmark; overlapping communities
#img: assets/img/ocd_cover.png
importance: 4
category: lab
---

This project investigates **overlapping community detection** in complex networks, extending classical label-propagation algorithms and benchmarking them against modern Graph Neural Networks (GNNs).  
The work was conducted as part of the *Probabilistic Graphical Models / Network Analysis* coursework, jointly with Elif Nebioglu and Marius Michetti. The report is available here: 
#[[pdf]](../assets/pdf/PGM_report.pdf){:target="_blank"}.

Some key points:

- Implemented **SLPA** and **COPRA**, two widely used overlapping label-propagation algorithms.
- Designed a **Graph Autoencoder–based GNN** with Bayesian Gaussian Mixture clustering to infer overlapping memberships.
- Built an extended **LFR benchmark generator** to simulate realistic overlapping networks.
- Evaluated methods using **ONMI** and **Omega Index**, analyzing robustness under varying overlap densities and cluster sizes.

---

### Implementing Classical Algorithms: SLPA and COPRA

We first re-implemented two foundational overlapping community detection algorithms:

#### **SLPA (Speaker–Listener Label Propagation Algorithm)**  
SLPA propagates labels through stochastic speaker–listener interactions. Each node maintains a memory of received labels, forming **probabilistic multi-community assignments**.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ocd/slpa_scheme.png" title="SLPA Process" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The SLPA dynamics allow nodes to accumulate multiple community labels.
</div>

#### **COPRA (Community Overlap Propagation Algorithm)**
COPRA assigns *belonging coefficients* to each community and enforces a limit on the number of memberships per node. The algorithm is lightweight but sensitive to parameter tuning.

Both algorithms were implemented from scratch using modular, object-oriented designs for efficiency and clarity.

---

### A Modern Approach: Graph Neural Network for Overlapping Communities

To test whether deep learning can outperform classical propagative methods, we implemented a **Graph Autoencoder (GAE)** with a two-layer **GCN encoder**, trained to reconstruct the adjacency matrix.

After training, node embeddings were passed into a **Bayesian Gaussian Mixture Model (BGMM)** to obtain *soft, overlapping* community assignments.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ocd/gnn_architecture.png" title="GNN Architecture" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Our GNN pipeline: GCN encoder → latent embeddings → BGMM for overlapping memberships.
</div>

Although the model captured structural information, it struggled to outperform SLPA on synthetic benchmarks, especially as overlap density increased.

---

### Benchmarking with the Overlapping LFR Generator

We extended the **Lancichinetti–Fortunato–Radicchi (LFR)** benchmark to support overlapping communities.  
Key parameters tested:

- Number of nodes: 250–1000  
- Overlap proportion \( O_n = 0.1,\ 0.5 \)  
- Max memberships per node \( O_m = 2,\ldots,8 \)  
- Mixing parameter \( \mu = 0.1,\ 0.3 \)

We evaluated algorithms using:

- **Overlapping Normalized Mutual Information (ONMI)**
- **Omega Index**

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ocd/nmi_results.png" title="NMI Results" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Performance trends across varying overlap densities using the ONMI metric.
</div>

---

### Key Findings

- **SLPA** remains the strongest classic algorithm for low-to-moderate overlap networks.
- **COPRA** suffers from instability and parameter sensitivity.
- **GNN-based methods**, surprisingly, did **not** outperform classical algorithms on synthetic LFR networks. Training time also increased significantly with graph size.
- Increasing the number of overlapping memberships \( O_m \) sharply degrades performance for all methods.

---

### Conclusion

This project highlights the strengths of traditional label-propagation algorithms in overlapping community detection, particularly on synthetic LFR benchmarks.  
While GNNs promise flexibility, our implementation showed that deep models require further architectural refinement and domain-specific design to surpass classical methods.

Future work includes exploring:

- Attention-based GNNs  
- Dynamic edge weighting  
- Hybrid SLPA–GNN pipelines  

---
