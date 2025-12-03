
<section id="title-slide">
  <h1 class="title">GRMHD Tutorial Day 3</h1>
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

# Part 3: What is GRMHD?

---

# Outline
<!-- .slide: data-auto-animate -->

1. Euler and Einstein <!-- .element: class="fragment" -->

2. Magnetic Field Additions <!-- .element: class="fragment" -->

3. GRMHD!  <!-- .element: class="fragment" -->

3. Evolving the magnetic field <!-- .element: class="fragment" -->

3. Coordinate systems <!-- .element: class="fragment" -->

4. Note on sound speed <!-- .element: class="fragment" -->

---

# Euler Equations
<!-- .slide: data-auto-animate -->

<!-- The $$ aren't necessary, but they trigger syntax highlighting in my editor -->
$$
\begin{aligned}
  \frac{\partial \rho}{\partial t} &amp; = - \nabla \cdot (\rho \vec{u}) \\\\
  \frac{\partial E}{\partial t} &amp; = - \nabla \cdot \left[ (E + p) \vec{u} \right] \\\\
  \frac{\partial (\rho \vec{u})}{\partial t} &amp; = - \rho \vec{u} \cdot \nabla \vec{u} - \nabla p
\end{aligned}
$$

notes: Remember the Euler equations from yesterday. Conservation of five quantities.

---

# Euler Equations
<!-- .slide: data-auto-animate -->

$$
\begin{aligned}
  \mathbf{U} &= \left( \rho, E, \rho u^i \right) \\\\
  \mathbf{F}^j &= \begin{pmatrix} \rho u^j \\\\ u^j \rho u^i + p \delta^{ij} \\\\ (E + p) u^j \end{pmatrix} \\\\
  \mathbf{P} &= \left( \rho, u, \vec{u} \right)
\end{aligned}
$$

---

# GRHD Equations

$$
\begin{aligned}
    \partial_{t} \left( \sqrt{-g} \rho u^{t} \right) &= -\partial_j \left( \sqrt{-g} \rho u^{j} \right) \\\\
    \partial_{t} \left( \sqrt{-g} T^{t}\_{\nu} \right) &= -\partial_j \left( \sqrt{-g} T^{j}\_{\nu} \right) + \sqrt{-g} T^{\kappa}\_{\lambda} \Gamma^{\lambda}\_{\nu \kappa} \\\\
\end{aligned}
$$

&nbsp;

$$
\begin{aligned}
  \mathbf{T}\_\text{HD}^{\mu \nu} = (\rho + u + p) u^\mu u^\nu + p g^{\mu\nu}
\end{aligned}
$$

notes: New source term!

---

# $\mathbf{U}$, $\mathbf{F}$ change!

$$
\begin{aligned}
  \mathbf{U} &= \sqrt{-g} \left( \rho u^t, T^t_t, T^t_i \right) \\\\
  \mathbf{F}^j &= \sqrt{-g} \begin{pmatrix} \rho u^j \\\\ T^j_t \\\\ T^j_i  \end{pmatrix} \\\\
  \mathbf{P} &= \left( \rho, u, \vec{u} \right)
\end{aligned}
$$

Where $\sqrt{-g}$ is the root negative metric determinant, which we pre-compute at every zone center and face, as the metric will not change.

---

# Adding Magnetic Fields
<!-- .slide: data-auto-animate -->

* $\vec{B}$ and $\vec{E}$ are just like in MHD! Electromagnetism is already relativistic
* I will not be deriving everything, but you can work this out from MTW if you define $B^i = F^{*it}$ from the Maxwell tensor and take $\vec{E}$ to vanish
* *But $\vec{E}$ only vanishes in fluid frame!*
  * We're purely working with the equations here, which are true in all frames!

notes: there will be some $\vec{E}$ in most frames!  However, it is only a result of frame-transforming $B^i$, not independent of B

---

# Adding Magnetic Fields
<!-- .slide: data-auto-animate -->

The magnetic field also contains energy and momentum we need to track with the stress-energy tensor

&nbsp;

$$
\begin{aligned}
  T_\mathrm{EM}^{\mu\nu} = b^2 u^\mu u^\nu + \frac{1}{2} b^2 g^{\mu\nu} - b^\mu b^\nu
\end{aligned}
$$

---

# GRMHD Equations:

$$
\begin{aligned}
    \partial_{t} \left( \sqrt{-g} \rho u^{t} \right) &= -\partial_i \left( \sqrt{-g} \rho u^{i} \right) \\\\
    \partial_{t} \left( \sqrt{-g} T^{t}\_{\nu} \right) &= -\partial_i \left( \sqrt{-g} T^{i}\_{\nu} \right) + \sqrt{-g} T^{\kappa}\_{\lambda} \Gamma^{\lambda}\_{\nu \kappa} \\\\
    \partial_{t} \left( \sqrt{-g} B^i \right) &= - \partial_j \left[ \sqrt{-g} \left( b^j u^i - b^i u^j \right) \right] \\\\
    \frac{1}{\sqrt{-g}} \partial_i \left( \sqrt{-g} B^i \right) &= 0
\end{aligned}
$$

$$
\begin{aligned}
  T_\mathrm{MHD}^{\mu\nu} = (\rho + u + p + b^2) u^\mu u^\nu + (p + \frac{1}{2} b^2) g^{\mu\nu} - b^\mu b^\nu
\end{aligned}
$$

notes:   We'll return to that last equation later!

---

# Final $\mathbf{U}$, $\mathbf{F}$

$$
\begin{aligned}
  \mathbf{U} &= \sqrt{-g} \left( \rho u^t, T^t_t, T^t_i, B^i \right) \\\\
  \mathbf{F}^j &= \sqrt{-g} \begin{pmatrix} \rho u^j \\\\ T^j_t \\\\ T^j_i \\\\  \left( b^j u^i - b^i u^j \right) \end{pmatrix} \\\\
  \mathbf{P} &= \left( \rho, u, \vec{u}, \vec{B} \right)
\end{aligned}
$$

---

# Recovering primitive variables

* Determining $\mathbf{P}$ in terms of $\mathbf{U}$ is now not an analytic operation.  But we update $\mathbf{U}$!
* Various solvers proposed:
  * Direct 5-way equation solve: slow and unreliable (Gammie et al. 2003)
  * Can be reduced to 1D: solve for enthalpy, determine Lorentz factor (Noble et al. 2006 $1D_W$)
  * By designing a 1D function, can guarantee a solution (Kastaun et al. 2020 "robust" or "RePrimAnd")
* There will always be $\mathbf{U}$ which do not correspond to any physical $\mathbf{P}$!  That is, we cannot always find $\mathbf{P}$ so $\mathbf{U}( \mathbf{P} ( \mathbf{U} ) ) = \mathbf{U}$

---

# Constraint equation
<!-- .slide: data-auto-animate -->

Recall our last GRMHD equation, $\frac{1}{\sqrt{-g}} \partial_i \left( \sqrt{-g} B^i \right) = 0$.

This is a constraint! It must be atisfied in initial conditions and throughout evolution.

Evolving the other equations *should* preserve this, but we're dealing with numerics here.

---

# Constraint equation
<!-- .slide: data-auto-animate -->

Approaches:

1. Just don't (bad idea)
2. "Clean up" any divergence every so often ("Elliptic maid")
3. Record constraint violations and advect/damp them away ("Constraint damping")
4. Revise the magnetic field evolution at each step to ensure the requirement is perfectly preserved ("Constrained transport")

&nbsp;

Most static-spacetime GRMHD codes use #4!

---

# Constraint equation
<!-- .slide: data-auto-animate -->

* Effectively, update the magnetic field using the current loops formed by the edges around each face
* Naturally this leads to defining B on the faces of each cell, and preserving the divergence at cell centers (Face-centered; Balsara & Spicer, Gardiner & Stone, etc.)
* However, simple constrained transport can be shifted to cell centers in each direction, for simplicity ("Flux-CT," Tóth 2000)

---

# Constrained transport
<!-- .slide: data-auto-animate data-background-image="Figures/diagrams/ct_lowres.png" data-background-size="contain" -->
&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

---

# Algorithm outline:

1. Consistent primitive and conserved variables $\mathbf{P}$, $\mathbf{U}$
2. Reconstruct values of primitive variables at the faces of zones
3. Calculate $\mathbf{U_L}$, $\mathbf{U_R}$, $\mathbf{F_L}$, $\mathbf{F_R}$
4. Riemann solve
5. **Update $\mathbf{B}$ using constrained transport**
6. Subtract $\nabla \cdot \mathbf{F}$ from $\mathbf{U}$ **and add $T^{\kappa}\_{\lambda} \Gamma^{\lambda}\_{\nu \kappa}$**
7. **Compute new primitive variables**
8. Floors, source terms, output, operator-split physics

notes: Putting it together, here are the updates to the algorithm for evolving GRMHD, with additions vs HD highlighted

---

# Coordinate choices

* We can make arbitrary transformations to the metric $g^{\mu\nu}$!
* Simulation zones are evenly spaced in the transformed coordinate system
* This lets us move around simulation resolution by compressing the *coordinate system*!

---

# Coordinate choices

e.g.,

$$
\begin{aligned}
    r &= \exp \left(X\_{\mathrm{FMKS}}^1 \right) \\\\
    \theta &= \theta_g + \exp \left(- s \left(X\_{\mathrm{FMKS}}^1 - X\_{\mathrm{FMKS,0}}^1 \right) \right)
\end{aligned}
$$

where

$$
\begin{aligned}
    \theta_g &amp; = \pi X\_{\mathrm{FMKS}}^2 + \frac{1 - h}{2} \sin\left( 2 \pi X\_{\mathrm{FMKS}}^2 \right) \\\\
    C &= \frac{\pi}{2 + \frac{2}{A^B + B A^B}} \\\\
    \theta_j &amp; = C \left( 2 X\_{\mathrm{FMKS}}^2 - 1 \right) \left( 1 + \frac{\left(\frac{2 X\_{\mathrm{FMKS}}^2 - 1}{A} \right)^B}{1 + B} \right) + \frac{\pi}{2}.
\end{aligned}
$$

---

# Coordinate choices

![](Figures/diagrams/coordinate_systems_spherical.png)

---

# Coordinate choices

![](Figures/diagrams/coordinate_systems_rectangular.png)

---

# Maximum wave speed ("sound" speed)

* Determined from the dispersion relation
* MHD version contains new oscillation modes (entropy, Alfvén, fast and slow magnetosonic)
* Full MHD dispersion relation is quartic, KHARMA uses a simpler quadratic form which overestimates
* GR adds *nothing*:
  * Composed of scalars
  * Speed is a scalar
* (However, if we wanted more waves we *would* need frame transformations)

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

