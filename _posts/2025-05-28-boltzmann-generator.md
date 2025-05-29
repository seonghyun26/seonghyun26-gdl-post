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
  - name: Transferable Boltzmann generators

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
  \mu(x) \sim \operatorname{exp}(-\frac{U(x)}{k_{B}T})
$$

where $$ U(x)$$ denotes the potential energy of the system, $$k_{B}$$ the Boltzmann constant, and $$T$$ the temperature. Intuitively, molecular systems having low energy are likely to be observed than the ones having a high energy.


## Boltzmann generators

Finally, the Boltzmann generators (BGs)




## Transferable Boltzmann generators

The transferable Boltzmann generators

