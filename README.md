# split-range-MPC-2024
Supplementary material for the paper "A model predictive control interpretation of the generalized split-range control"

This sentence uses `$` delimiters to show math inline:  $\sqrt{3x-1}+(1+x)^2$

$$\left( \sum_{k=1}^n a_k b_k \right)^2 \leq \left( \sum_{k=1}^n a_k^2 \right) \left( \sum_{k=1}^n b_k^2 \right)$$

# Exemplo

Para demonstrar o uso da formulação proposta com sequenciamento dos atuadores, análogo a um controle split-range, simulou-se um processo com a seguinte dinâmica:
$$    Y(z^{-1}) = \dfrac{z^{-1}}{1-0.5z^{-1}} \left( 0.2 U_1(z^{-1}) + 0.15 U_2(z^{-1}) +0.15 U_3(z^{-1}) \right)$$

onde $Y(z^{-1})$ é a variável de processo e $U_i(z^{-1})$ é a i-ézima variável manipulada, sendo que estas últimas já estão ordenadas da mais barata para a mais cara, sendo $z^{-1}$ o operador de atraso discreto e a planta estando amostrada com um período $T_s = 0.1$ segundos.

Para comparar o desempenho das cinco estratégias para solução do MIQP apresentadas, foram realizados três experimentos diferentes, variando o tamanho dos horizontes e, portanto, o número de variáveis de decisão:
    \item MPC sintonizado com $N_1 = 1$, $N_2 = 3$, $N_u = 3$, $\gamma = 4$ e $\lambda = 1$, totalizando 9 variáveis de decisão contínuas e 6 binárias;
    \item MPC sintonizado com $N_1 = 1$, $N_2 = 10$, $N_u = 5$, $\gamma = 4$ e $\lambda = 2$, totalizando 15 variáveis de decisão contínuas e 10 binárias;
    \item MPC sintonizado com $N_1 = 1$, $N_2 = 20$, $N_u = 10$, $\gamma = 4$ e $\lambda = 4$, totalizando 30 variáveis de decisão contínuas e 20 binárias.


Adicionalmente, para as estratégias 1, 4 e 5 testou-se um cenário com o MPC sintonizado com $N_1 = 1$, $N_2 = 40$, $N_u = 20$, $\gamma = 4$ e $\lambda = 8$, totalizando 60 variáveis de decisão contínuas e 40 binárias.



Para montar o MIQP, as variáveis de decisão são empilhadas em um vetor:
$$   X = \left[
\begin{array}{c}
\Delta U_1 \\
\Delta U_2 \\
\Delta U_3 \\
\delta_1 \\
\delta_2 \\
\end{array}
\right],$$

onde cada $\Delta U_i$ é, ele próprio, um vetor contendo os incrementos de controle no horizonte $\Delta u_i(k), ..., \Delta u_i (k+N_u-1)$, sendo $N_u$ o tamanho do horizonte de controle, e $\delta_i$  é um vetor contendo as variáveis binárias no horizonte $\delta_i(k), ..., \delta_i (k+N_u-1)$, pode-se escrever as restrições do MIQP no formato:


   $$ A_{nq} X \leq b_{nq},$$

sendo: 
$$
    A_{nq} = \begin{bmatrix}
T & 0 & 0 & 0 & 0 \\
-T & 0 & 0 & I & 0 \\
0 & T & 0 & -I & 0 \\
0 & -T & 0 & 0 & I \\
0 & 0 & T & 0 & -I \\
0 & 0 & -T & 0 & 0 \\
\end{bmatrix}, 
$$
onde $T$ é um bloco triangular inferior unitária de dimensão $N_u \times N_u$, e: 
$$
    b_{nq} = \begin{bmatrix}
(1-u_1(k-1)) U \\
u_1(k-1) U \\
-u_2(k-1) U  \\
u_2(k-1) U  \\
-u_3(k-1) U  \\
u_3(k-1) U  \\
\end{bmatrix}, 
$$
onde $U$ é um vetor preenchido de uns com comprimento $N_u$.

Adicionalmente, a função objetivo será escrita como:
$$
    J = \dfrac{1}{2} X^T H_o X + q_o^T X,
$$
onde:
$$
   H_o = \begin{bmatrix}
2(G^T Q_\gamma G + Q_\lambda) & 0 \\
0 & 0
\end{bmatrix}, 
$$
sendo $Q_\gamma$ a matriz diagonal com a ponderação no erro quadrático e $Q_\lambda$ a matriz diagonal contendo as ponderações para o esforço de controle de cada entrada, também com os $0$ de dimensões apropriadas para o número de variáveis binárias de $X$ e:
$$
   q_o = \begin{bmatrix}
(2(f-W)^T Q_\gamma G)^T \\ 0
\end{bmatrix}, 
$$
com o $0$ de dimensão apropriada para o número de variáveis binárias nas linhas finais de $X$.

Assim, o problema a ser resolvido a cada instante de tempo para calcular o incremento de controle ótimo a ser aplicado ao processo é:

$$
\begin{aligned}
\min \quad & \dfrac{1}{2} X^T H_o X + q_o^T X\\
\textrm{s.a.} \quad & A_{nq} X \leq b_{nq}\\
\end{aligned}
$$

