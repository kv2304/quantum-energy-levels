# Method

Finite differences is chosen rather than a shooting method or a basis expansion because the
discretised operator is sparse and symmetric, one diagonalisation returns every state at once,
and no basis has to be chosen in advance. The cost is second-order accuracy, which the box check
measures rather than assumes.

## Given

<p align="center">
<img src="../figures/setup.svg">
</p>

Two wells of width $a = 1.0$ nm and depth $V_0 = 0.30$ eV, separated by a barrier of width $w$,
swept over $w \in \lbrace 1, 1.5, 2, \ldots, 8 \rbrace$ nm. The barrier and the region outside
the wells both sit at $V = V_0$; the well floors sit at $V = 0$ before any tilt. $a$ and $V_0$
are chosen so one well holds exactly one bound state, so the double well holds two and nothing
else.

The tilt is the single real parameter $\lambda$. It displaces the left well floor to
$-\lambda/2$ and the right well floor to $+\lambda/2$, and changes nothing outside the well
interiors.

Grid: $M = 2100$ interior points of spacing $h = 0.010$ nm, giving a box of width
$L = (M+1)h = 21.01$ nm. Constants: $\hbar^{2}/2m_e = 0.0380998$ eV nm², with $m_e$ the electron
mass.

## Step 1 — discretise the TISE  (`hamiltonian`)

Write the time-independent Schrödinger equation in one dimension:

```math
-\frac{\hbar^{2}}{2m_e}\frac{\mathrm{d}^{2}\psi}{\mathrm{d}x^{2}} + V(x)\,\psi = E\psi
```

Sample it on the grid. Let $x_i = ih$ for $i = 1, \ldots, M$, and write $\psi_i = \psi(x_i)$ and
$V_i = V(x_i)$. Replace the second derivative by the three-point central difference:

```math
\frac{\mathrm{d}^{2}\psi}{\mathrm{d}x^{2}}\bigg|_{x_i}
 = \frac{\psi_{i-1} - 2\psi_i + \psi_{i+1}}{h^{2}} + O(h^{2})
```

Substitute and collect terms, defining $k = \hbar^{2}/2m_eh^{2} = 380.998$ eV:

```math
-k\psi_{i-1} + (2k + V_i)\,\psi_i - k\psi_{i+1} = E\psi_i
```

This is one equation per grid point, so the set of them is the matrix eigenvalue problem
$H\psi = E\psi$ with $H$ real, symmetric and tridiagonal:

```math
H=\begin{pmatrix}
2k{+}V_1 & -k & 0 & \cdots & 0 \\
-k & 2k{+}V_2 & -k & \cdots & 0 \\
0 & -k & 2k{+}V_3 & \ddots & \vdots \\
\vdots & & \ddots & \ddots & -k \\
0 & 0 & \cdots & -k & 2k{+}V_M
\end{pmatrix}
```

The grid is truncated at $i = 0$ and $i = M{+}1$, which imposes $\psi_0 = \psi_{M+1} = 0$: the
box walls. The spectrum of $H$ is therefore discrete.

Return $H$, and return $V$ as well so the potential can be checked independently of the solve.

## Step 2 — follow each state through the tilt  (`track`)

Diagonalise $H$ at each value of $\lambda$ in turn and keep the lowest two states. Match each
new eigenvector to the previous one by the overlap $|\langle \psi^{\text{new}} |
\psi^{\text{old}} \rangle|$, taking the largest.

Reason for matching: the two levels exchange character across $\lambda = 0$. Labelling by rank
in energy would swap the states there; labelling by overlap keeps each state's identity.

## Step 3 — rebuild the tilt grid at each width  (`sweep`)

For each barrier width $w$, build a fresh $\lambda$ grid spanning $\pm 12 \times 2t(w)$ in 21
points, always including $\lambda = 0$.

Reason for rescaling: $2t$ falls exponentially with $w$, so one fixed grid would step straight
over the crossing at the larger widths.

## Step 4 — project onto the localised states  (`measure`)

The solver returns the symmetric state $\psi_S$ and the antisymmetric state $\psi_A$, with
$\psi_A$ taken positive in the left well. Form the two localised orbitals:

```math
\phi_L = \tfrac{1}{\sqrt{2}}\left(\psi_S + \psi_A\right), \qquad
\phi_R = \tfrac{1}{\sqrt{2}}\left(\psi_S - \psi_A\right)
```

Each holds over 99 % of its probability in one well. In the basis $\lbrace \phi_L, \phi_R
\rbrace$ the two-state problem is

```math
H_{\mathrm{eff}}=\begin{pmatrix}
-\varepsilon/2 & -t \\
-t & +\varepsilon/2
\end{pmatrix},
\qquad \varepsilon = d\lambda
```

where $t = -\langle \phi_L | \hat{H} | \phi_R \rangle$ is the tunnelling energy, set by the
overlap of the two localised states inside the barrier, and $\varepsilon$ is the energy
difference the tilt opens between them. Only the well interiors are tilted, so $\varepsilon$ is
$\lambda$ reduced by $d$, the fraction of a localised state's $|\psi|^{2}$ lying inside its own
well.

Diagonalise $H_{\mathrm{eff}}$. Its two eigenvalues are separated by

```math
\Delta E(\lambda) = \sqrt{\varepsilon^{2}+4t^{2}}
                  = \sqrt{(d\lambda)^{2}+(2t)^{2}}
```

Fit this form to the computed gap across one sweep in $\lambda$ at fixed $w$. Two free
parameters, $d$ and $2t$; read both off the fit.

## Step 5 — fit the collapse with width

Repeat Step 4 at each $w$ to get $2t(w)$, then fit

```math
\ln 2t = \mathrm{const} - \kappa w
```

over $w \geq W_{\mathrm{FIT}} = 2.0$ nm only. Below that the wells couple strongly enough that
the local slope has not yet reached $-\kappa$.

The slope $-\kappa$ is the rate at which $\psi$ decays inside the barrier, set by how far the
state energy $E$ lies below the barrier top $V_0$:

```math
\kappa=\frac{\sqrt{2m_e(V_0-E)}}{\hbar}
```

## Step 6 — check each result by an independent route

Three quantities come out of the fits, and each is checked against a route that does not use
that fit:

| quantity | fitted from | checked against |
|---|---|---|
| $2t$ | the $\Delta E(\lambda)$ fit | the gap read directly at $\lambda = 0$ |
| $d$ | the $\Delta E(\lambda)$ fit | the fraction of $\vert\psi\vert^{2}$ inside one well, read off a localised state |
| $\kappa$ | the slope of $\ln 2t$ against $w$ | $\sqrt{2m_e(V_0-E)}/\hbar$, which uses the potential and the state energy alone, and no gap |

Results of all three, and of the grid and box convergence checks, are tabulated in the
[README](../README.md).
