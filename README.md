![blue_birds_disney_smaller_50](https://github.com/user-attachments/assets/56384332-23a1-4770-9754-33ae0aa641b7)

# Birds-on-Wire Model

In this project we study a stochastic model for the self-organization of $N$ birds perched along a telephone wire. Each bird repeatedly adjusts position to reduce the discomfort caused by nearby neighbors, leading to a continuous-time Markov jump process whose long-term behavior can be analyzed analytically and explored numerically.

## Motivation

The idea stems from the Pixar short *[For the Birds](https://www.youtube.com/watch?v=Q6X80IWdS6s)*, where crowding triggers a cascade of movement. Our goal is to turn that intuition into a mathematically tractable model that captures how local interactions and exclusion constraints shape collective configurations.

## Discrete Wire Model

We discretize the wire $[0,1]$ into $L-1$ interior lattice points

$$
X_L = \left\{\tfrac{1}{L}, \tfrac{2}{L}, \ldots, \tfrac{L-1}{L}\right\},
$$

and track ordered bird positions $X_t = (X_t^1, X_t^2, \ldots, X_t^N)$ in the state space

$$
E_L = \{x \in X_L^N : 0 = x_0 < x_1 < \cdots < x_N < x_{N+1} = 1\}.
$$

The boundary points $x_0 = 0$ and $x_{N+1} = 1$ act as fixed "ghost birds" that keep the flock on the wire while enforcing minimum spacing.

## Discomfort-Driven Dynamics

Birds become uncomfortable when neighbors are too close. We encode this with a decreasing penalty $\phi_L : X_L \to [0, \infty)$ and define the cumulative left/right discomfort felt by bird $n$ in configuration $x \in E_L$ as

$$
\Phi_{n,1}(x) = \sum_{k=0}^{n-1} \phi_L(x_n - x_k), \qquad
\Phi_{n,2}(x) = \sum_{k=n+1}^{N+1} \phi_L(x_k - x_n).
$$

Instantaneous moves are triggered by two independent exponential clocks per bird: one for shifting left and one for shifting right. Their rates are

$$
\lambda_{n,1}(x) = \frac{1}{L} \frac{\Phi_{n,1}(x)}{\lvert \Phi_{n,1}(x) - \Phi_{n,2}(x) \rvert} + \eta_L(x_n, x_{n-1}), \qquad
\lambda_{n,2}(x) = \frac{1}{L} \frac{\Phi_{n,2}(x)}{\lvert \Phi_{n,1}(x) - \Phi_{n,2}(x) \rvert} + \eta_L(x_{n+1}, x_n),
$$
where the collision barrier
$$
\eta_L(x,y) = \infty \cdot \mathbf{1}_{\lvert x-y \rvert = 1/L}
$$
eliminates jumps that would place two birds on adjacent lattice sites. When the minimum of all active clocks rings, the corresponding bird hops one lattice step in that direction.

## Markov Jump Process Formulation

Collecting all rates yields a continuous-time Markov jump process on $E_L$. The total exit rate from state $x$ is

$$
\Lambda(x) = \sum_{n=1}^{N} \sum_{i \in \{1,2\}} \lambda_{n,i}(x),
$$

and the transition probability to a neighboring configuration $y$ that moves bird $n$ one step left ($i=1$) or right ($i=2$) is

$$
P(x,y) =
\begin{cases}
\lambda_{n,i}(x) / \Lambda(x), & \|x - y\|_1 = 1/L, \\[4pt]
0, & \text{otherwise.}
\end{cases}
$$

This reformulation, detailed in the project report, simplifies proofs of ergodic properties and enables direct simulation.

## Long-Term Behavior

The report proves a Doeblin condition for the chain, ensuring the existence of a unique stationary distribution and exponential convergence in total variation. A key ingredient is the notion of **stable configurations** $x_L \in E_L$ satisfying

$$
\Phi_{n,1}(x_L) = \Phi_{n,2}(x_L), \qquad \forall n = 1, \ldots, N,
$$
and a partition of the state space into "too-close" and "not-too-close" regions that guarantees irreducibility even when no stable point exists.

Under mild assumptions on $\phi_L$, the stable configuration is unique and uniformly spaced, with minimum gap lower bounded by a constant that is independent of $L$ for large lattices. Numerical experiments from the accompanying notebook support the conjecture that, for sufficiently large $L$,

$$
\mathbb{P}\big(\|X_t^L - \bar{x}\|_1 \le 0.165 \cdot L^{-0.488}\big) \ge 0.95 \qquad \text{as } t \to \infty,
$$
where $\bar{x}$ is the continuous stable profile solving $\Phi_{n,1}(\bar{x}) = \Phi_{n,2}(\bar{x})$.

## Repository Contents

- `Birds_on_wire_Model_Barriviera_Yan.pdf` - full write-up with proofs of the Markov jump formulation and ergodic analysis.
- `For_the_birds_ Barriviera_Yan.ipynb` - simulation notebook that reproduces the stochastic dynamics and explores empirical convergence.
- `birds_moving_simulation.gif` - animation of a sample trajectory illustrating the asynchronous jumps and gradual spacing.
- `README.md` - project overview (this file).

## Authors and Course Context

This project was created by Luca Barriviera and Wenbin Yan for the Master's-level course *Markov Processes and Applications* taught by Professors Vincent Bansaye and Clement Rey.

![birds_moving_simulation](https://github.com/user-attachments/assets/a29a5d92-8e0c-443a-8b50-eb83be7ef765)
