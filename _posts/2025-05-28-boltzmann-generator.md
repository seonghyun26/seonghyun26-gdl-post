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
  - name: Molecular systems and the Boltzmann distribution
  - name: Boltzmann generators
  - name: Transferable Boltzmann Generators
    - subsection:
      - name: Flow matching

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

This post goes over the basic of Boltzmann distribution and generative models learning them.

## Molecular systems and the Boltzmann distribution

In this post, we consider a molecular systems consisting of $$N$$ atoms, where the positiosn of each atoms is denoted as $$ x \in \mathbb{R}^{3N} $$.

The Boltzmann distribution refers to the probability distribution as follows:

$$
  \mu(x) \sim \operatorname{exp} \big(-\frac{U(x)}{k_{B}T}\big)
$$

where $$ U(x)$$ denotes the potential energy of the system, $$k_{B}$$ the Boltzmann constant, and $$T$$ the temperature. Intuitively, molecular systems having low energy are likely to be observed than the ones having a high energy.


## Boltzmann generators (BG)

Boltzmann generators (BG) learns the Boltzmann distribution with the following main three components:

- training by energy
- training by example
- inveritble network

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/bg.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
</div>

## Transferable Boltzmann Generators (TBG)

The Transferable Boltzmann Generators (TBG) extend boltzmann generators on two aspects:

- Flow matching
- Full atom coordinate generation
- Transferablility among dipeptides

### Flow matching

Previously, the boltzmann generator used an ``RealNVP'' to learn the flow. Extending from this, TBG replaces this process with flow matching.