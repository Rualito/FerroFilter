
# Running simulation on SFEpy

All of the following are required to get the problem instance
- Equations/terms [[#^bf2489]]
-  Fields 
- Materials

- Mesh [[#^66fe66]]
- Domain (Mesh)
- Domain subregions (boundaries, etc)
- Boundary conditions

- Integral
- Solvers


An initial understanding of how SFEpy works is in [[SFEpy tests]]



## Equations/terms 

^bf2489

#ferrofilter/equations

Look at 
 <div class="csl-bib-body" style="line-height: 1.35; ">
 <div class="csl-entry" style="clear: left; "> <div class="csl-left-margin" style="float: left; padding-right: 0.5em;text-align: right; width: 1em;">[1]</div><div class="csl-right-inline" style="margin: 0 .4em 0 1.5em;">I. Tomas,  FERROFLUIDS: MODELING, NUMERICAL ANALYSIS, AND SCIENTIFIC COMPUTATION, 2015. doi: <a href="https://doi.org/10.13016/M2RH0N">10.13016/M2RH0N</a>.</div>  
 </div></div>


This is a two-phase model, with ferrofluid+non-ferrofluid

Domain: $\Omega \subset\mathbb{R}^d, d=2,3$ 
Boundary of $\Omega$: $\Gamma$, is Lipschitz continuous

Diffuse interface model
- Tracks the position of the fluid implicitly with a phase-field variable $\theta$.
Evolution described by 
- velocity $\vec{u}$
- pressure $p$
- magnetization $\vec{m}$, induced by $\vec{h}$


 ### **Evolution of phase-field $\theta$ : Cahn-Hilliard model

$$ 
\partial_{t}\,\theta = -\gamma\, \Delta \psi \quad \text{in } \Omega
$$
$$ \psi = \varepsilon \ \Delta\theta - \frac{1}{\varepsilon} f(\theta) \quad \text{in }\Omega$$
$$ \partial_n \theta = \partial_n \psi = 0 \quad \text{in } \Gamma $$
where $\varepsilon$ is related to the interface thickness and $\gamma$ is the mobility. Then we also have $f(\theta)=F'(\theta)$, with $F(\theta)$ is the truncated double well potential

$$ F(\theta) = 
\begin{cases} (\theta+1)^2 & \theta \in (-\infty, -1] \\
 \frac{1}{4} (\theta^2-1)^2 & \theta\in[-1,1]\\
(\theta-1)^2& \theta\in[1,+\infty)\\
\end{cases}$$

```desmos-graph
    left=-5; right=5;
    top=5; bottom=-1;
    ---
    y=(x+1)^2|x<-1
    y=(x^2-1)^2/4 | -1<x<1
    y=(x-1)^2 | x>1
    
```

 ### **Simplified ferrohydrodynamics: Shliomis model

$$ \partial_m t + (\vec{u}\cdot\nabla) \vec u - \nu \Delta \vec u + \nabla p = \mu_0 (\vec m \cdot \nabla)\vec h + \frac{\mu_0}{2} \nabla \times (\vec u \times \vec h) $$
$$ \partial_t \vec m + (\vec u\cdot \nabla)\vec m - \frac{1}{2} (\nabla\times\vec u) \times\vec m = -\frac{1}{\mathcal T} (\vec m - \chi_0\, \vec h)-\beta \vec m \times (\vec m \times \vec h) $$

The parameters $\nu, \mu_0, \mathcal T, \beta, \chi_0$ are positive constants. We can simplify $\mathcal T\ll1$ in commertial grade ferrofluids ($\mathcal T$ is the relaxation time $\sim 10^{-9} - 10^{-5}$)

 ### **Simplified capilary forces

$$\vec f_c = -\nabla \cdot \hat\sigma_c $$
$$ \hat \sigma_c = \lambda \nabla \theta \times \nabla \theta $$ is the capilary stress tensor
 
 ### **Simplified electromagnetism

$$\nabla\times\vec h = 0 $$
$$ \nabla \cdot \vec B = 0 $$
$$ \vec B = \vec h +\vec m $$
$$ \vec h = \vec h_a + \vec h_d  $$ given magnetic field $\vec h_a$ and demagnetization field $\vec h_d$
$$ \begin{split} -\Delta\varphi = \nabla \cdot \vec m \quad \text{  in } \Omega \\
\partial_t \varphi = (\vec h_a-\vec m)\cdot \vec n \quad \text{on } \Gamma\end{split} $$
so that $\vec h = \nabla \varphi$

 ### **With all the simplifications

#ferrofilter/simple-pde 

These are the equations for the bulk
$$ \begin{split} 
\partial_t\, \theta + \nabla \cdot(\vec u \,\theta) + \gamma\Delta \psi &= 0 & (A)\\
-\varepsilon \Delta \theta + \frac{1}{\varepsilon} f(\theta) + \psi &= 0 & (B)\\
\partial_t \vec m + (\vec u\cdot \nabla) \vec m &= -\frac{1}{\mathcal{T}}  (\vec m - \chi_\theta \,\vec h)& (C)\\
-\Delta \varphi &= \nabla \cdot (\vec m-\vec h_a)& (D)\\
\partial_t\, \vec u + (\vec u\cdot\nabla)\vec u - \nabla \cdot (\nu_\theta\, \hat T(\vec u))  + \nabla p &= \mu_0(\vec m\cdot \nabla) \vec h + \frac{\lambda}{\varepsilon} \theta \nabla \psi \quad  & (E)\\
\nabla \cdot \vec u &= 0 & (F)
\end{split} $$
with $\hat T(\vec u) = \frac{1}{2} (\nabla \vec u + \nabla \vec u^T)$ being the symmetric gradient.

And boundary conditions

$$ \begin{split} \partial_n\, \theta &= \partial_n \, \psi\\
\vec u&=0\\
\partial_n \varphi &= (\vec h_a-\vec m)\cdot \vec n\end{split}$$
(we could also consider static flow and have $\partial_t\,\vec u=0$ on the boundary)

$\nu_\theta$ is the viscosity
$\chi_\theta$ is the susceptibility
						subordinate to the phase field $\theta$

these are functions such that
$0<\nu_w\leq \nu_\theta \leq \nu_f$
($\nu_w$ viscosity of non-magnetic, like water; $\nu_f$ viscosity of ferrofluid )
$0\leq\chi_\theta\leq\chi_0$
($\chi_0$ magnetic susecptibility of ferrofluid)

$\nu_\theta, \chi_\theta$ are arbitrary, but can use example
$$ \begin{split} \nu_\theta &= \nu_w + (\nu_f-\nu_w)\ \mathcal H \left( {\theta}/{\varepsilon} \right)\\
\chi_\theta &= \chi_0\ \mathcal H(\theta/\varepsilon)\end{split} $$
where $\mathcal H$ is an approximation of Heaviside (like sigmoid)
can also add gravitiational effects (approximate)
$$ \vec f_g = (1+ r \ \mathcal H(\theta/\varepsilon))\ \vec g $$
which we add to eq $(E)$. $r$ is
$$ r = \frac{|\rho_f-\rho_w|}{\min(\rho_f,\rho_w)} $$
and the approximation is valid when $r\ll1$




## Meshes

^66fe66

#ferrofilter/mesh 

[gmsh](http://gmsh.info/): 3D mesh generator
[pucgen](https://github.com/sfepy/pucgen): periodic unit cell generator

