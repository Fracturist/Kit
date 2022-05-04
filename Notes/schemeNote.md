# Scheme Notes

## 1. Runge-Kutta Scheme

**ODE:**

$$
\begin{gather}
    \frac{dy}{dt} = f(t, y),\quad y(t_0) = y_0.
\end{gather}
$$

**4th order (RK4):**

$$
\begin{align}
    k_1 &= f(t_n, y_n), \\
    k_2 &= f \left( t_n + \frac h 2, y_n + \frac h 2 k_1 \right), \\
    k_3 &= f \left( t_n + \frac h2, y_n + \frac h2 k_2 \right), \\
    k_4 &= f(t_n + h, y_n + h k_3).
\end{align}
$$

$$
\begin{gather}
    t_{n+1} = t_n + h, \\
    y_{n+1} = y_n +\frac16 \left( k_1 + 2k_2 + 2k_3 + k_4 \right).
\end{gather}
$$

**3rd order (RK3):**

$$
\begin{align}
    k_1 &= f(t_n, y_n), \\
    k_2 &= f \left( t_n + \frac h 2, y_n + \frac h 2 k_1 \right), \\
    k_3 &= f \left[ t_n + h, y_n + h (2k_2 - k1) \right]. \\
\end{align}
$$

$$
\begin{gather}
    t_{n+1} = t_n + h, \\
    y_{n+1} = y_n +\frac16 \left( k_1 + 4k_2 + k_3 \right).
\end{gather}
$$

**Incompressible pressure-based solver version:**

1. Navier—Stokes equations for incompressible flow

$$
\begin{gather}
    \nabla \cdot \mathbf{u} = 0 \\
    \frac{\partial \mathbf{u}}{\partial t} + \nabla\cdot(\mathbf{u}\otimes\mathbf{u}) = \nabla \cdot (\nu_{\text{eff}} \nabla \mathbf{u}) - \nabla \left( \dfrac{p}{\rho} \right) + \mathbf{f}
\end{gather}
$$

> **Note:**
> For ***laminar*** viscous stress, we have
> $$
  \begin{gather*}
      \sigma_{ij} = \lambda S_{k,k}\delta_{ij} + 2\mu S_{ij} = \lambda u_{k,k}\delta_{ij} + \mu( u_{i,j} + u_{j,i} ) \\
      \pmb{\sigma} = \lambda(\nabla\cdot\mathbf{u})\mathbf{I} + \mu\left( \nabla\mathbf{u} + (\nabla\mathbf{u})^\mathrm{T} \right)
  \end{gather*}
  $$ or decompose the stress tensor into isotropic and deviatoric parts and get
> $$
  \begin{gather*}
      \sigma_{ij} = \left( \lambda + \tfrac{2}{3} \mu \right) u_{k,k}\delta_{ij} + \mu\left( u_{i,j} + u_{j,i} - \tfrac23u_{k,k}\delta_{ij} \right) \\
      \pmb{\sigma} = \left( \lambda + \tfrac{2}{3}\mu \right)(\nabla\cdot\mathbf{u})\mathbf{I} + \mu\left( \nabla\mathbf{u} + (\nabla\mathbf{u})^\mathrm{T} - \tfrac{2}{3}\mu(\nabla\cdot\mathbf{u})\mathbf{I} \right)
  \end{gather*}
  $$ then calculate Cauchy's stress principle $ \sigma_{ji,j} $ or $ \nabla\cdot\pmb{\sigma} $
> $$
  \begin{gather*}
      \sigma_{ji,j} = (\lambda u_{k,k})_{,i} + (\mu u_{i,j})_{,j} + (\mu u_{j,i})_{,j} \\
      \nabla\cdot\pmb{\sigma} = \nabla(\lambda(\nabla\cdot\mathbf{u})) + \nabla\cdot(\mu\nabla\mathbf{u}) + \nabla\cdot\left(\mu(\nabla\mathbf{u})^{\mathrm{T}}\right)
  \end{gather*}
  $$ and assuming $\lambda$ and $\mu$ to be constant we have
> $$
  \begin{gather*}
      \sigma_{ji,j} = (\lambda+\mu) u_{k,ki} + \mu u_{i,jj} \\
      \nabla\cdot\pmb{\sigma} = \mu \nabla^2\mathbf{u} + (\lambda + \mu)\nabla(\nabla\cdot\mathbf{u})
  \end{gather*}
  $$ For ***turbulent*** Reynolds stress, we have
> $$
  \begin{gather*}
      \mathbf{R} - \tfrac{2}{3}\rho k\mathbf{I} = 2\mu_t\left( \mathbf{S} - \tfrac{1}{3}(\nabla\cdot\mathbf{u})\mathbf{I} \right)
  \end{gather*}
  $$ then $ \nabla \cdot \mathbf{R} $ will be
> $$
  \begin{align*}
      \nabla\cdot\mathbf{R} &= \tfrac{2}{3}\nabla(\rho k) - \tfrac{2}{3}\nabla\left( \mu_t(\nabla\cdot\mathbf{u}) \right) + \nabla\cdot(\mu_t\nabla\mathbf{u}) + \nabla\cdot\left(\mu_t(\nabla\mathbf{u})^{\mathrm{T}}\right) \\
      &= \mu_t\nabla^2\mathbf{u} + \tfrac{1}{3}\mu_t\nabla(\nabla\cdot\mathbf{u})+\tfrac{2}{3}\nabla(\rho k)
  \end{align*}
  $$ For incompressible flow with constant properties, we get
> $$
  \begin{gather*}
      \nabla\cdot\mathbf{T} = (\mu+\mu_t)\nabla^2 \mathbf{u} + \tfrac{2}{3}\nabla(\rho k)
  \end{gather*}
  $$

Using **Fraction Step Method** or **Projection Method**,

* 1st step:
  $$
    \frac{\mathbf{u}^{*}-\mathbf{u}^n}{\Delta t} = \left\lbrace -\nabla\cdot(\mathbf{u}\otimes\mathbf{u}) + \nabla\cdot\mathbf{T} + \mathbf{f} \right\rbrace_{n} = G(\mathbf{u}^n)
  $$
* 2nd step:
  $$
    \nabla^2 p^{n+1} = \frac{1}{\Delta t}(\nabla\cdot\mathbf{u}^* -   \nabla\cdot\mathbf{u}^{n+1}) = \frac{1}{\Delta t}\nabla\cdot\mathbb{u}^*
  $$
* 3rd step:
  $$
    \mathbf{u}^{n+1} = \mathbf{u}^* - \Delta t \, \nabla p^{n+1}
  $$

If employing **RK4 scheme**,

1. Calculate $ k_1 = (\mathbf{u}^{n+1}_1 - \mathbf{u}^n)/\Delta t = G(\mathbf{u}^n) - \nabla p^{n+1}_1 $
2. Calculate $ k_2 = (\mathbf{u}^{n+1}_2 - \mathbf{u}^n)/\Delta t = G(\mathbf{u}^n+k_1 \, \Delta t/2) - \nabla p^{n+1}_2 = G(0.5\mathbf{u}^n + 0.5\mathbf{u}^{n+1}_1) - \nabla p^{n+1}_2 $
3. Calculate $ k_3 = (\mathbf{u}^{n+1}_3 - \mathbf{u}^n)/\Delta t = G(\mathbf{u}^n+k_2 \, \Delta t/2) - \nabla p^{n+1}_3 = G(0.5\mathbf{u}^n + 0.5\mathbf{u}^{n+1}_2) - \nabla p^{n+1}_3 $
4. Calculate $ k_4 = (\mathbf{u}^{n+1}_4 - \mathbf{u}^n)/\Delta t = G(\mathbf{u}^n+k_3 \, \Delta t) - \nabla p^{n+1}_4 = G(\mathbf{u}^{n+1}_3) - \nabla p^{n+1}_4 $
5. Calculate $ \mathbf{u}^{n+1} = \mathbf{u}^n + \frac{\Delta t}6 (k_1 + 2k_2 + 2k_3 + k_4) = \frac16( \mathbf{u}^{n+1}_1 + 2\mathbf{u}^{n+1}_2 + 2\mathbf{u}^{n+1}_3 + \mathbf{u}^{n+1}_4 ) $ and $ p^{n+1} = \frac{1}{6}( p^{n+1}_1 + 2p^{n+1}_2 + 2p^{n+1}_3 + p^{n+1}_4 ) $

After simplified,
```cpp
U_n = U;

// k_1

#include "projectionMethod.H"

U_tmp = 1./6.*U;
p_tmp = 1./6.*p;

U = (U + U_n)/2.;
U.correctBoundaryConditions();
fvOptions.correct(U);
phi = fvc::flux(U);


// k_2

#include "projectionMethod.H"

U_tmp += 1./3.*U;
p_tmp += 1./3.*p;

U = (U + U_n)/2.;
U.correctBoundaryConditions();
fvOptions.correct(U);
phi = fvc::flux(U);


// k_3

#include "projectionMethod.H"

U_tmp += 1./3.*U;
p_tmp += 1./3.*p;

U.correctBoundaryConditions();
fvOptions.correct(U);
phi = fvc::flux(U);


// k_4

#include "projectionMethod.H"

U_tmp += 1./6.*U;
p_tmp += 1./6.*p;

p = p_tmp;
p.correctBoundaryConditions();
U = U_tmp;
U.correctBoundaryConditions();
fvOptions.correct(U);
phi = fvc::flux(U);
```

projectionMethod.H

```cpp
U = runTime.deltaT()*G(U);             // Compute U*
U.correctBoundaryConditions();

#include "pEqn.H"

U -= fvc::grad(p)*runTime.deltaT();    // Compute U^{n+1}
```

pEqn.H

```cpp
{
    fvScalarMatrix pEqn
    (
        fvm::laplacian(p)
     ==
        fvc::div(U)/runTime.deltaT()
    );                                 // Solve pressure poisson equation

    pEqn.setReference(pRefCell, pRefValue);

    pEqn.solve();
}
```
