class: middle, center, title-slide
count: false

# An introduction to pyhf
# and HistFactory likelihoods

<br>
[Matthew Feickert](https://www.matthewfeickert.com/)<br>(for the dev team)<br><br>
University of Illinois at Urbana-Champaign

<br><br>
[LHCb Statistics Working Group](https://indico.cern.ch/event/863729/contributions/3639915/)

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
<br><br>
.bold[Scikit-HEP:]<br>
A community-driven and community-oriented project with the aim of providing Particle Physics at large with an ecosystem for data analysis in Python

<br><br><br><br><br>
.bold[pyhf:]<br>
A pure-python implementation of the widely-used HistFactory p.d.f. template described in [CERN-OPEN-2012-016](http://inspirehep.net/record/1236448)
]
.kol-1-2.center[
.width-50[[![scikit-hep_logo](https://scikit-hep.org/assets/images/logo.png)](https://scikit-hep.org/)]

.width-70[[![pyhf_logo](https://iris-hep.org/assets/logos/pyhf-logo.png)](https://scikit-hep.org/pyhf/)]
]

---
# HistFactory

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
  - [`$ pip install pyhf`](https://scikit-hep.org/pyhf/installation.html#install-from-pypi)
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
# Machine Learning Frameworks for Computational Backends

.grid[
.kol-2-3[

<br><br>
- All numerical operations implemented in .bold[tensor backends] through an API of $n$-dimensional array operations
- Using deep learning frameworks as computational backends allows for .bold[exploitation of auto differentiation (autograd) and GPU acceleration]
- As huge buy in from industry we benefit for free as these frameworks are .bold[continually improved] by professional software engineers
]
.kol-1-4.right[
.width-100[![NumPy](figures/logos/NumPy_logo.svg)]
.width-100[![PyTorch](figures/logos/Pytorch_logo.svg)]
.width-100[![Tensorflow](figures/logos/TensorFlow_logo.svg)]
]
]

---
# Automatic differentiation

With tensor library backends gain access to _exact (higher order) derivatives_ &mdash; accuracy is only limited by floating point precision

$$
\frac{\partial L}{\partial \mu}, \frac{\partial L}{\partial \theta_{i}}
$$

.grid[
.kol-1-2[

<br><br>
Gain this through the frameworks creating _computational directed acyclic graphs_ and then applying the chain rule (to the operations)
]
.kol-1-2[
.center.width-80[![DAG](figures/directed_acyclic_graph.png)]
.center[Simple example graph]
]
]

.footnote[[Image credit: Alan Du](http://www.columbia.edu/~ahd2125/post/2015/12/5/)]

---
# Automatic differentiation

With tensor library backends gain access to _exact (higher order) derivatives_ &mdash; accuracy is only limited by floating point precision

$$
\frac{\partial L}{\partial \mu}, \frac{\partial L}{\partial \theta_{i}}
$$

.grid[
.kol-1-2[

<br><br>
Gain this through the frameworks creating _computational directed acyclic graphs_ and then applying the chain rule (to the operations)
]
.kol-1-2[
.center.width-80[![DAG](figures/computational_graph.png)]
.center[HistFactory likelihood]
]
]

---
# Example pyhf JSON spec

.center[<a href="https://carbon.now.sh/?bg=rgba(255%2C255%2C255%2C1)&t=seti&wt=none&l=application%2Fjson&ds=false&dsyoff=20px&dsblur=68px&wc=true&wa=true&pv=3px&ph=1px&ln=false&fl=1&fm=Hack&fs=14px&lh=133%25&si=false&es=4x&wm=false&code=%257B%250A%2520%2520%2520%2520%2522channels%2522%253A%2520%255B%2520%2523%2520List%2520of%2520regions%250A%2520%2520%2520%2520%2520%2520%2520%2520%257B%2520%2522name%2522%253A%2520%2522singlechannel%2522%252C%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2522samples%2522%253A%2520%255B%2520%2523%2520List%2520of%2520samples%2520in%2520region%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%257B%2520%2522name%2522%253A%2520%2522signal%2522%252C%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2522data%2522%253A%2520%255B5.0%252C%252010.0%255D%252C%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2523%2520List%2520of%2520rate%2520factors%2520and%252For%2520systematic%2520uncertainties%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2522modifiers%2522%253A%2520%255B%2520%257B%2520%2522name%2522%253A%2520%2522mu%2522%252C%2520%2522type%2522%253A%2520%2522normfactor%2522%252C%2520%2522data%2522%253A%2520null%257D%2520%255D%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%257D%252C%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%257B%2520%2522name%2522%253A%2520%2522background%2522%252C%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2522data%2522%253A%2520%255B50.0%252C%252060.0%255D%252C%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2522modifiers%2522%253A%2520%255B%2520%257B%2522name%2522%253A%2520%2522uncorr_bkguncrt%2522%252C%2520%2522type%2522%253A%2520%2522shapesys%2522%252C%2520%2522data%2522%253A%2520%255B5.0%252C%252012.0%255D%257D%2520%255D%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%257D%250A%2520%2520%2520%2520%2520%2520%2520%2520%2520%2520%255D%250A%2520%2520%2520%2520%2520%2520%2520%2520%257D%250A%2520%2520%2520%2520%255D%252C%250A%2520%2520%2520%2520%2522observations%2522%253A%2520%255B%2520%2523%2520Observed%2520data%250A%2520%2520%2520%2520%2520%2520%2520%2520%257B%2520%2522name%2522%253A%2520%2522singlechannel%2522%252C%2520%2522data%2522%253A%2520%255B50.0%252C%252060.0%255D%2520%257D%250A%2520%2520%2520%2520%255D%252C%250A%2520%2520%2520%2520%2522measurements%2522%253A%2520%255B%2520%2523%2520Parameter%2520of%2520interest%250A%2520%2520%2520%2520%2520%2520%2520%2520%257B%2520%2522name%2522%253A%2520%2522Measurement%2522%252C%2520%2522config%2522%253A%2520%257B%2522poi%2522%253A%2520%2522mu%2522%252C%2520%2522parameters%2522%253A%2520%255B%255D%257D%2520%257D%250A%2520%2520%2520%2520%255D%252C%250A%2520%2520%2520%2520%2522version%2522%253A%2520%25221.0.0%2522%2520%2523%2520Version%2520of%2520spec%2520standard%250A%257D">`JSON` defining a single channel, two bin counting experiment with systematics</a>]

.center.width-80[![demo_JSON](figures/carbon_JSON_spec_annotated.png)]

---
# Will pyhf extend to unbinned models?

<br>
- No. This is outside the project scope
- pyhf mission goal: To deliver an intuitive and computationally efficient tool for using HistFactory in physics analyses
- _Very_ interested in seeing Pythonic tools for unbinned fits in the wider particle physics community
- We feel that our efforts would be better spent in focusing on building underlying interface libraries for statistical modeling that offer .bold[common APIs] for the growing number of Pythonic libraries in the ecosystem

<br>
# Why are you talking at an LHCb meeting then?

- LHCb uses binned models for _some_ analyses
   - Perhaps useful in those cases? Let's talk.
- At CHEP 2019 was approached by analyst of such an analysis

---
# Live demo time!

<br><br>

.center.bold[Just click the button!]<br><br><br>

.center.width-70[[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/matthewfeickert/talk-LHCb-Stats-Forum/master?urlpath=lab)]

---
# From spec to model with Python API

.center.width-70[![model_from_spec](figures/carbon_model_from_spec.png)]

- Build workspace from spec
- From workspace:
   - Construct model
   - Get data (observations + auxiliary measurements)

---
# Interval estimation

.kol-1-2[
.bold[(pseudo)Frequentist confidence intervals]

- Use the [$\\mathrm{CL}_{s}$ method](http://cds.cern.ch/record/451614) to construct the interval
   - $\\mathrm{CL}_{s}$ results in [overcoverage by construction](https://indico.cern.ch/event/208901/contributions/1501047/) (at low signal-background discrimination)
- pyhf offers an API & CLI for hypothesis test
   - `$ pyhf cls spec.json`
- Can *invert* the tests in order to obtain an interval with the correct coverage properties
- pyhf plan is to factor out inference to another library and focus on modeling

.bold[Bayesian credible intervals]

- Currently don't support any API for this for same reason
- c.f. [H. Dembinski, PyHEP 2019](https://indico.cern.ch/event/833895/contributions/3577810/)
]
.kol-1-2[
.center.width-100[![demo_CLI](figures/carbon_CLI_output.png)]
.center[CLI to [`pyhf.utils.hypotest`](https://scikit-hep.org/pyhf/_generated/pyhf.utils.hypotest.html) returns  $\\mathrm{CL}_{s}$ values]
]

---
# Interval estimation

.kol-1-3[
.bold[(pseudo)Frequentist confidence intervals]

- Use the [$\\mathrm{CL}_{s}$ method](http://cds.cern.ch/record/451614) to construct the interval
   - $\\mathrm{CL}_{s}$ results in [overcoverage by construction](https://indico.cern.ch/event/208901/contributions/1501047/) (at low signal-background discrimination)
- pyhf offers an API & CLI for hypothesis test
   - `$ pyhf cls spec.json`
- Can *invert* the tests in order to obtain an interval with the correct coverage properties
- pyhf plan is to factor out inference to another library and focus on modeling
]
.kol-2-3[
.center.width-100[![demo_interval](figures/carbon_interval_estimation.png)]
.left[From demo: invert tests to get expected ([Brazil band](https://arxiv.org/abs/1306.3117)) and observed $95\%\\, \\mathrm{CL}$ upper limits on $\mu$]
]

---
# How to combine likelihoods?

.kol-1-2[
<br><br>
- Essentially just concatenate them
- As the channels are different, then just add the lists
]
.kol-1-2[
.left.width-80[![demo_JSON](figures/carbon_JSON_spec_annotated.png)]
]
# Is there a pyhf utility to make this easy?

- Not yet, but there is a PR to add this functionality into the CLI
   - [scikit-hep/pyhf PR #617](https://github.com/scikit-hep/pyhf/pull/617)
- `$ pyhf spec combine spec1.json spec2.json`

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
[![flowchart](figures/process.svg)](https://cds.cern.ch/record/2684863)
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
.kol-2-3[
Through pyhf are able to provide:
- Independent .bold[pure-Python implementation] of HistFactory + hypothesis testing
- .bold[Efficient] computation with .bold[backend agnostic] API to tensor libraries
- .bold[JSON specification] of likelihoods
   - human/machine readable, versionable, HEPData friendly, orders of magnitude smaller
   - long long term support
- .bold[Bidirectional translation] of likelihood specifications
   - ROOT workspaces ↔ JSON
- Publication of the .bold[full likelihood] of physics analysis
- Transparent .bold[open development] on GitHub
]
.kol-1-3.center.width-100[
<br>
[![overlay_multiplex_contour](figures/overlay_multiplex_contour.png)](https://cds.cern.ch/record/2684863)
[![pyhf_logo](https://iris-hep.org/assets/logos/pyhf-logo.png)](https://github.com/scikit-hep/pyhf)
]

---
class: end-slide, center

Backup

---
# Why is the likelihood important?

<br>

.kol-1-2.width-90[
- High information-density summary of analysis
- Almost everything we do in the analysis ultimately affects the likelihood and is encapsulated in it
   - Trigger
   - Detector
   - Systematic Uncertainties
   - Event Selection
- Unique representation of the analysis to preserve
]
.kol-1-2.width-90[
<br><br><br>
[![likelihood_connections](figures/likelihood_connections.png)](https://indico.cern.ch/event/839382/contributions/3521168/)
]

---
# Likelihood serialization...

.center[...making good on [19 year old agreement to publish likelihoods](https://indico.cern.ch/event/746178/contributions/3396797/)]

<br>

.center.width-80[
[![likelihood_publishing_agreement](figures/likelihood_publishing_agreement.png)](https://cds.cern.ch/record/411537)
]

.center[([1st Workshop on Confidence Limits, CERN, 2000](http://inspirehep.net/record/534129))]

.bold[This hadn't been done in HEP until now]
- In an "open world" of statistics this is a difficult problem to solve
- What to preserve and how? All of ROOT?
- Idea: Focus on a single more tractable binned model first

---
# HistFactory likelihood graph

.center.width-60[![DAG](figures/computational_graph.png)]

---
# ROOT + XML to JSON and back

.center.width-70[![flowchart](figures/process.png)]

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
4. A. Read, .italic[[Modified frequentist analysis of search results (the $\\mathrm{CL}_{s}$ method)](http://cds.cern.ch/record/451614)], 2000.
5. K. Cranmer, .italic[[CERN Latin-American School of High-Energy Physics: Statistics for Particle Physicists](https://indico.cern.ch/event/208901/contributions/1501047/)], 2013.
6. ATLAS collaboration, .italic[[Search for bottom-squark pair production with the ATLAS detector in final states containing Higgs bosons, b-jets and missing transverse momentum](http://inspirehep.net/record/1748602)], 2019
7. ATLAS collaboration, .italic[[Reproducing searches for new physics with the ATLAS experiment through publication of full statistical likelihoods](https://cds.cern.ch/record/2684863)], 2019
8. ATLAS collaboration, .italic[[Search for bottom-squark pair production with the ATLAS detector in final states containing Higgs bosons, b-jets and missing transverse momentum: HEPData entry](https://www.hepdata.net/record/ins1748602)], 2019

---

class: end-slide, center
count: false

The end.
