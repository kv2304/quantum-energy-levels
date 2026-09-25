# Analysis

Figures produced by `python levels.py`. 
## Spectrum: $E_n$ and $\psi_n$

<p align="center">
<img src="../figures/spectrum.svg" width="49%">
<img src="../figures/eigenstates.svg" width="49%">
</p>

A symmetric double well produces these four lowest eigenvalues and eigenstates from
$H\psi = E\psi$. Each level is drawn only where $E > V(x)$: $E_1, E_2$ are bound inside the
wells, while $E_3, E_4$ clear $V_0$ and span the box. State $n$ carries $n-1$ nodes.

The two identical wells still do not give one energy twice over: the barrier is finite, so each
tail leaks into it and couples the wells, splitting $E_1, E_2$ apart. The inset magnifies that
split, 3.84 neV against energies of 0.12 eV.

## Tilted wells: $\psi_{1,2}$ against $\lambda$

<p align="center">
<img src="../figures/tilted_wells.svg">
</p>

Pushing the left floor to $-\lambda/2$ and the right to $+\lambda/2$ breaks that symmetry, so
$\psi_1, \psi_2$ need not split evenly. With $\lambda$ negative the right well sits lower, and
$\psi_1$ shifts weight into it, lowering $\int V|\psi|^{2}\,\mathrm{d}x$. That shift raises
$\tfrac{\hbar^{2}}{2m_e}\int|\psi'|^{2}\,\mathrm{d}x$, so the gain in potential energy is paid
for in kinetic energy and the localisation grows only gradually with $\lambda$. $\psi_2$,
orthogonal to $\psi_1$, is therefore dominated by the left well.

The panel is drawn at $|\lambda|/2t = 1.9$, rather than the sweep's extreme of 12: the tilt
dominates the coupling without completing the localisation, so each state keeps visible
probability density on the disfavoured side.

## Avoided crossing: $\Delta E(\lambda)$

<p align="center">
<img src="../figures/avoided_crossing.svg">
</p>

Sweeping $\lambda$ through zero brings the two levels together and turns them away, with the
two-level fit $\Delta E(\lambda) = \sqrt{(d\lambda)^{2}+(2t)^{2}}$ lying on both curves. They
bottom out at $2t = 1.782$ meV and never meet; the degeneracy is reached only as
$w \to \infty$.

The right panel shows the two states trading places through $\lambda = 0$. Forbidden to cross in
energy, the pair crosses in character: the state that began in the left well ends in the right
one. A sweep slow enough to stay on one curve therefore carries the electron bodily across the
barrier. Had the levels been free to cross, the electron would have stayed where it started.

## Log gap: $\ln 2t$ against $w$

<p align="center">
<img src="../figures/log_gap.svg">
</p>

The left panel is the measurement. $\ln 2t$ against $w$ is a straight line, so $2t$, the gap at
$\lambda = 0$, falls exponentially with barrier width: the fit spans 13 in $\ln 2t$, a drop of
about half a million between $w = 2$ and $w = 8$ nm. Its slope is $-2.1747$ nm⁻¹, and $\kappa$
built from $V_0 - E$, which uses the potential and the state energy alone, is $2.1747$ nm⁻¹:
the same number by an independent route.

The right panel repeats the fit at six well depths, the numbers beside each line being $V_0$ in
eV. Every slope follows its own $\kappa$ rather than settling at one value. Points below
$w = 2$ nm are drawn but excluded from the fit, where the wells couple strongly enough that the
local slope has not yet reached $-\kappa$.

The line is straight because of the form of $t$, the off-diagonal element of
$H_{\mathrm{eff}}$, defined by

```math
t = -\int \phi_L^{*}\,\hat{H}\,\phi_R\,\mathrm{d}x = -\langle \phi_L | \hat{H} | \phi_R \rangle
```

This is the matrix element that couples the two localised states, turning the pair into one
system rather than two independent wells. Substituting the definitions of $\phi_L$ and $\phi_R$
gives
$\langle \phi_L | \hat{H} | \phi_R \rangle = \phi_L^{T} H \phi_R = \tfrac{1}{2}(E_S - E_A)$, so
$2t = E_A - E_S$. Inside the barrier the two states decay from opposite sides,

```math
\phi_L \sim e^{-\kappa(x + w/2)}, \qquad \phi_R \sim e^{-\kappa(w/2 - x)}
```

and their product is $e^{-\kappa w}$, the $x$-dependence cancelling exactly, so $w$ enters $t$
through that exponential alone.

So $\kappa = 2.17$ nm⁻¹: inside the wall the amplitude falls by a factor $e$ every
$1/\kappa = 0.46$ nm, and the probability sits on average $1/2\kappa = 0.23$ nm in. Every
further 0.46 nm of barrier then divides $2t$ by $e$ and multiplies the crossing time by the
same, taking an electron placed in one well from 0.13 ps at $w = 1$ nm to 0.54 μs at $w = 8$
nm.
