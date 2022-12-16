
# Weak formulation
#weak-formulation
The first step is to convert the PDE into the weak formulation. Consider the equation 
$$ \mathcal L_{t,\vec x}\ y = f(t, \vec x, y) $$
where $\mathcal L_{t, \vec x}$ is a differential operator (any combination of derivatives) that acts on $y(t, \vec x)$. $f(\vec x, y)$ is some function. The purpose of the weak formulation is to convert second-order derivatives into first-order, by introducing a test function $\varphi(t,\vec x)$. Then you apply the functional dot product with the above equation, to get
$$ \int_\Omega \varphi(t, \vec x) (\mathcal L_{t, \vec x}\ y) d\vec x = \int_\Omega f(t, \vec x, y) d\vec x $$
Using integration by parts and calculus theorems, one can often get the weak form of the equations, often involving integrals over the boundary of $\Omega$: $\int_\Gamma dS$. To find out how to describe each of these terms in the python code, check [sfepy.org/terms_overview](https://sfepy.org/doc-devel/terms_overview.html)

# Solving a static magnetic field - 2D
## equations
Consider the following magnetostatic equations (without electric current)
$$ \begin{split} 
\vec B &= \vec h +\vec m\\
\nabla \cdot\vec B &=0\\
\nabla\times\vec h&=0
\end{split} $$
with unknowns $\vec B, \vec h$ and known $\vec m$ defined.

## Regions

Magnet region: $x_m, y_m, w_m, h_m$
$\Omega$: $w_\Omega$, $h_\Omega$, centered at $(x,y)=(0,0)$


## Mesh

[sfepy/preprocessing_mesh](https://sfepy.org/doc-devel/preprocessing.html)
[sfepy/mesh_param](https://sfepy.org/doc-devel/splinebox.html)




