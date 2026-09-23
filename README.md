<div align="center">

<h1>Learning Gait-Aware Quadruped Locomotion with Temporal Logic Specifications</h1>

<p>
  <a href="https://github.com/M-Atasever/STL-based-Quadruped-Locomotion">[Github repo]</a> &nbsp;&nbsp;|&nbsp;&nbsp;
  <a href="https://arxiv.org/abs/2607.00442">[Manually designed STL specs]</a> &nbsp;&nbsp;|&nbsp;&nbsp;
  <a href="https://github.com/M-Atasever/llm-stl-locomotion">[LLM-generated STL specs Github repo]</a> &nbsp;&nbsp;|&nbsp;&nbsp;
  <a href="https://arxiv.org/abs/2609.07111">[LLM-generated STL specs]</a>
</p>

<img src="assets/quadruped%20model.png" width="500" alt="Barkour Quadruped Model">

</div>

<hr>

## Abstract

Reinforcement learning (RL) for quadruped locomotion commonly depends on fixed, hand-crafted, and Markovian reward functions that may limit interpretability of learned policies and may lack explicit control over gait behaviors. We introduce a framework where distinct gaits are specified using parameterized constraints expressed in Signal Temporal Logic (STL). These include safety bounds, gait synchronization constraints, command tracking, and actuation bounds. From these specifications, we develop a reward shaping mechanism that provides learning agents a dense, continuous reward landscape that encodes desired behavior. We define parametric STL templates for three speed regimes (walking-trot, trot, bound), calibrate their parameters from reference rollouts, and compute rewards from using smooth approximations of STL robustness over the rollouts. The generated rewards can be used to provide shaped gradients compatible with Proximal Policy Optimization (PPO). We instantiate the approach on Google's Barkour quadruped robot in MuJoCo XLA (MJX). We use parallelization within the simulator to improve training speeds and use domain randomization to robustify learned policies. Compared with hand-crafted rewards, an expert-switching oracle, and Text2Reward, Human-STL maintains high command-tracking success across the evaluated speed range while exhibiting substantially higher consistency with the intended speed-dependent gait structures. Videos can be found on our project website: https://stl-locomotion.github.io/.

<br>

<div align="center">
<img src="assets/pipeline.png" width="800" alt="Algorithm Pipeline">
</div>

<br>
<hr>

## Methodology

Our framework aims interpretable specification-based, gait-aware reward design for quadruped locomotion tasks. The reward component corresponds directly to human-readable requirements.

### 1. Feature Extraction
We compile trajectory datasets from specialized models corresponding to low-speed, mid-speed, and high-speed regimes. Extracted features include:
* **Tracking features:** Linear and angular velocities.
* **Safety/stability features:** Center of Mass (CoM), Base roll/pitch, and slip proxy.
* **Contact-pattern features:** Stride period, duty factor, and diagonal phase error.

### 2. Parametric STL (PSTL) Templates
We define fixed PSTL templates for three locomotion modes, fitting parameters using empirical quantiles from the expert datasets. 
* **Walk-Trot:** Characterized by support-rich diagonal locomotion with no flight.
* **Trot:** Characterized by dominant diagonal 2-contact support.
* **Bound:** High-speed pair-synchronized running where forelegs and hind legs move in phase.

### 3. Hierarchical Reward Machine
The final reward is derived from the quantitative robustness of the active specifications within the current temporal window. The active locomotion mode g(t) &isin; {W, T, B} is selected dynamically based on the commanded forward velocity v<sub>x</sub><sup>cmd</sup>. The scalar reward aggregates safety, tracking, and gait structure robustness alongside a torque-effort penalty.

<hr>

## Experimental Results

The locomotion controller is designed for **Google's Barkour vb quadruped robot**, modeled and trained using PPO within MuJoCo XLA (MJX). We utilize domain randomization over friction and actuator parameters to robustify the learned policies. 

### Benchmark Comparison

Benchmark comparison over 20 rollouts per commanded velocity. CoT: lower is better; Survival and Success: higher is better.

<table>
  <thead>
    <tr>
      <th rowspan="2">v<sub>x</sub> (m/s)</th>
      <th rowspan="2">Gait</th>
      <th colspan="3" align="center">Human-STL</th>
      <th colspan="3" align="center">GPT-STL</th>
      <th colspan="3" align="center">Expert Oracle</th>
      <th colspan="3" align="center">Heuristic</th>
      <th colspan="3" align="center">Text2Reward</th>
    </tr>
    <tr>
      <th align="center">CoT &darr;</th><th align="center">Surv. &uarr;</th><th align="center">Succ. &uarr;</th>
      <th align="center">CoT &darr;</th><th align="center">Surv. &uarr;</th><th align="center">Succ. &uarr;</th>
      <th align="center">CoT &darr;</th><th align="center">Surv. &uarr;</th><th align="center">Succ. &uarr;</th>
      <th align="center">CoT &darr;</th><th align="center">Surv. &uarr;</th><th align="center">Succ. &uarr;</th>
      <th align="center">CoT &darr;</th><th align="center">Surv. &uarr;</th><th align="center">Succ. &uarr;</th>
    </tr>
  </thead>
  <tbody>
    <tr><td align="center">0.3</td><td align="center">Walk</td><td align="center">2.1</td><td align="center">100%</td><td align="center">100%</td><td align="center">2.0</td><td align="center">100%</td><td align="center">100%</td><td align="center">0.9</td><td align="center">100%</td><td align="center">100%</td><td align="center">1.1</td><td align="center">100%</td><td align="center">100%</td><td align="center">1.1</td><td align="center">100%</td><td align="center">100%</td></tr>
    <tr><td align="center">0.5</td><td align="center">Walk</td><td align="center">1.5</td><td align="center">100%</td><td align="center">100%</td><td align="center">1.6</td><td align="center">100%</td><td align="center">100%</td><td align="center">0.9</td><td align="center">100%</td><td align="center">100%</td><td align="center">1.0</td><td align="center">100%</td><td align="center">100%</td><td align="center">0.8</td><td align="center">100%</td><td align="center">100%</td></tr>
    <tr><td align="center">0.7</td><td align="center">Walk</td><td align="center">1.3</td><td align="center">100%</td><td align="center">100%</td><td align="center">1.4</td><td align="center">100%</td><td align="center">100%</td><td align="center">1.0</td><td align="center">100%</td><td align="center">100%</td><td align="center">1.0</td><td align="center">100%</td><td align="center">100%</td><td align="center">0.9</td><td align="center">100%</td><td align="center">100%</td></tr>
    <tr><td align="center">1.0</td><td align="center">Trot</td><td align="center">1.2</td><td align="center">100%</td><td align="center">100%</td><td align="center">1.3</td><td align="center">100%</td><td align="center">100%</td><td align="center">1.1</td><td align="center">100%</td><td align="center">100%</td><td align="center">1.1</td><td align="center">100%</td><td align="center">100%</td><td align="center">1.1</td><td align="center">100%</td><td align="center">100%</td></tr>
    <tr><td align="center">1.3</td><td align="center">Trot</td><td align="center">1.1</td><td align="center">100%</td><td align="center">100%</td><td align="center">1.2</td><td align="center">100%</td><td align="center">100%</td><td align="center">1.3</td><td align="center">100%</td><td align="center">100%</td><td align="center">1.2</td><td align="center">100%</td><td align="center">100%</td><td align="center">1.1</td><td align="center">100%</td><td align="center">100%</td></tr>
    <tr><td align="center">1.6</td><td align="center">Trot</td><td align="center">1.0</td><td align="center">100%</td><td align="center">100%</td><td align="center">1.3</td><td align="center">100%</td><td align="center">100%</td><td align="center">1.3</td><td align="center">100%</td><td align="center">100%</td><td align="center">1.4</td><td align="center">100%</td><td align="center">100%</td><td align="center">1.0</td><td align="center">100%</td><td align="center">100%</td></tr>
    <tr><td align="center">1.9</td><td align="center">Bound</td><td align="center">1.1</td><td align="center">100%</td><td align="center">100%</td><td align="center">1.3</td><td align="center">100%</td><td align="center">0%</td><td align="center">1.3</td><td align="center">95%</td><td align="center">0%</td><td align="center">1.4</td><td align="center">100%</td><td align="center">100%</td><td align="center">-</td><td align="center">0%</td><td align="center">0%</td></tr>
    <tr><td align="center">2.0</td><td align="center">Bound</td><td align="center">1.1</td><td align="center">100%</td><td align="center">100%</td><td align="center">1.3</td><td align="center">100%</td><td align="center">0%</td><td align="center">1.4</td><td align="center">95%</td><td align="center">0%</td><td align="center">1.4</td><td align="center">0%</td><td align="center">100%</td><td align="center">-</td><td align="center">0%</td><td align="center">0%</td></tr>
    <tr><td align="center">2.1</td><td align="center">Bound</td><td align="center">1.2</td><td align="center">100%</td><td align="center">70%</td><td align="center">1.3</td><td align="center">100%</td><td align="center">0%</td><td align="center">1.4</td><td align="center">100%</td><td align="center">0%</td><td align="center">1.4</td><td align="center">100%</td><td align="center">0%</td><td align="center">-</td><td align="center">0%</td><td align="center">0%</td></tr>
  </tbody>
</table>

<br>

### Ablation Studies

**Transition Success Rates**

Transition success rates under different temporal windows H. Each transition changes the commanded forward velocity from v<sub>1</sub> to v<sub>2</sub>.

<table>
  <thead>
    <tr>
      <th align="center">Transition</th>
      <th align="center">v<sub>1</sub> (m/s)</th>
      <th align="center">v<sub>2</sub> (m/s)</th>
      <th align="center">H=10</th>
      <th align="center">H=20</th>
      <th align="center">H=30</th>
    </tr>
  </thead>
  <tbody>
    <tr><td align="center">Walk &rarr; Trot</td><td align="center">0.3</td><td align="center">0.8</td><td align="center">100%</td><td align="center">100%</td><td align="center">95%</td></tr>
    <tr><td align="center">Walk &rarr; Trot</td><td align="center">0.3</td><td align="center">1.2</td><td align="center">100%</td><td align="center">100%</td><td align="center">100%</td></tr>
    <tr><td align="center">Walk &rarr; Trot</td><td align="center">0.6</td><td align="center">0.8</td><td align="center">100%</td><td align="center">100%</td><td align="center">100%</td></tr>
    <tr><td align="center">Walk &rarr; Trot</td><td align="center">0.6</td><td align="center">1.2</td><td align="center">100%</td><td align="center">100%</td><td align="center">100%</td></tr>
    <tr><td align="center">Trot &rarr; Walk</td><td align="center">0.8</td><td align="center">0.3</td><td align="center">100%</td><td align="center">100%</td><td align="center">100%</td></tr>
    <tr><td align="center">Trot &rarr; Walk</td><td align="center">1.2</td><td align="center">0.3</td><td align="center">100%</td><td align="center">100%</td><td align="center">100%</td></tr>
    <tr><td align="center">Trot &rarr; Walk</td><td align="center">0.8</td><td align="center">0.6</td><td align="center">100%</td><td align="center">100%</td><td align="center">100%</td></tr>
    <tr><td align="center">Trot &rarr; Walk</td><td align="center">1.2</td><td align="center">0.6</td><td align="center">100%</td><td align="center">100%</td><td align="center">100%</td></tr>
    <tr><td align="center">Trot &rarr; Bound</td><td align="center">1.0</td><td align="center">1.75</td><td align="center">90%</td><td align="center">15%</td><td align="center">25%</td></tr>
    <tr><td align="center">Trot &rarr; Bound</td><td align="center">1.0</td><td align="center">1.9</td><td align="center">70%</td><td align="center">30%</td><td align="center">35%</td></tr>
    <tr><td align="center">Trot &rarr; Bound</td><td align="center">1.5</td><td align="center">1.75</td><td align="center">50%</td><td align="center">15%</td><td align="center">0%</td></tr>
    <tr><td align="center">Trot &rarr; Bound</td><td align="center">1.5</td><td align="center">1.9</td><td align="center">0%</td><td align="center">55%</td><td align="center">5%</td></tr>
    <tr><td align="center">Bound &rarr; Trot</td><td align="center">1.75</td><td align="center">1.0</td><td align="center">10%</td><td align="center">100%</td><td align="center">50%</td></tr>
    <tr><td align="center">Bound &rarr; Trot</td><td align="center">1.9</td><td align="center">1.0</td><td align="center">10%</td><td align="center">85%</td><td align="center">55%</td></tr>
    <tr><td align="center">Bound &rarr; Trot</td><td align="center">1.75</td><td align="center">1.5</td><td align="center">0%</td><td align="center">85%</td><td align="center">20%</td></tr>
    <tr><td align="center">Bound &rarr; Trot</td><td align="center">1.9</td><td align="center">1.5</td><td align="center">0%</td><td align="center">85%</td><td align="center">40%</td></tr>
  </tbody>
</table>

<br>

**Temporal Window Ablation**

Ablation over temporal window size H. Metrics are averaged across all evaluated commanded forward velocities. 

<table>
  <thead>
    <tr>
      <th align="center">Method</th>
      <th align="center">H</th>
      <th align="center">CoT</th>
      <th align="center">Survival &uarr;</th>
      <th align="center">Success &uarr;</th>
    </tr>
  </thead>
  <tbody>
    <tr><td align="center">GPT-STL</td><td align="center">1</td><td align="center">1.4</td><td align="center">100%</td><td align="center">55.6%</td></tr>
    <tr><td align="center">GPT-STL</td><td align="center">5</td><td align="center">1.6</td><td align="center">100%</td><td align="center">55.6%</td></tr>
    <tr><td align="center">GPT-STL</td><td align="center">10</td><td align="center">1.4</td><td align="center">100%</td><td align="center">55.6%</td></tr>
    <tr><td align="center">GPT-STL</td><td align="center">20</td><td align="center">1.4</td><td align="center">100%</td><td align="center">66.7%</td></tr>
    <tr><td align="center">GPT-STL</td><td align="center">30</td><td align="center">1.4</td><td align="center">99.4%</td><td align="center">66.7%</td></tr>
    <tr><td align="center">Human-STL</td><td align="center">1</td><td align="center">1.3</td><td align="center">100%</td><td align="center">100%</td></tr>
    <tr><td align="center">Human-STL</td><td align="center">5</td><td align="center">1.1</td><td align="center">99.4%</td><td align="center">99.4%</td></tr>
    <tr><td align="center">Human-STL</td><td align="center">10</td><td align="center">1.4</td><td align="center">100%</td><td align="center">100%</td></tr>
    <tr><td align="center">Human-STL</td><td align="center">20</td><td align="center">1.3</td><td align="center">99.4%</td><td align="center">99.4%</td></tr>
    <tr><td align="center">Human-STL</td><td align="center">30</td><td align="center">1.3</td><td align="center">100%</td><td align="center">95.6%</td></tr>
  </tbody>
</table>

<br>
<hr>

## Locomotion Regimes

<div align="center">

<p><b>Walk-Trot Gait (v<sub>x</sub> = 0.4 m/s)</b></p>
<video src="assets/0.4%20vel%20-%20walk.mp4" controls autoplay loop muted width="500"></video>

<br>

<p><b>Trot Gait (v<sub>x</sub> = 1.2 m/s)</b></p>
<video src="assets/1.2%20vel%20-%20trot.mp4" controls autoplay loop muted width="500"></video>

<br>

<p><b>Bound Gait (v<sub>x</sub> = 1.9 m/s)</b></p>
<video src="assets/1.9%20vel%20-%20bound.mp4" controls autoplay loop muted width="500"></video>

</div>

<hr>

## LLM-Generated Temporal Logic Specifications

<p>Below are evaluations of the 6 baselines generated using Large Language Models (LLMs) to automatically synthesize Temporal Logic Specifications. For each baseline, we showcase policies executed at low (0.4 m/s), medium (1.2 m/s), and the highest achieved velocity.</p>

<div align="center">

<h3>GPT-Gait-Agnostic</h3>
<table>
  <tr>
    <td align="center"><b>v<sub>x</sub> = 0.4 m/s</b></td>
    <td align="center"><b>v<sub>x</sub> = 1.2 m/s</b></td>
    <td align="center"><b>v<sub>x</sub> = 1.9 m/s</b></td>
  </tr>
  <tr>
    <td><video src="assets/gait_agnostic_velocity_0.4_GPT-Gait-Agnostic.mp4" controls autoplay loop muted width="300" height="225" style="object-fit: cover;"></video></td>
    <td><video src="assets/gait_agnostic_velocity_1.2_GPT-Gait-Agnostic.mp4" controls autoplay loop muted width="300" height="225" style="object-fit: cover;"></video></td>
    <td><video src="assets/gait_agnostic_velocity_1.9_GPT-Gait-Agnostic.mp4" controls autoplay loop muted width="300" height="225" style="object-fit: cover;"></video></td>
  </tr>
</table>

<h3>GPT-Multi-Gait</h3>
<table>
  <tr>
    <td align="center"><b>v<sub>x</sub> = 0.4 m/s</b></td>
    <td align="center"><b>v<sub>x</sub> = 1.2 m/s</b></td>
    <td align="center"><b>v<sub>x</sub> = 1.6 m/s</b></td>
  </tr>
  <tr>
    <td><video src="assets/multigait_velocity_0.4_GPT-Multi-Gait.mp4" controls autoplay loop muted width="300" height="225" style="object-fit: cover;"></video></td>
    <td><video src="assets/multigait_velocity_1.2_GPT-Multi-Gait.mp4" controls autoplay loop muted width="300" height="225" style="object-fit: cover;"></video></td>
    <td><video src="assets/multigait_velocity_1.6_GPT-Multi-Gait.mp4" controls autoplay loop muted width="300" height="225" style="object-fit: cover;"></video></td>
  </tr>
</table>

<h3>Qwen-Gait-Agnostic</h3>
<table>
  <tr>
    <td align="center"><b>v<sub>x</sub> = 0.4 m/s</b></td>
    <td align="center"><b>v<sub>x</sub> = 1.2 m/s</b></td>
    <td align="center"><b>v<sub>x</sub> = 1.3 m/s</b></td>
  </tr>
  <tr>
    <td><video src="assets/velocity_0.4_Qwen-Gait-Agnostic.mp4" controls autoplay loop muted width="300" height="225" style="object-fit: cover;"></video></td>
    <td><video src="assets/velocity_1.2_Qwen-Gait-Agnostic.mp4" controls autoplay loop muted width="300" height="225" style="object-fit: cover;"></video></td>
    <td><video src="assets/velocity_1.3_Qwen-Gait-Agnostic.mp4" controls autoplay loop muted width="300" height="225" style="object-fit: cover;"></video></td>
  </tr>
</table>

<h3>Qwen-Multi-Gait</h3>
<table>
  <tr>
    <td align="center"><b>v<sub>x</sub> = 0.4 m/s</b></td>
    <td align="center"><b>v<sub>x</sub> = 1.2 m/s</b></td>
    <td align="center"><b>v<sub>x</sub> = 1.9 m/s</b></td>
  </tr>
  <tr>
    <td><video src="assets/velocity_0.4_Qwen-Multi-Gait.mp4" controls autoplay loop muted width="300" height="225" style="object-fit: cover;"></video></td>
    <td><video src="assets/velocity_1.2_Qwen-Multi-Gait.mp4" controls autoplay loop muted width="300" height="225" style="object-fit: cover;"></video></td>
    <td><video src="assets/velocity_1.9_Qwen-Multi-Gait.mp4" controls autoplay loop muted width="300" height="225" style="object-fit: cover;"></video></td>
  </tr>
</table>

<h3>Text2Reward-Gait-Agnostic</h3>
<table>
  <tr>
    <td align="center"><b>v<sub>x</sub> = 0.4 m/s</b></td>
    <td align="center"><b>v<sub>x</sub> = 1.2 m/s</b></td>
    <td align="center"><b>v<sub>x</sub> = 1.9 m/s</b></td>
  </tr>
  <tr>
    <td><video src="assets/velocity_0.4_Text2Reward-Gait-Agnostic.mp4" controls autoplay loop muted width="300" height="225" style="object-fit: cover;"></video></td>
    <td><video src="assets/velocity_1.2_Text2Reward-Gait-Agnostic.mp4" controls autoplay loop muted width="300" height="225" style="object-fit: cover;"></video></td>
    <td><video src="assets/velocity_1.9_Text2Reward-Gait-Agnostic.mp4" controls autoplay loop muted width="300" height="225" style="object-fit: cover;"></video></td>
  </tr>
</table>

<h3>Text2Reward-Multi-Gait</h3>
<table>
  <tr>
    <td align="center"><b>v<sub>x</sub> = 0.4 m/s</b></td>
    <td align="center"><b>v<sub>x</sub> = 1.2 m/s</b></td>
    <td align="center"><b>v<sub>x</sub> = 1.6 m/s</b></td>
  </tr>
  <tr>
    <td><video src="assets/velocity_0.4_Text2Reward-Multi-Gait.mp4" controls autoplay loop muted width="300" height="225" style="object-fit: cover;"></video></td>
    <td><video src="assets/velocity_1.2_Text2Reward-Multi-Gait.mp4" controls autoplay loop muted width="300" height="225" style="object-fit: cover;"></video></td>
    <td><video src="assets/velocity_1.6_Text2Reward-Multi-Gait.mp4" controls autoplay loop muted width="300" height="225" style="object-fit: cover;"></video></td>
  </tr>
</table>

</div>

<hr>

## Citation

```bibtex
@article{atasever2026learning,
  title={Learning Gait-Aware Quadruped Locomotion with Temporal Logic Specifications},
  author={Atasever, Merve and Bakirci, Cagan and Corona, Alfredo Reina and Azbijari, Keyan and Deshmukh, Jyotirmoy V},
  journal={arXiv preprint arXiv:2607.00442},
  year={2026}
}

@article{atasever2026llm,
  title={From LLM-Generated Specifications to Learned Quadruped Locomotion},
  author={Atasever, Merve and Azbijari, Keyan and Bakirci, Cagan and Corona, Alfredo Reina and Izdas, Tolga and Yang, Richard and Biyik, Erdem and Deshmukh, Jyotirmoy V},
  journal={arXiv preprint arXiv:2609.07111},
  year={2026}
}
