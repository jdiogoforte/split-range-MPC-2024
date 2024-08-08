# Supplementary material for the paper "A model predictive control interpretation of the generalized split-range control"

Text

# Additional Results

Text

# GPC Formulation

Text

# SRGPC Formulation

Text

# Additional Exemple

To demonstrate the use of the proposed formulation with actuator sequencing, analogous to a split-range control, a process with the following dynamics was simulated:

$$    Y(z^{-1}) = \dfrac{z^{-1}}{1-0.5z^{-1}} \left( 0.2 U_1(z^{-1}) + 0.15 U_2(z^{-1}) +0.15 U_3(z^{-1}) \right)$$

where $Y(z^{-1})$ is the process variable and $U_i(z^{-1})$ is the i-th manipulated variable, with the latter already ordered from cheapest to most expensive, $z^{-1}$ being the discrete delay operator and the plant being sampled with a period of $T_s = 0.1$ seconds.

To assemble the MIQP problem, the decision variables are stacked in a vector:

$$ X = \left[
\begin{array}{c}
\Delta U_1 \\\
\Delta U_2 \\\
\Delta U_3 \\\
\delta_1 \\
\delta_2 \\
\end{array}
\right],$$

where each $\Delta U_i$ is itself a vector containing the control increments at horizon $\Delta u_i(k), ... , \Delta u_i (k+N_u-1)$, where $N_u$ is the size of the control horizon, and $\delta_i$ is a vector containing the binary variables in the horizon $\delta_i(k), ..., \delta_i (k+N_u-1)$, you can write the MIQP constraints in the form:


   $$ A_{nq} X \leq b_{nq},$$

where: 

$$
    A_{nq} = \begin{bmatrix}
T & 0 & 0 & 0 & 0 \\\
-T & 0 & 0 & I & 0 \\\
0 & T & 0 & -I & 0 \\\
0 & -T & 0 & 0 & I \\\
0 & 0 & T & 0 & -I \\\
0 & 0 & -T & 0 & 0 \\\
\end{bmatrix}, 
$$

where $T$ is a unit lower triangular block of dimension $N_u \times N_u$, and: 

$$ b_{nq} = \begin{bmatrix} (1-u_1(k-1)) U \\ u_1(k-1) U \\ -u_2(k-1) U \\ u_2(k-1) U \\ -u_3(k-1) U \\ u_3(k-1) U \\end{bmatrix},$$

where $U$ is a vector filled with ones of length $N_u$.

Additionally, the objective function will be written as:

$$
    J = \dfrac{1}{2} X^T H_o X + q_o^T X,
$$

where:

$$
   H_o = \begin{bmatrix}
2(G^T Q_\gamma G + Q_\lambda) & 0 \\\
0 & 0
\end{bmatrix}, 
$$

where $Q_\gamma$ is the diagonal matrix with the weighting in the quadratic error and $Q_\lambda$ is the diagonal matrix containing the weightings for the control effort of each input, also with the $0$ of appropriate dimensions for the number of binary variables of $X$ and:

$$
   q_o = \begin{bmatrix}
(2(f-W)^T Q_\gamma G)^T \\\ 0
\end{bmatrix}, 
$$

with the $0$ dimension appropriate for the number of binary variables in the final lines of $X$.

Thus, the problem to be solved at each instant of time to calculate the optimal control increment to be applied to the process is:

$$
\begin{aligned}
\min \quad & \dfrac{1}{2} X^T H_o X + q_o^T X\\
\textrm{s.a.} \quad & A_{nq} X \leq b_{nq}\\
\end{aligned}
$$



