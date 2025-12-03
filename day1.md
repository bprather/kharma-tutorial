
<section id="title-slide">
  <h1 class="title">GRMHD Tutorial Day 1</h1>
  <div class="column" style="height: 500px;">
  <p class="author">Ben Prather,<br />
  Black Hole Initiative Fellow, Harvard University
  </p>
  <p>
  Instituto de Astrofísica de Andalucía,<br />
  Granada, Spain
  </p>
  <p class="date">Nov 24, 2025</p>
  </div>
</section>

---

<h1 data-id="notes"> Notes for This Week </h1>
<!-- .slide: data-auto-animate -->

---

<h1 data-id="notes"> Lesson Plan </h1>
<!-- .slide: data-auto-animate -->

Morning Lectures:

1. What is GRMHD and why do we use it? <!-- .element: class="fragment" -->

2. How does a finite-volume simulation work? <!-- .element: class="fragment" -->

3. What else do you need in a GRMHD algorithm? <!-- .element: class="fragment" -->

4. KHARMA in particular <!-- .element: class="fragment" -->

5. Beyond GRMHD <!-- .element: class="fragment" -->

notes: This is a general overview of what I'm planning on covering each day.  Today I'll start with an overview of why this field is important, and why I think it's cool -- hopefully that'll justify the pain and anguish of getting KHARMA running on your hardware this afternoon.  Then we move into the nitty-gritty details, the pieces that make up a GRMHD algorithm.  I'll build that up piece by piece, starting with Newtonian Hydrodynamics and only ending up with GRMHD later in the week.  That should get you familiar with each piece, and hopefully help you understand when things don't work, as well as give you some confidence to dive into the code and change things if you need to.

---

<h1 data-id="notes"> Lesson Plan </h1>
<!-- .slide: data-auto-animate -->

Tutorials:

1. Getting KHARMA running; first simulation <!-- .element: class="fragment" -->

2. Shock tubes, hydrodynamics, instabilities <!-- .element: class="fragment" -->

3. Magnetohydrodynamics, first tori <!-- .element: class="fragment" -->

4. Hike at Dehesa del Generalife! <!-- .element: class="fragment" -->

5. Measuring simulation properties <!-- .element: class="fragment" -->

notes: After the lectures, we'll cover all of KHARMA's parameters and run simple simulations.  Most "interesting" GRMHD simulations, i.e. 3D tori, are not really feasible on the hardware we have.  If you have access to computer cycles at your home institution and want to start a long, 3D GRMHD run today, that would finish up right about when everyone is doing simulation analysis on Thursday and Friday

---

# NB

1. This is an *outline*!

2. Please feel free to change things up: ask what *you'd* like to know!

3. This timeline is *optimistic*. We don't have to cover everything this week.

notes: I'm trying to teach two things here: first what do you have to do to run a GRMHD simulation, but also what are the techniques going on under the hood, and what are their limitations?\
Today's lecture is supposed to be a an overview, and then we can drill down on different pieces as you all are interested.  It's going to be a surface view of a lot of material

---

<h1 data-id="is"> Part 1: What use is GRMHD? </h1>
<!-- .slide: data-auto-animate -->

---

<h1 data-id="is"> What <strong>isn't</strong> GRMHD? </h1>
<!-- .slide: data-auto-animate -->

GRMHD isn't flawless!

* System is highly unstable <!-- .element: class="fragment" -->

* Can be inaccurate, wrong about details <!-- .element: class="fragment" -->

* Some elements always determined by floors <!-- .element: class="fragment" -->

notes: The temperature and velocity of the jet jet of accretion GRMHD simulations will be determined by the simulation floors.  The total energy and Poynting flux are more trustworthy, as they come from globally-conserved fields and are influenced by their own truncation error primarily vs. other fields with larger values. \
\
As with any model, be very careful that you're sticking to the domain of validity, and drawing only the inferences the model warrants.

---

<h1 data-id="is"> What <strong>isn't</strong> GRMHD? </h1>
<!-- .slide: data-auto-animate -->

<div style="display:flex; align-items: flex-start; justify-content: space-evenly; text-align: left">

"All simulations are wrong.  Some are useful."\
\
\
&mdash; Loose paraphrase of a saying among statisticians, attributed to George Box

</div>

---

<h1 data-id="is"> What is GRMHD? </h1>
<!-- .slide: data-auto-animate -->

Assumptions:

<div style="text-align: left">
<div class="fragment">

1. A **Fluid**

</div>
<div class="fragment">

2. Which has isotropic pressure and no heat conduction (**inviscid**)

</div>
<div class="fragment">

3. With perfect conductivity (**non-resistive**)

</div>
<div class="fragment">

&nbsp; &nbsp; &nbsp; &nbsp; a. And thus a "frozen-in" magnetic field (**Flux-freezing**)

</div>
<div class="fragment">

4. Which does not emit or absorb radiation (**Non-radiative**)

</div>
<div class="fragment">

5. Or exert meaningful self-gravity (**Non-gravitating**)

</div>
</div>

notes: Assumption 1 is important!  We'll return to it tomorrow.  The rest of the assumptions are less fundamental and we'll talk about how we can move beyond them later.

---

<h1 data-id="is"> What is GRMHD used for? </h1>
<!-- .slide: data-auto-animate -->

<div style="display:flex; align-items: flex-start; justify-content: space-evenly; text-align: left">

<div>

<div class="fragment"data-fragment-index="1">

* Black hole accretion

</div>
<div class="fragment"data-fragment-index="2">

* Neutron Stars, Kilonovae

</div>
<div class="fragment"data-fragment-index="3">

* Relativistic Supernovae

</div>
<div class="fragment"data-fragment-index="4">

* Anything **large** & **dense** (LRDs?)

</div>
</div>

<div class="r-stack" width=400px>
<img class="fragment" data-fragment-index="1"
  src="Figures/stock/eht_m87_2017.png" type="image/png"/>
<img class="fragment" data-fragment-index="2"
  src="Figures/stock/bns_fields.png" type="image/png"/>
<img class="fragment" data-fragment-index="3"
  src="Figures/stock/supernova.png" type="image/png"/>
</div>

</div>

---

<h1 data-id="is"> What <strong>isn't</strong> GRMHD used for? </h1>
<!-- .slide: data-auto-animate -->

The GRMHD we'll be doing is not for:

* Non-relativistic systems

* Dense/incompressible systems

* Sparse/kinetic systems

* Self-gravitating systems

* Highly resistive or viscous systems

* Radiatively efficient or mostly radiation-pressure systems


notes: The same simulation may be valid when trying to measure some quantities, and not others.  It may faithfully reproduce a quantity on short timescales but not long, or on average but not in specific. \
Also watch out for properties which reflect computational details, or still present from initial conditions!

---

# What GRMHD codes are there?

<div class="row" style="align-items: flex-start; text-align: left">

<div>

GPU/Hybrid:
* KHARMA
* H-AMR
* AthenaK

</div>
<div>

CPU:
* iharm3d
* BHAC
* KORAL
* Athena++
* Many other HARM-likes...

</div>
<div>

Dynamical Spacetimes:
* Phoebus
* IllinoisGRMHD
* Whisky/WhiskyTHC
* Spritz
* Many others

</div>

notes: Very incomplete list!\
Note there are different spheres of codes here.  Among HARM-like codes, a split between traditional CPU and newer GPU/hybrid codes.  Also note there are a lot more MHD codes and SRMHD codes\
See Oliver Porth et al, "Comparison of GRMHD codes..."

---

# Getting matter to the EH

Must transport energy, angular momentum away
* Radiation (thin disk)
* Viscosity (alpha-disk, ADAF, RIAF)
* Magnetic field can provide "viscosity" efficiently

---

# Disk Profile

<img width=80% src="Figures/thesis/intro/jet_disk_both.png">

---

# MADs and SANEs

<video loop muted width=80% src="Figures/disks/Comparisons/MADvSANE_phi_diffnlines.webm"></video>

---

# Rotation Profiles

<img width=60% src="Figures/thesis/intro/rotation_curves.png">

notes: Differential rotation with strong field slows MADs all by itself.  Large magnetic field near EH pressure-supports very sub-keplerian material. SANEs accrete via magnetorotational instability, which much more gradually transports angular momentum outward even in local magnetic field, and thus operates even for chaotic fields.

---

# GRMHD in EHT

---

# The EHT Theory Pipeline

![](Figures/diagrams/pipeline_diagram_only.png) <!-- .element: class="r-stretch" -->

notes: A simulation is a full forward model -- given a normalization, it is a prediction for all observables, at all frequencies, variability, etc, etc.  So to compare models, we run GRMHD simulations covering different sets of parameters.

---

<h1 data-id="libs"> Libraries </h1>
<!-- .slide: data-auto-animate -->

<video loop muted src="Figures/collage_grmhd/log_rho_poloidal_big.mp4" type="video/mp4"></video>

---

<h1 class="white-title-top" style="color: white" data-id="libs"> Libraries </h1>
<!-- .slide: data-auto-animate data-background-image="Figures/collage_grmhd/collage_v5.png" -->

---

# The Imaging Process

<img width=80% src="Figures/outreach/m87_release_materials/EHT-photon-shadow-v3-190322.jpg"></img>

---

# The Imaging Process

<img width=80% src="Figures/thesis/intro/geodesic_paths.png">

---

<h1 class="white-title-bottom" style="color: white"> Building Image Libraries </h1>
<!-- .slide: data-background-video="Figures/collage_images/build_annotated_1080p.mp4" data-background-video-loop=true data-background-size=contain data-background-video-muted=true -->

---

<h1 class="white-title-bottom" style="color: white"> Cutting Image Libraries </h1>
<!-- .slide: data-background-video="Figures/collage_images/collage_cuts_best_bet_only_notes_1080p.mp4" data-background-video-loop=true data-background-size=contain data-background-video-muted=true -->

---

# Comparisons
<!-- .slide: data-auto-animate -->

<img style="max-width: 100%; height: auto;" src="Figures/libraries/Pizza_upshot.png"></img>

---

# Comparisons
<!-- .slide: data-auto-animate -->

<div class="space-even">
<!-- <img style="max-width: 50%; height: auto;" src="Figures/collage_images/sgra_bestbet_00000.png"> -->
<img style="max-width: 100%; height: auto;" src="Figures/stock/eht_sgra_compare.png">
</div>

---

# Thanks!

<div class="column" style="height: 900px;">

Thank you to all KHARMA contributors! \
Hyerin Cho, Vedant Dhruv, Josh Stanway, Angelo Ricarte, George Wong

Work supported by:

<div class="row" style="align-items: flex-end;">

![](Figures/brand/Harvard/BHI-Logo-Black-on-White-Transparent-1024x496.png) <!-- .element width=20% -->

![](Figures/brand/Harvard/gbmf_new-1024x612_transparent.png) <!-- .element width=20% -->

![](Figures/brand/Harvard/jtf-logo-transparent.png) <!-- .element width=20% -->

</div>

</div>

---

# Tutorial Day 1

---

