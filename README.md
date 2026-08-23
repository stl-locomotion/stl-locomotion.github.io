<div align="center">

<h1>Quadruped Locomotion with Temporal Logic Specifications</h1>

<p>
  <a href="https://github.com/M-Atasever/STL-based-Quadruped-Locomotion">[Github repo]</a> &nbsp;&nbsp;|&nbsp;&nbsp;
  <a href="https://arxiv.org/abs/2607.00442">[Manually designed STL specs]</a> &nbsp;&nbsp;|&nbsp;&nbsp;
  <a href="#">[LLM-generated STL specs (will appear soon)]</a>
</p>

<img src="assets/quadruped%20model.png" width="500" alt="Barkour Quadruped Model">

</div>

<hr>

<h2>Learning Gait-Aware Quadruped Locomotion with Temporal Logic Specifications</h2>

## Abstract

Reinforcement learning (RL) for quadruped locomotion commonly relies on fixed, hand-crafted, and Markovian reward functions, which limit the interpretability of learned policies and provide no explicit control over gait behaviors. We introduce a framework in which distinct gaits are specified using parameterized constraints expressed in Signal Temporal Logic (STL), including safety bounds, gait synchronization constraints, command tracking objectives, and actuation limits. From these specifications, we develop a reward-shaping mechanism that provides learning agents with a dense and continuous reward landscape that encodes the desired behavior. We define parametric STL templates for three speed regimes (walking-trot, trot, and bound) calibrate their parameters from reference rollouts, and compute rewards using smooth approximations of STL robustness over these rollouts. The resulting rewards provide shaped gradients that are compatible with Proximal Policy Optimization (PPO). We instantiate the approach on Google’s Barkour quadruped robot in MuJoCo XLA (MJX), leveraging simulator parallelization to accelerate training and domain randomization to improve policy robustness. Experimental results show that, compared to conventional hand-crafted rewards, STL-shaped rewards achieve tighter velocity tracking and more stable training performance.

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

### Velocity Tracking & Stability

Benchmark comparison across commanded forward velocities. Each entry reports mean &plusmn; standard deviation over 20 rollouts. Lower CoT is better; higher survival and success are better. Success means the average post-warmup forward speed stays within &plusmn;15% of the commanded speed.

<table>
  <thead>
    <tr>
      <th rowspan="2">v<sub>x</sub></th>
      <th colspan="3" align="center">STL-based reward</th>
      <th colspan="3" align="center">Heuristic-default</th>
      <th colspan="3" align="center">Heuristic-best</th>
    </tr>
    <tr>
      <th align="center">CoT &darr;</th>
      <th align="center">Survival &uarr;</th>
      <th align="center">Success &uarr;</th>
      <th align="center">CoT &darr;</th>
      <th align="center">Survival &uarr;</th>
      <th align="center">Success &uarr;</th>
      <th align="center">CoT &darr;</th>
      <th align="center">Survival &uarr;</th>
      <th align="center">Success &uarr;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="center">0.3</td>
      <td align="center">2.1 &plusmn; 0.1</td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center">2.1 &plusmn; 0.1</td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center">0.0 &plusmn; 0.0</td>
      <td align="center"><b>1.2 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
    </tr>
    <tr>
      <td align="center">0.5</td>
      <td align="center">1.5 &plusmn; 0.0</td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center">1.3 &plusmn; 0.0</td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center">0.0 &plusmn; 0.0</td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
    </tr>
    <tr>
      <td align="center">0.7</td>
      <td align="center">1.2 &plusmn; 0.0</td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center">1.1 &plusmn; 0.0</td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center">0.1 &plusmn; 0.2</td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
    </tr>
    <tr>
      <td align="center">1.0</td>
      <td align="center">1.2 &plusmn; 0.0</td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center">1.5 &plusmn; 0.0</td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center">0.8 &plusmn; 0.4</td>
      <td align="center"><b>1.2 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
    </tr>
    <tr>
      <td align="center">1.3</td>
      <td align="center"><b>1.1 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center">1.3 &plusmn; 0.1</td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center">0.3 &plusmn; 0.5</td>
      <td align="center">1.3 &plusmn; 0.0</td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
    </tr>
    <tr>
      <td align="center">1.6</td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center">1.2 &plusmn; 0.0</td>
      <td align="center">1.0 &plusmn; 0.2</td>
      <td align="center">0.3 &plusmn; 0.4</td>
      <td align="center">1.4 &plusmn; 0.0</td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
    </tr>
    <tr>
      <td align="center">1.9</td>
      <td align="center"><b>1.1 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center">1.2 &plusmn; 0.1</td>
      <td align="center">0.5 &plusmn; 0.5</td>
      <td align="center">0.0 &plusmn; 0.0</td>
      <td align="center">1.4 &plusmn; 0.0</td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
    </tr>
    <tr>
      <td align="center">2.0</td>
      <td align="center"><b>1.1 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center">1.3 &plusmn; 0.1</td>
      <td align="center">0.5 &plusmn; 0.5</td>
      <td align="center">0.0 &plusmn; 0.0</td>
      <td align="center">1.4 &plusmn; 0.0</td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center">0.1 &plusmn; 0.2</td>
    </tr>
    <tr>
      <td align="center">2.1</td>
      <td align="center"><b>1.1 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center">1.3 &plusmn; 0.1</td>
      <td align="center">0.3 &plusmn; 0.4</td>
      <td align="center">0.0 &plusmn; 0.0</td>
      <td align="center">1.4 &plusmn; 0.0</td>
      <td align="center"><b>1.0 &plusmn; 0.0</b></td>
      <td align="center">0.0 &plusmn; 0.0</td>
    </tr>
  </tbody>
</table>

<br>

### Evaluation under mixed velocity commands

<p> We compare the learned unified model with the heuristic-best baseline across commanded forward velocities while additionally sampling lateral-velocity and yaw-rate commands from (v<sub>y</sub><sup>*</sup> &isin; [-0.4, 0.4] m s<sup>-1</sup>) and (&omega;<sub>z</sub><sup>*</sup> &isin; [-0.4, 0.4] rad s<sup>-1</sup>), respectively. This setting evaluates robustness beyond straight-line forward locomotion. Lower CoT is better; higher survival and success rates are better.</p>

<table>
  <thead>
    <tr>
      <th rowspan="2">v<sub>x</sub></th>
      <th colspan="3" align="center">STL-based unified model</th>
      <th colspan="3" align="center">Heuristic-best</th>
    </tr>
    <tr>
      <th align="center">CoT &darr;</th>
      <th align="center">Survival &uarr;</th>
      <th align="center">Success &uarr;</th>
      <th align="center">CoT &darr;</th>
      <th align="center">Survival &uarr;</th>
      <th align="center">Success &uarr;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="center">0.30</td>
      <td align="center"><b>1.87 &plusmn; 0.06</b></td>
      <td align="center">1.00</td>
      <td align="center"><b>1.00</b></td>
      <td align="center">2.55 &plusmn; 0.21</td>
      <td align="center">1.00</td>
      <td align="center">0.00</td>
    </tr>
    <tr>
      <td align="center">0.50</td>
      <td align="center"><b>1.46 &plusmn; 0.04</b></td>
      <td align="center">1.00</td>
      <td align="center"><b>1.00</b></td>
      <td align="center">2.11 &plusmn; 0.11</td>
      <td align="center">1.00</td>
      <td align="center">0.35</td>
    </tr>
    <tr>
      <td align="center">0.70</td>
      <td align="center"><b>1.24 &plusmn; 0.02</b></td>
      <td align="center">1.00</td>
      <td align="center"><b>1.00</b></td>
      <td align="center">1.83 &plusmn; 0.07</td>
      <td align="center">1.00</td>
      <td align="center"><b>1.00</b></td>
    </tr>
    <tr>
      <td align="center">1.00</td>
      <td align="center"><b>1.21 &plusmn; 0.02</b></td>
      <td align="center">1.00</td>
      <td align="center"><b>1.00</b></td>
      <td align="center">1.74 &plusmn; 0.06</td>
      <td align="center">1.00</td>
      <td align="center"><b>1.00</b></td>
    </tr>
    <tr>
      <td align="center">1.30</td>
      <td align="center"><b>1.10 &plusmn; 0.01</b></td>
      <td align="center">1.00</td>
      <td align="center"><b>1.00</b></td>
      <td align="center">1.76 &plusmn; 0.05</td>
      <td align="center">1.00</td>
      <td align="center"><b>1.00</b></td>
    </tr>
    <tr>
      <td align="center">1.60</td>
      <td align="center"><b>1.07 &plusmn; 0.01</b></td>
      <td align="center">1.00</td>
      <td align="center"><b>1.00</b></td>
      <td align="center">1.74 &plusmn; 0.05</td>
      <td align="center">1.00</td>
      <td align="center"><b>1.00</b></td>
    </tr>
    <tr>
      <td align="center">1.90</td>
      <td align="center"><b>1.11 &plusmn; 0.01</b></td>
      <td align="center">1.00</td>
      <td align="center"><b>1.00</b></td>
      <td align="center">1.72 &plusmn; 0.05</td>
      <td align="center">1.00</td>
      <td align="center">0.00</td>
    </tr>
    <tr>
      <td align="center">2.00</td>
      <td align="center"><b>1.12 &plusmn; 0.01</b></td>
      <td align="center">1.00</td>
      <td align="center"><b>0.80</b></td>
      <td align="center">1.69 &plusmn; 0.06</td>
      <td align="center">1.00</td>
      <td align="center">0.00</td>
    </tr>
    <tr>
      <td align="center">2.10</td>
      <td align="center"><b>1.13 &plusmn; 0.01</b></td>
      <td align="center"><b>1.00</b></td>
      <td align="center">0.00</td>
      <td align="center">1.69 &plusmn; 0.15</td>
      <td align="center">0.90</td>
      <td align="center">0.00</td>
    </tr>
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
    <td><video src="assets/gait_agnostic_velocity_0.4_GPT-Gait-Agnostic.mp4" controls autoplay loop muted width="300"></video></td>
    <td><video src="assets/gait_agnostic_velocity_1.2_GPT-Gait-Agnostic.mp4" controls autoplay loop muted width="300"></video></td>
    <td><video src="assets/gait_agnostic_velocity_1.9_GPT-Gait-Agnostic.mp4" controls autoplay loop muted width="300"></video></td>
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
    <td><video src="assets/multigait_velocity_0.4_GPT-Multi-Gait.mp4" controls autoplay loop muted width="300"></video></td>
    <td><video src="assets/multigait_velocity_1.2_GPT-Multi-Gait.mp4" controls autoplay loop muted width="300"></video></td>
    <td><video src="assets/multigait_velocity_1.6_GPT-Multi-Gait.mp4" controls autoplay loop muted width="300"></video></td>
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
    <td><video src="assets/velocity_0.4_Qwen-Gait-Agnostic.mp4" controls autoplay loop muted width="300"></video></td>
    <td><video src="assets/velocity_1.2_Qwen-Gait-Agnostic.mp4" controls autoplay loop muted width="300"></video></td>
    <td><video src="assets/velocity_1.3_Qwen-Gait-Agnostic.mp4" controls autoplay loop muted width="300"></video></td>
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
    <td><video src="assets/velocity_0.4_Qwen-Multi-Gait.mp4" controls autoplay loop muted width="300"></video></td>
    <td><video src="assets/velocity_1.2_Qwen-Multi-Gait.mp4" controls autoplay loop muted width="300"></video></td>
    <td><video src="assets/velocity_1.9_Qwen-Multi-Gait.mp4" controls autoplay loop muted width="300"></video></td>
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
    <td><video src="assets/velocity_0.4_Text2Reward-Gait-Agnostic.mp4" controls autoplay loop muted width="300"></video></td>
    <td><video src="assets/velocity_1.2_Text2Reward-Gait-Agnostic.mp4" controls autoplay loop muted width="300"></video></td>
    <td><video src="assets/velocity_1.9_Text2Reward-Gait-Agnostic.mp4" controls autoplay loop muted width="300"></video></td>
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
    <td><video src="assets/velocity_0.4_Text2Reward-Multi-Gait.mp4" controls autoplay loop muted width="300"></video></td>
    <td><video src="assets/velocity_1.2_Text2Reward-Multi-Gait.mp4" controls autoplay loop muted width="300"></video></td>
    <td><video src="assets/velocity_1.6_Text2Reward-Multi-Gait.mp4" controls autoplay loop muted width="300"></video></td>
  </tr>
</table>

</div>

<hr>

## Citation


@article{atasever2026learning,<br>
  title={Learning Gait-Aware Quadruped Locomotion with Temporal Logic Specifications},<br>
  author={Atasever, Merve and Bakirci, Cagan and Corona, Alfredo Reina and Azbijari, Keyan and Deshmukh, Jyotirmoy V},<br>
  journal={arXiv preprint arXiv:2607.00442},<br>
  year={2026}
}
