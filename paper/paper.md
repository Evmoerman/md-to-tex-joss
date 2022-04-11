---
title: 'Interface to high-performance periodic coupled-cluster theory calculations with atom-centered, localized basis functions'
tags:
  - Fortran
  - Materials science
  - Quantum chemistry
  - High-performance
  - Periodic Coupled Cluster
authors:
  - name: Evgeny Moerman
    affiliation: 1
  - name: Felix Hummel
    affiliation: 2
  - name: Andreas Grüneis
    affiliation: 2
  - name: Andreas Irmler
    affiliation: 2
  - name: Matthias Scheffler 
    affiliation: 1
affiliations:
 - name: The NOMAD Laboratory at the Fritz Haber Institute of the Max Plank Society, Berlin, Germany
   index: 1
 - name: Institute for Theoretical Physics, TU Wien, Vienna, Austria
   index: 2
date: 10 February 2021
bibliography: paper.bib
---

# Summary

A major part of computational materials science and 
computational chemistry concerns calculations of
total energy differences and electronic excitations of poly-atomic systems. 
Currently, the most prevalent method for such 
computations is density-functional theory [@Hohenberg:1964] 
(DFT) based on the Kohn-Sham formalism [@Kohn:1965] (KS)
together with one of the numerous exchange-correlation (xc) 
approximations [@Civalleri:2012; @Sousa:2007]. 
The trade-off between comparably low 
computational cost and often reliably accurate results 
render it the dominant method in the field.
Often, however, the accuracy of the xc approximations 
is not sufficent and uncertain, 
in particular when electronic correlations play a 
decisive role [@Savin:2014; @Zhang:2016; @Zhang:2019].  
Coupled-cluster (CC) methods [@Cizek:1966], while substantially more 
computationally expensive have proven to be significantly more 
accurate and reliable, at least for molecules and non-metallic solids.
On these grounds, they allow for a systematic accuracy benchmark of
other methods.
Indeed, in molecular quantum chemistry the CC approach is considered
the gold standard for a theoretical description of binding energies and
electronic properties [@Chan:2019]. It is typically used (at least)
for benchmark studies.
While the original CC methodology allows to calculate the groundstate total energy
of a system, it is also possible to compute properties of excited states using the 
equation-of-motion formalism of CC (EOM-CC) with comparable accuracy and reliability [@Stanton:1993].
The conventional CC approach is limited, however, to systems with about 50 electrons [@Feller:2001;@Gyevi:2019].
By employing approximations, which exploit the locality of electronic correlation, materials with a couple
of hundreds of electrons have been calculated via local natural orbital CC (LNO-CC) [@Nagy:2019] and
explicitly correlated pair natural orbital CC (PNO-CC-F12) [@Ma:2021].
The extension of CC to periodic systems [@Hirata:2004] has been explored to a great degree
by the research groups 
of [Andreas Grüneis](http://cqc.itp.tuwien.ac.at/code.html) and [Garnet Chan](https://pyscf.org/).

In the work of Andreas Grüneis et al. the periodic formulation of CC was, for example, applied to 
study the adsorption behavior and surface chemistry of 2-dimensional materials 
including graphene [@Al:2017], boron-nitride [@Brandenburg:2019] and surfaces [@Tsatsoulis:2018].
These studies showed that CC yields consistently accurate 
adsorption energies and reaction energetics. 
The work of the groups of Garnet Chan and Timothy Berkelbach 
addressed the electronic properties of 2- and 3-dimensional materials. 
By applying the periodic equation-of-motion (EOM) CC formalism, band paths, optical spectra and band gaps
of various materials (diamond, silicon, nickel oxide and others)  
have been investigated [@Mcclain:2017; @Gao:2020; @Wang:2020].

It is important to note that all the periodic CC calculations published so far 
utilized pseudopotentials [@Kresse:1999] to largely disregard the effect of core electrons.
One notable distinction between the works of the two groups, is the type of basis set used. 
While the [CC4S](http://cqc.itp.tuwien.ac.at/code.html) code of Andreas Grüneis et al. uses a plane-wave basis,
the group of Garnet Chan perform their calculations using gaussian basis sets [@Mcclain:2017; @Sun:2018]. 
Localized atom-centred basis sets like gaussian orbitals or 
numerical atomic orbitals (NAO) [@Blum:2009] can potentially decrease the computational cost. This is mostly
due to the locality of the basis functions and their improved description of the atomic core region, 
which decreases the number of basis functions necessary for accurate computations of the system. 

This paper describes a generalizable interface, called ``CC-aims``, to the ``Coupled-Cluster for solids`` code
([CC4S](http://cqc.itp.tuwien.ac.at/code.html)) developed by Andreas Grüneis et al.
The interface is formulated in a general way and demonstrated here for 
the all-electron FHI-aims code [@Blum:2009]. A generalization to other codes with atom-centered 
basis functions is straight forward. 
This interface expands the power of electronic-structure theory codes to a variety of 
correlated methods. 
These include Møller-Plesset perturbation theory to second order (MP2), coupled-cluster theory
including single and double excitations (CCSD) and CCSD including the perturbative treatment of triple excitations
(CCSD(T)). Implementations of EOM-CC for neutral (EE-EOM-CC) and charged (IP-EOM-CC/EA-EOM-CC) 
excitations are currently under development.
``CC-aims`` can be used directly by any software package which uses a localized basis set and 
employs a resolution-of-identity scheme [@Ren:2012] (RI). 
On the one hand, this includes local RI schemes, which expand products of atomic orbital basis 
function (AOs) pairs only using auxiliary basis functions which are localized on 
either of the atoms of the AOs. Primary examples for this family of localized schemes 
is the  RI-LVL approach employed by FHI-aims [@Ihrig:2015], ADF [@Forster:2020] 
and ABACUS [@Lin:2020] and the the pair-atomic RI (PARI) [@Merlot:2013]. On the other hand, more conventional
non-local schemes, which are predominantly used in molecular calculations, 
like RI-V [@Whitten:1973] and RI-SVS [@Feyereisen:1993] are recognized by CC-aims as well. 


# Statement of need
The only input quantity of CC4S, which is not typically calculated in quantum chemistry or 
electronic structure codes, but which is computed by ``CC-aims``, is the Coulomb vertex [@Hummel:2017]. 
The Coulomb vertex, a rank-3 tensor,  constitutes a memory-saving, approximation 
of the rank-4 tensor of Coulomb integrals.
The storage of Coulomb integrals is the major memory bottleneck in CC calculations, 
so that the utilization of the Coulomb vertex expands the scope of system sizes which can be calculated.
While in the case of localized orbitals the herein presented CC-aims interface can be used, 
in the case of plane-wave basis sets a different approach to calculate the Coulomb 
vertex has to be taken, which is described in [@Hummel:2017]. 
For Quantum chemistry programs employing a localized basis additionally 
the use of an RI scheme is needed or needs to be implemented. 
``CC-aims`` allows software packages which either lack certain Quantum chemistry 
algorithms completely or which only offer insufficiently optimized 
implementations easy access to these methods.
Interfaces like ``CC-aims`` will substantially accelerate the research done in areas,
where DFT is too inaccurate or too unreliable, by allowing many electronic structure codes
to participate in these investigations without the time-consuming effort of implementing
correlated wave-function methods. 

# Acknowledgement 
This work received funding from the European Union's Horizon 2020 Research and Innovation Programme 
(grant agreement No. 951786, the NOMAD CoE), and the ERC Advanced Grant TEC1P (No. 740233)

# References

