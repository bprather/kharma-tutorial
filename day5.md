
<section id="title-slide">
  <h1 class="title">GRMHD Tutorial Day 5</h1>
  <div class="column" style="height: 500px;">
  <p class="author">Ben Prather,<br />
  Black Hole Initiative Fellow, Harvard University
  </p>
  <p>
  Instituto de Astrofísica de Andalucía,<br />
  Granada, Spain
  </p>
  <p class="date">Nov 26, 2025</p>
  </div>
</section>

---

# Part 5: Beyond GRMHD

---

# Assumptions:

1. A **Fluid**
2. Which has isotropic pressure and no heat conduction (**inviscid**)
3. With perfect conductivity (**non-resistive**)
4. Which does not emit or absorb radiation (**Non-radiative**)
5. Or exert meaningful self-gravity (**Non-gravitating**)

---

# Viscous MHD

* Subgrid diffusive effects, so semi-Implicit scheme
* $\mathbf{P}$ for the next step is computed implcitly in each zone: fluxes are explicit, but source terms are time-dependent!
* 



---