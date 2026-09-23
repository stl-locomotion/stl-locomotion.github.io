<div align="center">

<h1>Learning Gait-Aware Quadruped Locomotion with Temporal Logic Specifications</h1>

<p>
  <a href="https://github.com/M-Atasever/STL-based-Quadruped-Locomotion">[Github repo]</a> &nbsp;&nbsp;|&nbsp;&nbsp;
  <a href="https://arxiv.org/abs/2607.00442">[Manually designed STL specs]</a> &nbsp;&nbsp;|&nbsp;&nbsp;
  <a href="https://github.com/M-Atasever/llm-stl-locomotion">[LLM-generated STL specs Github repo]</a> &nbsp;&nbsp;|&nbsp;&nbsp;
  <a href="https://arxiv.org/abs/2609.07111">[LLM-generated STL specs arXiv]</a>
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

## Experimental Results

### Benchmark Comparison

Benchmark comparison over 20 rollouts per commanded velocity. CoT: lower is better; Survival and Success: higher is better.

<table>
  <thead>
    <tr>
      <th rowspan="2">v<sub>x</sub></th>
      <th rowspan="2">Gait</th>
      <th colspan="3" align="center">Human-STL</th>
      <th colspan="3" align="center">GPT-STL</th>
      <th colspan="3" align="center">Expert Oracle</th>
      <th colspan="3" align="center">Heuristic</th>
      <th colspan="3" align="center">Text2Reward</th>
    </tr>
    <tr>
      <th align="center">CoT</th><th align="center">Surv. &uarr;</th><th align="center">Succ. &uarr;</th>
      <th align="center">CoT</th><th align="center">Surv. &uarr;</th><th align="center">Succ. &uarr;</th>
      <th align="center">CoT</th><th align="center">Surv. &uarr;</th><th align="center">Succ. &uarr;</th>
      <th align="center">CoT</th><th align="center">Surv. &uarr;</th><th align="center">Succ. &uarr;</th>
      <th align="center">CoT</th><th align="center">Surv. &uarr;</th><th align="center">Succ. &uarr;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="center">0.3</td>
      <td align="center">Walk</td>
      <td align="center">2.1</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">2.0</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">0.9</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">1.1</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">1.1</td><td align="center">100%</td><td align="center">100%</td>
    </tr>
    <tr>
      <td align="center">0.5</td>
      <td align="center">Walk</td>
      <td align="center">1.5</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">1.6</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">0.9</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">1.0</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">0.8</td><td align="center">100%</td><td align="center">100%</td>
    </tr>
    <tr>
      <td align="center">0.7</td>
      <td align="center">Walk</td>
      <td align="center">1.3</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">1.4</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">1.0</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">1.0</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">0.9</td><td align="center">100%</td><td align="center">100%</td>
    </tr>
    <tr>
      <td align="center">1.0</td>
      <td align="center">Trot</td>
      <td align="center">1.2</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">1.3</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">1.1</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">1.1</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">1.1</td><td align="center">100%</td><td align="center">100%</td>
    </tr>
    <tr>
      <td align="center">1.3</td>
      <td align="center">Trot</td>
      <td align="center">1.1</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">1.2</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">1.3</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">1.2</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">1.1</td><td align="center">100%</td><td align="center">100%</td>
    </tr>
    <tr>
      <td align="center">1.6</td>
      <td align="center">Trot</td>
      <td align="center">1.0</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">1.3</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">1.3</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">1.4</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">1.0</td><td align="center">100%</td><td align="center">100%</td>
    </tr>
    <tr>
      <td align="center">1.9</td>
      <td align="center">Bound</td>
      <td align="center">1.1</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">1.3</td><td align="center">100%</td><td align="center">0%</td>
      <td align="center">1.3</td><td align="center">95%</td><td align="center">0%</td>
      <td align="center">1.4</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">-</td><td align="center">0%</td><td align="center">0%</td>
    </tr>
    <tr>
      <td align="center">2.0</td>
      <td align="center">Bound</td>
      <td align="center">1.1</td><td align="center">100%</td><td align="center">100%</td>
      <td align="center">1.3</td><td align="center">100%</td><td align="center">0%</td>
      <td align="center">1.4</td><td align="center">95%</td><td align="center">0%</td>
      <td align="center">1.4</td><td align="center">0%</td><td align="center">100%</td>
      <td align="center">-</td><td align="center">0%</td><td align="center">0%</td>
    </tr>
    <tr>
      <td align="center">2.1</td>
      <td align="center">Bound</td>
      <td align="center">1.2</td><td align="center">100%</td><td align="center">70%</td>
      <td align="center">1.3</td><td align="center">100%</td><td align="center">0%</td>
      <td align="center">1.4</td><td align="center">100%</td><td align="center">0%</td>
      <td align="center">1.4</td><td align="center">100%</td><td align="center">0%</td>
      <td align="center">-</td><td align="center">0%</td><td align="center">0%</td>
    </tr>
  </tbody>
</table>

<br>

### Ablation Studies

**Table IV: Transition Success Rates**

Transition success rates under different temporal windows H. Each transition changes the commanded forward velocity from v1 to v2.

| Transition | v1 (m/s) | v2 (m/s) | H=10 | H=20 | H=30 |
| :--- | :---: | :---: | :---: | :---: | :---: |
| Walk &rarr; Trot | 0.3 | 0.8 | 100% | 100% | 95% |
| Walk &rarr; Trot | 0.3 | 1.2 | 100% | 100% | 100% |
| Walk &rarr; Trot | 0.6 | 0.8 | 100% | 100% | 100% |
| Walk &rarr; Trot | 0.6 | 1.2 | 100% | 100% | 100% |
| Trot &rarr; Walk | 0.8 | 0.3 | 100% | 100% | 100% |
| Trot &rarr; Walk | 1.2 | 0.3 | 100% | 100% | 100% |
| Trot &rarr; Walk | 0.8 | 0.6 | 100% | 100% | 100% |
| Trot &rarr; Walk | 1.2 | 0.6 | 100% | 100% | 100% |
| Trot &rarr; Bound | 1.0 | 1.75 | 90% | 15% | 25% |
| Trot &rarr; Bound | 1.0 | 1.9 | 70% | 30% | 35% |
| Trot &rarr; Bound | 1.5 | 1.75 | 50% | 15% | 0% |
| Trot &rarr; Bound | 1.5 | 1.9 | 0% | 55% | 5% |
| Bound &rarr; Trot | 1.75 | 1.0 | 10% | 100% | 50% |
| Bound &rarr; Trot | 1.9 | 1.0 | 10% | 85% | 55% |
| Bound &rarr; Trot | 1.75 | 1.5 | 0% | 85% | 20% |
| Bound &rarr; Trot | 1.9 | 1.5 | 0% | 85% | 40% |

<br>

**Table V: Temporal Window Ablation**

Ablation over temporal window size H. Metrics are averaged across all evaluated commanded forward velocities. 

| Method | H | CoT | Survival &uarr; | Success &uarr; |
| :--- | :---: | :---: | :---: | :---: |
| GPT-STL | 1 | 1.4 | 100% | 55.6% |
| GPT-STL | 5 | 1.6 | 100% | 55.6% |
| GPT-STL | 10 | 1.4 | 100% | 55.6% |
| GPT-STL | 20 | 1.4 | 100% | 66.7% |
| GPT-STL | 30 | 1.4 | 99.4% | 66.7% |
| Human-STL | 1 | 1.3 | 100% | 100% |
| Human-STL | 5 | 1.1 | 99.4% | 99.4% |
| Human-STL | 10 | 1.4 | 100% | 100% |
| Human-STL | 20 | 1.3 | 99.4% | 99.4% |
| Human-STL | 30 | 1.3 | 100% | 95.6% |

<br>
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
