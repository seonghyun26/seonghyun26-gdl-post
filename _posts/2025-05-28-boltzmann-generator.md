---
layout: distill
title: Boltzmann distributions and generative models
description: generative models for learning the boltzmann distribution
tags: geometric_deep_learning
giscus_comments: true
date: 2025-05-28
featured: true
mermaid:
  enabled: true
  zoomable: true
code_diff: true
map: true
chart:
  chartjs: true
  echarts: true
  vega_lite: true
tikzjax: true
typograms: true

authors:
  - name: Seonghyun Park


bibliography: 2025-05-28-bg.bib

# Optionally, you can add a table of contents to your post.
# NOTES:
#   - make sure that TOC names match the actual section names
#     for hyperlinks within the post to work correctly.
#   - we may want to automate TOC generation in the future using
#     jekyll-toc plugin (https://github.com/toshimaru/jekyll-toc).
toc:
  - name: Introduction
  - name: Molecular systems and the Boltzmann distribution
    subsection:
    - name: Alanine Dipeptide
  - name: Boltzmann generators
    subsection:
    - name: Training by Energy
    - name: Training by Example
    - name: Invertible Networks (Normalizaing Flows)
  - name: Transferable Boltzmann Generators
    subsection:
    - name: Flow matching
    - name: Full atom Coordinate Generation
    - name: Transferability among Dipeptides
  - name: Conclusion

# Below is an example of injecting additional post-specific styles.
# If you use this post as a template, delete this _styles block.
_styles: >
  .fake-img {
    background: #bbb;
    border: 1px solid rgba(0, 0, 0, 0.1);
    box-shadow: 0 0px 4px rgba(0, 0, 0, 0.1);
    margin-bottom: 12px;
  }
  .fake-img p {
    font-family: monospace;
    color: white;
    text-align: left;
    margin: 12px 0;
    text-align: center;
    font-size: 16px;
  }
---

## Introduction

Boltzmann Generators (BG) have emerged as powerful generative models that bridge statistical mechanics and deep learning. These innovative tools address the fundamental challenge of sampling molecular configurations efficiently from complex, high-dimensional energy landscapes. In this post, we provide an in-depth overview of molecular systems, the Boltzmann distribution, and how Boltzmann Generators learn and generate configurations adhering to this distribution. Furthermore, we explore recent advancements, particularly Transferable Boltzmann Generators (TBG), highlighting improvements in sampling efficiency and generalizability across diverse molecular systems.

## Molecular systems and the Boltzmann distribution

> Goal: sampling in high-dimensional energy landscapes

Consider a molecular system consisting of $$N$$ atoms, where the configuration of each atom is represented by coordinates $$x \in \mathbb{R}^{3 N}$$. In statistical mechanics, the equilibrium probability of observing a particular configuration  follows the Boltzmann distribution:

$$
  \mu(x) \sim \operatorname{exp} \big(-\frac{U(x)}{k_{B}T}\big)
$$

where each symbol denotes the following.

- $$U(x)$$: the potential energy associated with configuration

- $$k_{B}$$: the Boltzmann constant

- $$T$$: the temperature

Intuitively, configurations with lower energy are more probable, whereas configurations with higher energy are exponentially less likely. While efficiently sampling from this distribution is crucial for understanding molecular behavior, thermodynamics, and reaction dynamics, samples with high energy makes this challenging.

### Alanine Dipeptide

For instance, consider the Ramachandran plot for Alanine Dipeptide, a simplified model peptide frequently studied in molecular dynamics. The Ramachandran plot illustrates energetically favorable regions (valleys) and unfavorable regions (mountains), visually representing the Boltzmann distribution in torsional angle space $$\phi$$ and $$\psi$$.

## Boltzmann generators (BG)

> TL;DR configuration generation with invertible networks, trained with energy and samples.

Boltzmann generators (BG) learns the Boltzmann distribution with the following main three components:

- training by energy
- training by example
- inveritble network

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/bg.jpg" title="boltzmann generators" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Overview of Boltzmann generators
</div>


### Training by Energy

Boltzmann Generators leverage the explicit energy function $$U(x)$$ to guide learning. The training objective often involves minimizing the Kullback–Leibler divergence between the generated distribution and the true Boltzmann distribution, expressed as:

$$
    \mathcal{L}_{KL} = \mathbb{E}_{z \sim q_{z}} \Big[
        \beta U(F(z)) - \operatorname{log}\operatorname{det}\Big\vert
            \frac{F(z)}{z}
        \Big\vert
    \Big]
$$

where $$\beta$$ denotes the inverse temperature, i.e., $$\beta = 1 / (k_{B}T)$$.

### Training by Example

To overcome the limitations of energy-based training alone (such as mode collapse), BGs often integrate example-based training using molecular dynamics or Monte Carlo samples. This hybrid approach ensures that the model adequately captures multiple low-energy configurations and effectively covers the energy landscape.

### Invertible Networks (Normalizing Flows)

At the heart of BGs lies the concept of normalizing flows, invertible neural networks capable of efficiently transforming simple latent distributions into complex, high-dimensional molecular configurations. By maintaining invertibility, these networks allow explicit probability calculations necessary for accurate sampling and reweighting.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/RealNVP.jpg" title="boltzmann generators" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Overview of Boltzmann generators
</div>

### Experiments

BGs presents configuration generation on xxx.

## Transferable Boltzmann Generators (TBG)

> TL;DR Full atom coordinate generation with flow matching, transferable between dipeptides.

Transferable Boltzmann Generators extend the capabilities of traditional Boltzmann Generators by incorporating innovations that improve sampling efficiency and enable generalization across multiple molecular systems. Specifically, TBG introduces:

- Flow matching
- Full atom coordinate generation
- Transferablility among dipeptides

### Flow matching

Previously, the boltzmann generator used an ``RealNVP'' to learn the flow. Extending from this, TBG replaces this process with flow matching.

### Full atom Coordinate Generation

Boltzman generators have used ``internal coordinates'', i.e., atom-wise distance and torsion angles, for representations.

### Transferability among Dipeptides

Another notable contribution of TBGs is the transferability among dipeptides.

## Conclusion

Boltzmann Generators (BG) represent a transformative integration of statistical mechanics and deep learning, addressing critical challenges in efficient sampling from high-dimensional molecular energy landscapes. Continuous advancements such as Transferable Boltzmann Generators (TBG) further enhance the efficiency, accuracy, and generalizability of these powerful models. As these methodologies evolve, researchers gain increasingly robust tools to explore complex molecular behaviors, profoundly impacting fields like computational chemistry, drug discovery, and materials science.