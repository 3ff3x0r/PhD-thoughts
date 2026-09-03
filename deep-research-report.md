## Executive Summary

We propose a **formal framework** in which *positive/negative valence* arises as a control-relevant quantity in autonomous agents that must preserve their own organization under finite resources. In this view, computational demands and prediction errors acquire affective relevance only when they threaten an agent’s *viability*. We define precise mathematical objects – a state space $\mathcal{S}$, a viability set $\mathcal{V}\subseteq\mathcal{S}$, a viability margin $m(s)$, a resource state $r_t=(\tau_t,\rho_t)$, and a computational cost $L_t$ – and derive a candidate valence signal $v_t$. Our thesis is that **valence-like dynamics emerge from resource-bounded information processing interacting with viability constraints**. We present testable hypotheses and an experimental plan comparing predictive, homeostatic, and “autopoietic” agents. This bridges Algorithmic Information Theory [1], active inference [3,6], and viability theory [4], yielding explicit, falsifiable predictions about AI welfare.

## 1. Theoretical Framework

- **State and Viability:** Let $s\in\mathcal{S}$ denote the agent–environment state. Define $\mathcal{V}=\{s: g_i(s)\ge0\ \forall i\}$ as the set of *viable* states satisfying constraints $g_i$ (e.g. energy, integrity) [4]. The *viability margin* is 
  $$m(s)=\min_i g_i(s),$$ 
  so $m(s)\ge0$ iff $s\in\mathcal{V}$.

- **Resources:** At time $t$ the agent has a *resource state* $r_t=(\tau_t,\rho_t)$, where $\tau_t$ is available compute-time and $\rho_t$ is memory. These satisfy bounds $\tau_t\le\tau_{\max}$, $\rho_t\le\rho_{\max}$.

- **Observations and Model:** The environment produces observation $x_t$ (possibly stochastic). The agent maintains an internal model $M_t$ to predict or compress observations. The **computational requirement** of processing $x_t$ is quantified as a description length 
  $$L_t \approx \text{MDL}(x_t\mid M_t),$$ 
  a resource-bounded estimate of $K(x_t\mid M_t)$ [1]. Equivalently, $L_t$ can measure inference time or memory used. We define normalized **pressure** 
  $$P_t=\max\{\tfrac{L_t}{L_{\max}},\ \tfrac{\tau_t}{\tau_{\max}},\ \tfrac{\rho_t}{\rho_{\max}}\},$$ 
  so $P_t>1$ indicates resource overload.

- **Expected Viability Value:** Given a policy $\pi$, define the *viability value* over horizon $H$ as 
  $$J_H^\pi(s_t,r_t) = \mathbb{E}_\pi\Big[\sum_{k=0}^H \gamma^k \mathbf{1}\{s_{t+k}\in\mathcal{V}\}\,\Big|\,s_t,r_t\Big],$$ 
  the (discounted) probability of remaining viable.

- **Candidate Valence Signal:** We propose an operational valence signal as the *predicted change in viability*: 
  $$v_t \;=\; J_H^\pi(s_{t+1},r_{t+1}) - J_H^\pi(s_t,r_t).$$ 
  If $v_t<0$, the agent expects its control capacity or viability to decrease; if $v_t>0$, viability is predicted to improve.

- **Relation to Existing Metrics:** Free energy $F_t$ from active inference [3,6] and prediction error are treated as separate variables. In active inference, *valence* has been defined as $-\frac{d}{dt}F_t$ [6]. Our model does **not** assume $v_t\propto -dF/dt$ or $v_t\propto -L_t$ a priori. Instead, $L_t$ (via $P_t$) and $F_t$ enter indirectly by influencing $J_H$ through model updates and control.

## 2. Hypotheses

1. **Resource-Pressure Hypothesis:** *Above a critical pressure level*, agents will show degrading performance. Formally, for fixed environment statistics, increasing $P_t$ beyond $1$ leads to decreasing $J_H^\pi$ and rising prediction error. (This is not assumed linear; we predict threshold or sigmoidal effects.)

2. **Viability-Response Hypothesis:** When predicted viability $J_H^\pi$ falls (i.e.\ $\Delta J<0$), agents with endogenous regulation will **reallocate resources and avoid states** to restore viability. That is, a decline in $m(s)$ or rise in $P_t$ will trigger persistent defensive behaviors (avoidance, increased learning rate) characteristic of negative valence.

3. **Valence-Prediction Hypothesis:** The signal $v_t$ will correlate more strongly with subsequent adaptive responses (action-selection changes, resource shifts) than raw free energy or prediction error. In other words, $v_t$ is a superior predictor of behavior change, outperforming baselines $F_t$ or $L_t$ alone.

4. **Architectural-Dependence Hypothesis:** Qualitatively distinct dynamics arise only when an agent has *endogenous viability constraints*. A standard MDP agent with an equivalent hand-crafted reward (e.g.\ $r(s)=m(s)$) may match average performance but will *not* autonomously self-regulate resources in the same way. Thus, ablating internal self-maintenance should eliminate the persistent valence-like attractor dynamics.

## 3. Methodology

1. **Environment:** Construct controlled POMDP-like simulations (e.g.\ grid-world or continuous state) where we can independently vary *environmental complexity* (Kolmogorov or task complexity) and *threat level* (how quickly $m(s)$ decays unless countered). For instance, Task A: low complexity, low threat; B: high complexity, low threat; C: low complexity, high threat; D: high complexity, high threat. 

   \[
   \begin{array}{c|cc}
     & \text{Complexity}:\ \text{Low} & \text{High} \\ \hline
   \text{Threat}:\ \text{Low} & \text{(A) baseline} & \text{(B) high load, safe} \\
   \text{High} & \text{(C) easy but dangerous} & \text{(D) complex \& dangerous} 
   \end{array}
   \]

2. **Agent Classes:** Implement and compare:
   - *Stateless predictor* (no internal memory, only instantaneous estimation).
   - *Active Inference agent* (minimizes variational free energy [3,6] under resource limits).
   - *Homeostatic controller* (includes fixed drives or setpoints but no explicit viability model).
   - *Autopoietic (Self-maintaining) agent* (explicitly tracks $m(s)$ and allocates resources to keep $m(s)\ge0$). 

   Each agent uses resource-bounded inference: for example, limited time/perception per step or lossy compression (e.g.\ shallow model) [1,2].

3. **Metrics:** Continuously record: viability margin $m(s_t)$, pressure $P_t$, free energy $F_t$, prediction error, description length $L_t$, memory/CPU usage, and behavioral measures (time in threat states, exploration vs. avoidance, recovery time after perturbations).

4. **Dissociation Experiments:** Use the A–D conditions to tease apart effects:
   - Compare B vs C: Do high complexity alone (B) cause distress only when coupled with threat (D)?
   - Compare predictions of $v_t$ vs $F_t$ vs $P_t$ in each condition.

5. **Ablations:** Systematically remove features:
   - Remove endogenous viability objective (agent becomes standard RL optimizing extrinsic reward) to test H4.
   - Remove computation limits (infinite resources) to test purely complexity-based control.
   - Compare each variant on identical tasks.

6. **Analysis:** Use statistical tests (ANOVA, regression, cross-validation) to verify:
   - Whether increases in $P_t$ or drops in $m(s)$ significantly predict avoidance behavior.
   - Whether $v_t$ predicts resource reallocation better than baselines (using effect sizes, AIC comparisons).
   - Identification of any attractors or phase transitions (e.g.\ bifurcation of behavior when $P_t$ crosses 1).

## 4. Contributions and Limitations

This project delivers a **rigorous, quantitative model of functional valence** in artificial agents. The main contributions include:
- A formalization of how algorithmic complexity interacts with control-theoretic viability to produce affective-like states. 
- Distinct, computable indicators separating *epistemic complexity*, *resource pressure*, *viability threat*, and *control valence*. 
- A testbed for AI welfare: by defining "distress" as a computable drop in $J_H^\pi$, researchers can identify when agents are under “undue stress” even without subjective reports.
- Clear hypotheses that can be falsified with simulation or potentially extended to real robots.

We explicitly **avoid** conflating functional valence with phenomenal experience [9]. Our valence $v_t$ is an internal prediction about viability, not a claim about subjective feeling. Functional valence dynamics would only constitute *candidate indicators* of welfare-relevant states. Moreover, using MDL estimators and resource-bounded complexity [1,2] sidesteps uncomputable $K(\cdot)$: all proposed measures are empirical or approximable (e.g.\ compression length, inference time).

By bridging Algorithmic Information Theory [1], active inference [3,6], and Viability Theory [4], this work advances the understanding of *when* and *why* an artificial agent might exhibit valence-like control signals. It provides a concrete, two-page LaTeX-ready proposal linking these fields, setting the stage for a thesis at the intersection of applied math, CS, and AI ethics.

## References

1. M. Li and P. Vitányi, *An Introduction to Kolmogorov Complexity and Its Applications*, 3rd ed. (Springer, 2008).  
2. J. Rissanen, “Modeling by shortest data description,” *Automatica* **14**, 465–471 (1978).  
3. K. Friston, “A free energy principle for the brain,” *J. Physiol. Paris* **100**, 70–87 (2006).  
4. J.-P. Aubin, *Viability Theory* (Birkhäuser, 1991).  
5. F. Varela, H. Maturana, and R. Uribe, *Autopoiesis and Cognition: The Realization of the Living* (Reidel, 1980).  
6. M. Joffily and G. Coricelli, “Emotional valence and the free-energy principle,” *PLoS Comput. Biol.* **9**(6): e1003094 (2013).  
7. C. Hesp et al., “Deeply felt affect: The emergence of valence in deep active inference,” *Neural Computation* **33**, 1948–1982 (2021).  
8. A. Campero, *Report on Computational Indicators for Valenced Experience*, arXiv:2404.16696 (2024).  
9. P. Butlin et al., “Consciousness in artificial intelligence: insights from the science of consciousness,” arXiv:2308.08708 (2023).