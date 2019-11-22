class: middle, center, title-slide
count: false

# An introduction to pyhf

<br>
[Matthew Feickert](https://www.matthewfeickert.com/)<br>(for the dev team)<br><br>
University of Illinois at Urbana-Champaign

<br><br>
[LHCb Statistics Forum](https://talk-event-url)

November 25th, 2019

---
# pyhf team

<br><br>

.grid[
.kol-1-4.center[
.circle.width-80[![Lukas](figures/collaborators/heinrich.jpg)]

[Lukas Heinrich](https://github.com/lukasheinrich)

CERN
]
.kol-1-4.center[
.circle.width-80[![Matthew](https://avatars2.githubusercontent.com/u/5142394)]

[Matthew Feickert](https://www.matthewfeickert.com/)

Illinois
]
.kol-1-4.center[
.circle.width-80[![Giordon](https://avatars0.githubusercontent.com/u/761483)]

[Giordon Stark](https://github.com/kratsg)

UCSC SCIPP
]
.kol-1-4.center[
.circle.width-70[![Kyle](figures/collaborators/cranmer.png)]

[Kyle Cranmer](http://theoryandpractice.org/)

NYU
]
]

.kol-3-4.center.bold[Core Developers]
.kol-1-4.center.bold[Advising]

---
# Recent Scikit-HEP project

.kol-1-2.left[
.bold[Scikit-HEP:]<br>
A community-driven and community-oriented project with the aim of providing Particle Physics at large with an ecosystem for data analysis in Python

<br><br><br><br><br><br>
.bold[pyhf:]<br>
A pure-python implementation of the widely-used HistFactory p.d.f. template described in [CERN-OPEN-2012-016](http://inspirehep.net/record/1236448)
]
.kol-1-2.center[
.width-50[[![scikit-hep_logo](https://scikit-hep.org/assets/images/logo.png)](https://scikit-hep.org/)]

.width-70[[![pyhf_logo](https://iris-hep.org/assets/logos/pyhf-logo.png)](https://scikit-hep.org/pyhf/)]
]

---
# Enter HistFactory

- A flexible p.d.f. template to build statistical models from binned distributions and data
- Developed by Cranmer, Lewis, Moneta, Shibata, and Verkerke ([CERN-OPEN-2012-016](http://inspirehep.net/record/1236448))
- Widely used by the HEP community for standard model measurements and BSM searches
   <!-- - Show public summary plots and link to references that use HistFactory (multi b-jets for example) -->

.kol-1-1.center[
.width-100[![HistFactory_uses](figures/HistFactory_result_examples.png)]
]

---
# HistFactory Template

$$
f\left(\vec{n}, \vec{a}\middle|\vec{\eta}, \vec{\chi}\right) = \color{blue}{\prod\_{c \\,\in\\, \textrm{channels}} \prod\_{b \\,\in\\, \textrm{bins}\_c} \textrm{Pois} \left(n\_{cb} \middle| \nu\_{cb}\left(\vec{\eta}, \vec{\chi}\right)\right)} \\,\color{red}{\prod\_{\chi \\,\in\\, \vec{\chi}} c\_{\chi} \left(a\_{\chi}\middle|\chi\right)}
$$

$$
\nu\_{cb}(\vec{\eta}, \vec{\chi}) = \sum\_{s \\,\in\\, \textrm{samples}} \underbrace{\left(\sum\_{\kappa \\,\in\\, \vec{\kappa}} \kappa\_{scb}(\vec{\eta}, \vec{\chi})\right)}\_{\textrm{multiplicative}} \Bigg(\nu\_{scb}^{0}(\vec{\eta}, \vec{\chi}) + \underbrace{\sum\_{\Delta \\,\in\\, \vec{\Delta}} \Delta\_{scb}(\vec{\eta}, \vec{\chi})}\_{\textrm{additive}}\Bigg)
$$

.bold[Use:] Multiple disjoint _channels_ (or regions) of binned distributions with multiple _samples_ contributing to each with additional (possibly shared) systematics between sample estimates

.bold[Main pieces:]
- .blue[Main Poisson p.d.f. for simultaneous measurement of multiple channels]
- .katex[Event rates] $\nu\_{cb}$ from nominal rate $\nu\_{scb}^{0}$ and rate modifiers $\kappa$ and $\Delta$
- .red[Constraint p.d.f. (+ data) for "auxiliary measurements"]
   - encoding systematic uncertainties (normalization, shape, etc)
- $\vec{n}$: events, $\vec{a}$: auxiliary data, $\vec{\eta}$: unconstrained pars, $\vec{\chi}$: constrained pars

---
# HistFactory Template

$$
f\left(\vec{n}, \vec{a}\middle|\vec{\eta}, \vec{\chi}\right) = \prod\_{c \\,\in\\, \textrm{channels}} \prod\_{b \\,\in\\, \textrm{bins}\_c} \textrm{Pois} \left(n\_{cb} \middle| \nu\_{cb}\left(\vec{\eta}, \vec{\chi}\right)\right) \prod\_{\chi \\,\in\\, \vec{\chi}} c\_{\chi} \left(a\_{\chi}\middle|\chi\right)
$$
<br>

.center[.bold[This is a _mathematical_ representation!] Nowhere is any software spec defined]

.center[Until now, the only implementation of HistFactory has been in RooStats+RooFit]

<br>

- Preservation: Likelihood stored in the binary ROOT format
   - Challenge for long-term preservation (i.e. HEPData)
   - Why is a histogram needed for an array of numbers?
- To start using HistFactory p.d.f.s first have to learn ROOT, RooFit, RooStats
   - Problem for our theory colleagues (generally don't want to)
- Difficult to use for reinterpretation

---
# `pyhf`: HistFactory in pure Python
<!--  -->
.kol-1-2.width-95[
- First non-ROOT implementation of the HistFactory p.d.f. template
   - .width-40[[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.1169739.svg)](https://doi.org/10.5281/zenodo.1169739)]
- pure-Python library as second implementation of HistFactory
  - [`pip install pyhf`](https://scikit-hep.org/pyhf/installation.html#install-from-pypi)
  - No dependence on ROOT!
]
.kol-1-2.center.width-80[
[![pyhf_logo](https://iris-hep.org/assets/logos/pyhf-logo.png)](https://scikit-hep.org/pyhf/)
]
<!--  -->
.kol-1-1[
- Has a JSON spec that .blue[fully] describes the HistFactory model
   - JSON: Industry standard, parsable by every language, human & machine readable, versionable and easily preserved (HEPData is JSON)
- Open source tool for all of HEP
   - Originated from a [DIANA/HEP](https://diana-hep.org/) project fellowship and now an [IRIS-HEP](https://iris-hep.org/projects/pyhf.html) supported Scikit-HEP project
   - Used for reinterpretation in phenomenology paper (DOI: [10.1007/JHEP04(2019)144](https://inspirehep.net/record/1698425))
   - Used internally in ATLAS for pMSSM SUSY large scale reinterpretation
]

---
# Example pyhf JSON spec

.center[<a href="https://carbon.now.sh/?bg=rgba(255%2C255%2C255%2C1)&t=seti&wt=none&l=application%2Fjson&ds=false&dsyoff=20px&dsblur=68px&wc=true&wa=true&pv=3px&ph=1px&ln=false&fl=1&fm=Hack&fs=14px&lh=133%25&si=false&es=4x&wm=false&code=%257B%250A%2520%2520%2520%2520%2522channels%2522%253A%2520%255B%2520%2523%2520List%2520of%2520regions%250A%2520%2520%2520%2520%2520%2520%2520%2520%257B%2520%2522name%2522%253A%2520%2522singlechannel%2522%252C%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2522samples%2522%253A%2520%255B%2520%2523%2520List%2520of%2520samples%2520in%2520region%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%257B%2520%2522name%2522%253A%2520%2522signal%2522%252C%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2522data%2522%253A%2520%255B5.0%252C%252010.0%255D%252C%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2523%2520List%2520of%2520rate%2520factors%2520and%252For%2520systematic%2520uncertainties%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2522modifiers%2522%253A%2520%255B%2520%257B%2520%2522name%2522%253A%2520%2522mu%2522%252C%2520%2522type%2522%253A%2520%2522normfactor%2522%252C%2520%2522data%2522%253A%2520null%257D%2520%255D%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%257D%252C%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%257B%2520%2522name%2522%253A%2520%2522background%2522%252C%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2522data%2522%253A%2520%255B50.0%252C%252060.0%255D%252C%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2522modifiers%2522%253A%2520%255B%2520%257B%2522name%2522%253A%2520%2522uncorr_bkguncrt%2522%252C%2520%2522type%2522%253A%2520%2522shapesys%2522%252C%2520%2522data%2522%253A%2520%255B5.0%252C%252012.0%255D%257D%2520%255D%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%257D%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%255D%250A%2520%2520%2520%2520%2520%2520%2520%2520%257D%250A%2520%2520%2520%2520%255D%252C%250A%2520%2520%2520%2520%2522observations%2522%253A%2520%255B%2520%2523%2520Observed%2520data%250A%2520%2520%2520%2520%2520%2520%2520%2520%257B%2520%2522name%2522%253A%2520%2522singlechannel%2522%252C%2520%2522data%2522%253A%2520%255B50.0%252C%252060.0%255D%2520%257D%250A%2520%2520%2520%2520%255D%252C%250A%2520%2520%2520%2520%2522measurements%2522%253A%2520%255B%2520%2523%2520Parameter%2520of%2520interest%250A%2520%2520%2520%2520%2520%2520%2520%2520%257B%2520%2522name%2522%253A%2520%2522Measurement%2522%252C%2520%2522config%2522%253A%2520%257B%2522poi%2522%253A%2520%2522mu%2522%252C%2520%2522parameters%2522%253A%2520%255B%255D%257D%2520%257D%250A%2520%2520%2520%2520%255D%252C%250A%2520%2520%2520%2520%2522version%2522%253A%2520%25221.0.0%2522%2520%2523%2520Version%2520of%2520spec%2520standard%250A%257D">`JSON` defining a single channel, two bin counting experiment with systematics</a>]

.center.width-80[![demo_JSON](figures/carbon_JSON_spec_annotated.png)]

---
# Live demo time!

<br><br>

.center.bold[Just click the button!]<br><br><br>

.center.width-70[[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/matthewfeickert/talk-LHCb-Stats-Forum/master?urlpath=lab)]

---
# $CL_{s}$ Example using `pyhf` CLI

.center.width-60[![demo_CLI](figures/carbon_CLI_output.png)]

<!-- --- -->
<!-- # Lukas note -->
<!--  -->
<!-- - Change the model to give more interesting CLs values -->
<!-- - c.f. slide 18 from Lukas Higgs Combination talk -->

---
# JSON Patch for new signal models
<!--  -->
.kol-1-2[
<br>
.center.width-90[![demo_JSON](figures/carbon_JSON_spec_short.png)]
.center[Original model]
]
.kol-1-2[
<!-- <br> -->
.center.width-90[![patch_file](figures/carbon_patch.png)]
.center[New Signal (JSON Patch file)]
]
.kol-1-1[
.center.width-60[![demo_JSON](figures/carbon_patched_JSON.png)]
.center[Reinterpretation]
]

---
# JSON Patch for new signal models
<!--  -->
.center.width-60[![signal_reinterpretation](figures/carbon_reinterpretation.png)]
.kol-1-2[
.center.width-50[![measurement_cartoon](figures/measurement_cartoon.png)]
.center[Original analysis (model A)]
]
.kol-1-2[
.center.width-50[![reinterpretation_cartoon](figures/reinterpretation_cartoon.png)]
.center[Recast analysis (model B)]
]

---
# Likelihoods preserved on HEPData

- Background-only model JSON stored
- Signal models stored as JSON Patch files
- Together are able to fully preserve the full model (with own DOI! .width-20[[![DOI](https://img.shields.io/badge/DOI-10.17182%2Fhepdata.89408.v1%2Fr2-blue.svg)](https://doi.org/10.17182/hepdata.89408.v1/r2)] )

[.center.width-70[![HEPData_likelihoods](figures/HEPData_likelihoods.png)]](https://www.hepdata.net/record/ins1748602)

---
# ...can be streamed from HEPData

- Background-only model JSON stored
- Signal models stored as JSON Patch files
- Together are able to fully preserve the full model (with own DOI! .width-20[[![DOI](https://img.shields.io/badge/DOI-10.17182%2Fhepdata.89408.v1%2Fr2-blue.svg)](https://doi.org/10.17182/hepdata.89408.v1/r2)] )

.center.width-80[![HEPData_streamed_likelihoods](figures/carbon_HEPData_streamed_likelihoods.png)]

---
# Likelihood serialization and reproduction
<!--  -->
- ATLAS PUB note on the JSON schema for serialization and reproduction of results ([ATL-PHYS-PUB-2019-029](https://cds.cern.ch/record/2684863))
   - Contours: .root[█] original ROOT+XML, .pyhf[█] pyhf JSON, .roundtrip[█] JSON converted back to ROOT+XML
<!--  -->
.right.width-80[
[![flowchart](figures/process.png)](https://cds.cern.ch/record/2684863)
]

---
# Likelihood serialization and reproduction
<!--  -->
- ATLAS PUB note on the JSON schema for serialization and reproduction of results ([ATL-PHYS-PUB-2019-029](https://cds.cern.ch/record/2684863))
   - Contours: .root[█] original ROOT+XML, .pyhf[█] pyhf JSON, .roundtrip[█] JSON converted back to ROOT+XML
      - Overlay of contours nice visualization of near perfect agreement
   - Serialized likelihood and reproduced results of ATLAS Run-2 search for sbottom quarks ([CERN-EP-2019-142](http://inspirehep.net/record/1748602)) and published to HEPData
   - Shown to reproduce results but faster! .bold[ROOT:] 10+ hours .bold[pyhf:] < 30 minutes

.kol-1-2.center.width-95[
[![overlay_multiplex_contour](figures/overlay_multiplex_contour.png)](https://cds.cern.ch/record/2684863)
]
.kol-1-2.right.width-70[
[![discrepancy](figures/discrepancy.png)](https://cds.cern.ch/record/2684863)
]

---
# Summary
<!--  -->
Through pyhf are able to provide:
<!--  -->
- .bold[JSON specification] of likelihoods
   - human/machine readable, versionable, HEPData friendly, orders of magnitude smaller
- .bold[Bidirectional translation] of likelihood specifications
   - ROOT workspaces ↔ JSON
- Independent .bold[pure-Python implementation] of HistFactory + hypothesis testing
- Publication for the first time of the .bold[full likelihood] of a search for new physics

.kol-1-2.center.width-100[
[![likelihood_publishing_agreement](figures/likelihood_publishing_agreement.png)](https://cds.cern.ch/record/411537)
([1st Workshop on Confidence Limits, CERN, 2000](http://inspirehep.net/record/534129))
]
.kol-1-2.center.width-85[
[![PUB_note_cover](figures/PUB_note_cover.png)](https://cds.cern.ch/record/2684863)
([ATLAS, 2019](https://cds.cern.ch/record/2684863))
]

---
class: end-slide, center

Backup

---
# ROOT + XML to JSON and back

.center.width-70[![flowchart](figures/process.png)]

---
# Best-fit parameter values

.center.width-70[![fit_results](figures/fit_results.png)]

---
# JSON Patch files for new signal models
<br>

```
$ pyhf cls example.json | jq .CLs_obs
0.053994246621274014
```
```
$ cat new_signal.json
[{
    "op": "replace",
    "path": "/channels/0/samples/0/data",
    "value": [10.0, 6.0]
}]
```
```
$ pyhf cls example.json --patch new_signal.json | jq .CLs_obs
0.3536906623262466
```

---
# Likelihoods can be streamed from HEPData
<br>

```
# One signal model
$ curl -sL https://doi.org/10.17182/hepdata.89408.v1/r2 | \
  tar -O -xzv RegionA/BkgOnly.json | \
  pyhf cls --patch <(curl -sL https://doi.org/10.17182/hepdata.89408.v1/r2 | \
    tar -O -xzv RegionA/patch.sbottom_1300_205_60.json) | \
  jq .CLs_obs
0.2444363799054463
```

```
# A different signal model
$ curl -sL https://doi.org/10.17182/hepdata.89408.v1/r2 | \
  tar -O -xzv RegionA/BkgOnly.json | \
  pyhf cls --patch <(curl -sL https://doi.org/10.17182/hepdata.89408.v1/r2 | \
    tar -O -xzv RegionA/patch.sbottom_1300_230_100.json) | \
  jq .CLs_obs
0.040766026035752724
```

---
# References

1. F. James, Y. Perrin, L. Lyons, .italic[[Workshop on confidence limits: Proceedings](http://inspirehep.net/record/534129)], 2000.
2. ROOT collaboration, K. Cranmer, G. Lewis, L. Moneta, A. Shibata and W. Verkerke, .italic[[HistFactory: A tool for creating statistical models for use with RooFit and RooStats](http://inspirehep.net/record/1236448)], 2012.
3. L. Heinrich, H. Schulz, J. Turner and Y. Zhou, .italic[[Constraining $A_{4}$ Leptonic Flavour Model Parameters at Colliders and Beyond](https://inspirehep.net/record/1698425)], 2018.
4. ATLAS collaboration, .italic[[Search for bottom-squark pair production with the ATLAS detector in final states containing Higgs bosons, b-jets and missing transverse momentum](http://inspirehep.net/record/1748602)], 2019
5. ATLAS collaboration, .italic[[Reproducing searches for new physics with the ATLAS experiment through publication of full statistical likelihoods](https://cds.cern.ch/record/2684863)], 2019
6. ATLAS collaboration, .italic[[Search for bottom-squark pair production with the ATLAS detector in final states containing Higgs bosons, b-jets and missing transverse momentum: HEPData entry](https://www.hepdata.net/record/ins1748602)], 2019

---

class: end-slide, center
count: false

The end.
