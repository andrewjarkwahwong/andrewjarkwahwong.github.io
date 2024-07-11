---
layout: post
title: The Grand Canonical dilemma of using default DFT simulation to model  Electrocatalysis
date: 2024-07-10 11:12:00-0400
description: Default DFT simulations are inherently Canonical simulations. Electrocatalysis (real life) is Grand Canonical. Here we discuss how to correct DFT simulations to represent Grand Canonical Simulations.
tags: GC-DFT DFT basics electrocatalysis math
categories: GC-DFT
related_posts: false
featured: true
---

# Overview

The goal of this blog post is to explain the issue with "default" DFT simulations and how (analytical) Grand Canonical DFT can provide steps closer to achieving "more realistic simulations" for electrocatalysis
# Problems with DFT Simulations as Models for Electrocatalysis

DFT simulations have been instrumental in studying wide range of physics, chemistry, and material science problems. However, determining appropriate DFT simulations for electrocatalysis is challenging. In particular, we will introduce the dilemma that DFT simulations are inherently Canonical simulations (where the number of electrons is constant and the potential is varied) but the electrochemical system we are trying to model is a Grand Canonical (where the number of electrons are varied and the potential is constant). I will briefly introduce the dilemma as well as provide both an electrostatic and thermodynamic derivation to correct DFT energies to Grand Canonical energies. Hopefully, you understand the dilemma and how to approach it. 

## The Dilemma: Constant Charge to Constant Potential
DFT simulations can be seen as a "snapshot" of different ground-energy states along a reaction path. Consider a simple reaction of $$Na^+_{(g)}$$  adsorbing onto a metal surface. This reaction can be written as:

$$ Na^+_{(g)} + * \rightarrow Na* + e^-  $$

where the free energy change as a function of the applied potential on an absolute scale, $$U_{abs}$$ can be defined as:

$$\Delta G_{ads}=G_{e^-}+G_{Na*}-G_{*}-G_{Na^+}$$

$$\Delta G_{ads}(U_{abs})=-|e|U_{abs}+G_{Na*}-G_{*}-G_{Na^+}$$

where solvation is currently neglected. This seems straightforward to calculate as only the free energy of Na*, the bare surface, and the $$Na^+$$ in the gas-phase are needed. However, the dilemma of modeling this in a DFT cell is the fact that the simulations are not simulated at the constant and correct potential. This is an artifact of the DFT simulation being constant charge (constant number of electrons) rather than being constant potential. This dilemma is illustrated in the figure below.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="/assets/img/ConstantqvsV.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

As seen above, the initial state (the bare metal surface with $$Na^+$$ in the gas-phase) is at the potential of zero charge of the bare metal in vacuum ($$U_{pzc,*}$$ $$\approx$$ 0.5 V-SHE). 

When we adsorb Na onto the metal, Na donates electrons to the metal. This changes the energy of the electron (fermi energy) and thus, the measured $$U_{pzc,Na*}$$ $$\approx$$ -0.7 V-SHE.

This is an issue as 1) the two states of interest are not compared at the same potentials (not apples-to-apples) and 2) they are not necessarily the potential of interest. This is purely an artifact of the DFT simulation being a canonical simulation.

In real life in an actual electrochemical experiment, the potential of these two states or the metal surface is kept at a constant potential by varying the number of electrons. The potentiostat is the tool that essentially injects/removes electrons on the surface to maintain a specific potential. Here, the DFT simulation needs a "computational potentiostat" to correct the simulation to a specific potential as shown in Figure b, essentially modeling a Grand Canonical simulation.
# Grand Canonical Density Functional Theory

The dilemma is defined but what are the solutions to convert a Canonical DFT simulation to an Grand Canonical simulation? Here, I discuss two ways to understandThen, we briefly discuss the fundamentals and a brief introduction of Grand Canonical DFT methods.

## The Fundamentals of Corrections to Constant Potential
### Electrostatic Explanation

A given reaction state,$$\lambda$$, can be given in a generalized form and assigned at a potential of zero charge, $$U_{pzc,\lambda}$$.  This is seen above where the potential of the bare metal surface is $$U_{pzc,*}$$ $$\approx$$ 0.5 V-SHE and the potential of the Na* is $$U_{pzc,*}$$ $$\approx$$ -0.7 V-SHE, where $$\lambda$$ is respectively * and Na*. 

Here, we can generally write the corrected Grand Canonical energy of a reaction state, $$\Omega_{\lambda}(U)$$,  as:

$$\Omega_{\lambda}(U) =G_{\lambda}(U) = G_{\lambda}(U_{pzc,\lambda})+E_{charging}$$

where $$G_{\lambda}(U_{pzc,\lambda})$$ is the free energy of reaction state from the "default" Canonical DFT simulation and $$E_{charging}$$ is the energy to charge the reaction state to the correct potential, $$U$$. From electrostatics, $$E_{charging}$$ is the defined in the general form as:

$$ E_{charging} = -\int_{q=0}^{q(U)}\frac{q}{C}dq=-\int_{q=0}^{q(U)}Udq$$

where $$q$$ is the charge and $$C$$ is the capacitance. This can be written with the previous equation as:

$$\Omega_{\lambda}(U) = G_{\lambda}(U_{pzc,\lambda})-\int_{q=0}^{q(U)}Udq$$

where if we knew how the potential difference (voltage) changes as we charge the reaction state, we can correctly charge the reaction state of interest from  $$U_{pzc,\lambda}$$ to $$U$$.

### Thermodynamic Explanation

We cheated a bit before by assuming the energy of the canonical simulation is the Gibbs Free energy. In short, we basically assumed the Helmholtz Free energy and the Gibbs Free energy to be similar because we assume the internal energy,$$E$$, and enthalpy,$$H$$, are equivalent:
$$H=E+PV $$
where the constant pressure is assumed.

Here, we will use a more thermodynamically correct term of the Helmholtz Free Energy to represent the thermodynamic potential energy of a canonical simulation which is defined as:
$$ F = E - TS$$
where $$E$$ is the internal energy, $$T$$ is the temperature, and $$S$$ is the entropy. The Helmholtz free energy is the thermodynamic potential of a system at thermodynamic equilibrium from which all $$T$$ and volume,$$V$$ ,are constant. More importantly, the number of electrons or $$N$$ are constant.

A Grand Canonical simulation is a system at thermodynamic equilibrium
where T and $$V$$ are constant but the chemical potential of the electrons,$$\mu$$, is now constant (or N is non-constant). The Grand Canonical Free energy is given by the general Legendre Transform as:

$$ \Omega(y) = F(x)-xy$$

where for our case it is defined as:

$$\Omega(\mu) = F(N)-\mu N$$ 

Here, we converted the Canonical energy to a Grand Canonical energy by performing a legendre transform. The chemical potential represents the change in energy to add or subtract number of electrons. The last term represents the energy to gain or lose electrons as the number of electrons are changed. Once again, if we knew how the chemical potential of the electron changes as we change the number of electrons, we know how to calculate the Grand Canonical Energy.

If we take the equation we derived previously based on electrostatics, we can write the following expression that looks quite similar as:

$$\Omega(\mu) = F(N)-\mu N \approx G_{\lambda}(U_{pzc,\lambda})-\int_{q=0}^{q(U)}Udq$$

where here is the relationship between electrostatics and thermodynamics again if we (confusingly) assume $$F(N)\approx G_{\lambda}(U_{pzc,\lambda})$$. Hopefully, this alternative explanation has some help in understanding how we can obtain Grand Canonical Free Energies.  FYI, the negative term indicates the adding electrons decreases the energy of the system.

# Explicit vs Analytical Potentiostats

From the derivations, the dilemma remains that we need an approximation of how the approximate the energy to charge a reaction state to a specific potential either from  $$-\int_{q=0}^{q(U)}Udq$$ or $$-\mu N$$. These forms are generally unknown and there are two ways to approximate these terms. 

First is to explicitly model a potentiostat to approximate the energy to charge the system. I will not go too in depth and further readings are at the end. In a nutshell, there simulation methods that essentially inject or remove electrons in the DFT simulation to correct to the $$U$$ defined. If you know how many electrons you added or remove, you know $$N$$ and you can approximate $$\mu$$ by your description of how the energy cost of adding electrons in vacuum or in a media in the DFT cell that represents the electrolyte. Thus, you can explicitly model DFT simulations at different potentials without the simulation failing or crashing. The disadvantage of these methods are its computational cost and the approximation of the electrolyte that is ill-defined experimentally, which dictations $$\mu$$ and $$N$$ essentially.

The second way is the analytically determine the charging energy. If we assume a potential-independent capacitance, we can derive the energy to charge our DFT reaction state as:

$$ E_{charging} = -\int_{q=0}^{q(U)}\frac{q}{C}dq=-\frac{1}{2}\frac{q^2}{C}=-\frac{1}{2}C(U-U_{pzc,\lambda})^2$$

where now the energy to charge a DFT reaction state to a specific potential based on a potential-independent capacitance is:

$$\Omega_{\lambda}(U) = G_{\lambda}(U_{pzc,\lambda})-\frac{1}{2}C(U-U_{pzc,\lambda})^2$$

Here, we can calculate the "correct" Grand Canonical potential of any state if we knew the capacitance. This can be estimated experimentally or from simulations. If you wanted to use an expression of a potential-dependent capacitance, you just have to rederive the integral. The advantage of this approach is that you can analytically (without coding into a Fortran VASP simulation) evaluate the Grand Canonical DFT energy. Of course, careful treatment must be considered as this assume that the reaction state at $$U_{pzc,\lambda}$$ remains unperturbed at $$U$$, which may not be true. 
# Outlook and Further Readings

Here we overviewed the dilemma of "default" DFT simulations to model Grand Canonical or Constant potential simulations. We derived both the theory to calculate Grand Canonical DFT simulations and introduced approaches that addresses these dilemmas. To be honest, I left out a lot of well-deserved theory and derivations as well as great work due to 1) keeping this concise and 2) partially I was a lil lazy.

For further readings, consider the following:
- [This paper has great derivations of what I discussed and explicit DFT approaches.](https://pubs.aip.org/aip/jcp/article/157/18/180902/2842002/Electrochemistry-from-the-atomic-scale-in-the)
- [This work also compares between different Grand Canonical DFT approaches](https://doi.org/10.1002/cphc.202300950)
- [This is our Analytical DFT paper](https://doi.org/10.1016/j.jcat.2024.115360)
- [This paper compares explicit and analytical Grand Canonical DFT approaches](https://pubs.acs.org/doi/10.1021/acs.jctc.3c00836)): 
