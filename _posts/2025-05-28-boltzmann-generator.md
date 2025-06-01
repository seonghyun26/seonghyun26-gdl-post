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
  - name: Sequential Boltzmann Generators
    subsection:
    - name: Efficient Non-Equivariant Transformer-Based Flows
    - name: Annealed Langevin Dynamics and Sequential Monte Carlo
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

Consider a molecular system consisting of $N$ atoms, where the configuration of each atom is represented by coordinates $x \in \mathbb{R}^{3 N}$. In statistical mechanics, the equilibrium probability of observing a particular configuration  follows the Boltzmann distribution:

$$
  \mu(x) \sim \operatorname{exp} \big(-\frac{U(x)}{k_{B}T}\big)
$$

where each symbol denotes the following.

- $U(x)$: the potential energy associated with configuration

- $k_{B}$: the Boltzmann constant

- $T$: the temperature

Intuitively, configurations with lower energy are more probable, whereas configurations with higher energy are exponentially less likely. While efficiently sampling from this distribution is crucial for understanding molecular behavior, thermodynamics, and reaction dynamics, samples with high energy makes this challenging.

### Alanine Dipeptide

For instance, consider the Ramachandran plot for Alanine Dipeptide, a simplified model peptide frequently studied in molecular dynamics. The Ramachandran plot illustrates energetically favorable regions (valley, dark blue) and unfavorable regions (mountains, yellow), visually representing the Boltzmann distribution in torsional angle space $\phi$ and $\psi$.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/aldp_c7a2.png" title="aldp_c7ax" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ram.jpg" title="ram" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    3D visualization and the Ramachandran plot of Alanine Dipeptide.
</div>

## Boltzmann generators (BG)

> TL;DR configuration generation with invertible neural networks, trained with energy and samples.

Boltzmann generators (BG) learns the Boltzmann distribution with the following main three components:

- training by energy
- training by example
- inveritble network

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/bg.png" title="boltzmann generators" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Overview of Boltzmann generators
</div>


### Training by Energy

Basically, Boltzmann Generators leverage the explicit energy function $U(x)$ to guide learning. The training objective minimizes the Kullback–Leibler (KL) divergence between the generated distribution and the true Boltzmann distribution, expressed as:

$$
    \mathcal{L}_{KL} = \mathbb{E}_{z \sim q_{z}} \Big[
        U(F_{zx}(z)) - \operatorname{log}\operatorname{det}\Big\vert
            \frac{\partial F_{zx}(z)}{\partial z}
        \Big\vert
    \Big]
$$

where
<!-- - $\beta$: the inverse temperature, i.e., $\beta = 1 / (k_{B}T)$ -->
- $z$: latent variable from a simple known distribution, e.g., Gaussian
- $F_{zx}()$: the invertible neural network, transforming latent variables $z$ into real space configurations $x$

In short, BG generates real world configurations $x$ from latent variables $z$, and makes the distribution of the generates samples $p(x)$ similar to the Boltzmann distribution $q(x)$ computed with the potential energy function $U(x)$.

Another interpretation of the loss term is that the first term is the mean potential energy, i.e., the internal energy of the system. On the other hand, the second term is equal to the entropic contribution to the free energy. However, desptive the entropy term, simple training by energy has some troubles. It results in sampling most stable meta-stable states rather than multiple ones.

### Training by Example

To overcome this limitations of energy-based training alone (such as mode collapse), BGs often integrate example-based training. BG obatins some `valid' configurations, i.e., samples achieved by short MD simulations or experimental structures and transform them to the latent space by $z = F_{xz}(x)$. From here, it also maimizes the likelihood with the Gaussian distribution by minimizing the following:

$$
    \mathcal{L}_{ML} = \mathbb{E}_{x} \Big[
        \frac{1}{2} \Vert F_{xz}(x) \Vert^2 - \operatorname{log}\operatorname{det}\Big\vert
            \frac{\partial F_{xz}(x)}{\partial x}
        \Big\vert
    \Big]
$$

Here, the first term is the energy of a harmonic oscillaor in the Guassian prior distribution, while the second term again refers to the entropic contribution. By training a moodel with energy and example, we can sample configurations that have high probabilities and loew free energies. In the following, we now introduce the invertible network $F_{xz}()$ and $F_{zx}()$ in order to convert samples between the latent space and the real space.

### Invertible Networks (Normalizing Flows)

At the heart of BGs lies the concept of normalizing flows, invertible neural networks capable of efficiently transforming simple latent distributions into complex, high-dimensional molecular configurations back and forth. 

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/RealNVP.png" title="real nvp" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Overview of RealNVP
</div>

To be specific, the RealNVP splits the representation into two channels, i.e., $x=(x_1, x_2)$ and $z=(z_1, z_2)$. Additionally, a neural network $S$ and $T$ with parameters $\theta$ each for scaling and translates the second input channel using the input of the first channel as follows:

$$
    \begin{aligned}
    y_1 &= x_1 \\
    y_2 &= x_2 \odot \operatorname{exp}(S_\theta(x_1)) + T_\theta(x_1)
    \end{aligned}
$$

and does the same for swapping the channels

$$
    \begin{aligned}
    z_1 &= y_2 \\
    z_2 &= y_1 \odot \operatorname{exp}(S_\theta(y_2)) + T_\theta(y_2)
    \end{aligned}
$$

This two stacked RealNVP layers maps a real space configuration to the latent space. To map back from the latent space to the real space,

$$
    \begin{aligned}
    x_1 &= z_1 \\
    x_2 &= \Big(
        z_2 - T_{\theta}(x_1) \odot (- S_\theta (z_1))
    \Big)
    \end{aligned}
$$

and vice versa by swapping the channels.

### Experiments

BGs presents configuration generation on both synthetic systems and real world molecular systems. For synthetic systems, they consider two dimensional model potentials: the double well potential and Mueller potential.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/bg_dw.png" title="bg_dw" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/bg_muller.png" title="bg_muller" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Two-dimensional potentials: the double well and mueller
</div>

Two short MD simulation trajectories were used for training (blue, red) stay in their metastable states without crossing, while transition state and intermediate state ensembles are shown (orange) but were not used for training. Generated samples are evaluated through the free energy surface, projected to the x-axis.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/bg_dw_fe.png" title="bg_dw" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/bg_muller_fe.png" title="bg_muller" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Evaluation of samples generated in two-dimensional system with the Boltzmann Generators.
</div>

In the figure above, the black line refers to the ground truth obtained by molecular dynamics, while the green line indicates the values obtained by the Boltzmann Generators. One can see that the samples from BG matches the free energy surface to the ground truth.

## Transferable Boltzmann Generators (TBG)

> TL;DR Full atom coordinate generation with flow matching, transferable between dipeptides.

Transferable Boltzmann Generators extend the capabilities of traditional Boltzmann Generators by incorporating innovations that improve sampling efficiency and enable generalization across multiple molecular systems. Specifically, TBG introduces:

- Flow matching
- Full atom coordinate generation
- Transferablility among dipeptides

### Flow matching

BGs has used RealNVP-based architectures for normalizing flows. Furthermore, TBG introduces flow matching, a simulation free and efficient training method to learn the target distribution. To be specific, it matches a parameterized vector field to a target conditional vector field $u_t(x \vert z)$ as follows:

$$
    \mathcal{L}_{CFM}(\theta) = \mathbb{E}_{t \sim [0, 1], x \sim p_t(x \vert z)} \Big[ \Vert
        f_{t, \theta}(t, x) - u_t(x \vert z)
    \Vert\Big]^2_2
$$
- $f_{t, \theta}(t, x)$: parameterized vector field graudlly transforming the representation
- $u_t(x \vert z)$: target conditional vector field, computed by the time interpolation between the samples and noise

Now, another difference from the BG is the backbone model they used.

### Full atom Coordinate Generation

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/egnn.png" title="egnn" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Concept of EGNN, where the rotation applied to inputs are applied to outputs correspondingly.
</div>

While earlier BGs often utilized ''internal coordinate'', i.e., atom-wise distances and torsion angles, for input representations, TBG directly generates 'full atom coordinates'. This is possible due to choice of the backbone model used for the vector field, Equivariant Graph Neural Networks (EGNN). EGNN is a S(3) equivariant neural network, meaning that rotations to the input will be also coorrespondingly appear in the output. Eventually, without and internal coordinates TBG successfully generates atom coordiantes.

### Transferability among Dipeptides

Another advacnement TBGs demonstrates is the trasferability between dipeptides. By embedding chemical and structural information within the network architecture, TBGs successfully predict equilibrium distributions for new, unseen molecular systems without additional training. The training set consist of 200 dipeptides, where the test includes 100 dipeptides unseen in the training set.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/tbg_result.png" title="tbg_result" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Results on the KS dipeptide for TBG. Using more data results in distributions closer to the groud truth.
</div>

The evaluations of generated samples are two-fold. Qualitatively, the Ramachandran plot of molecular dynamics and TBG are compared. Quantitatively, the energy distribution between the samples from molecular dynamics and TBG are compared. As one can see above, TBG successfully generates samples in both quantitative and qualitative manners.

## Sequential Boltzmann Generators

Sequential Boltzmann Generators (SBG) further advance BG scalability and efficiency, employing two key improvements:

- Non-equivariant flows
- Annealed Langevin Dynamics

### Efficient Non-Equivariant Transformer-Based Flows

SBGs use highly efficient non-equivariant Transformer-based normalizing flows operating directly on Cartesian coordinates. Unlike equivariant continuous flows, these architectures are exactly invertible, offering efficient sample generation and likelihood computation, enabling sophisticated inference strategies.

### Annealed Langevin Dynamics and Sequential Monte Carlo

SBGs utilize inference-time annealed Langevin dynamics to transport initial proposal samples  towards the target Boltzmann distribution. This is governed by the stochastic differential equation:

$$
    \mathrm{d}t = 
$$

where  interpolates between the proposal energy  and the target Boltzmann energy , and  is a standard Wiener process.

The evolution of importance weights  through this process is given by Jarzynski's equality:


At the final step , these adjusted samples and importance weights significantly enhance sampling fidelity, enabling scalable equilibrium sampling even in larger peptide systems previously intractable for standard BGs.

## Conclusion

Boltzmann Generators (BG) represent a transformative integration of statistical mechanics and deep learning, efficiently addressing the sampling challenge from high-dimensional molecular energy landscapes. Advances like Transferable Boltzmann Generators (TBGs) and Sequential Boltzmann Generators (SBGs) significantly enhance efficiency, accuracy, and generalizability. As these methodologies evolve, they provide robust tools for exploring complex molecular behaviors, profoundly impacting computational chemistry, drug discovery, and materials science.