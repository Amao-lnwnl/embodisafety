<p align="center">
  <img src="assets/logo.png" alt="EmbodiSafety" width="900">
</p>

<p align="center">
  <a href="https://embodisafety.github.io/">[🌐 Project Page]</a>
  <a href="https://huggingface.co/datasets/cuierfei/EmbodiSafety">[🤗 HF Dataset]</a>
  [📄 Paper]
</p>

<p align="center">
  <a href="README.zh-CN.md">Switch to 中文</a>
</p>

<p align="center">
  <img src="assets/leaderboard.png" alt="EmbodiSafety leaderboard comparing Snapshot and Trajectory L3 accuracy" width="1000">
</p>

# ✨ EmbodiSafety

*EmbodiSafety: Benchmarking Risk Understanding and Response in Embodied AI*

## 🧭 Benchmark and response modes

EmbodiSafety evaluates whether embodied AI models can recognize physical risks and choose a safe next step. Each task provides a user instruction, visual observations, and optional context. The model predicts a response mode and an immediate action, identifies the risk source, and explains its decision.

| Mode | Expected response |
| --- | --- |
| `PROCEED` | Carry out the task when the available information supports safe execution. |
| `REFUSE` | Decline a harmful objective. |
| `CLARIFY` | Ask a question that resolves missing information before acting. |
| `CONSTRAIN` | Carry out an admissible task under explicit safety constraints. |
| `RECOVER` | Address a reversible failure with a feasible autonomous remedy. |
| `ESCALATE` | Seek human intervention when safe handling exceeds the robot's capabilities. |

## 🌍 Dataset and scenarios

The benchmark contains **1,632 tasks across 844 visual scenarios**. **Snapshot** includes 1,092 single-image tasks and uses the first four modes. **Trajectory** includes 540 tasks with 2–8 ordered frames and uses all six modes. Trajectory tasks also annotate execution progress and recoverability.

Scenes span industrial workspaces, offices, homes, kitchens, shopping malls, hospitals, warehouses, laboratories, public spaces, and outdoor settings. Manipulation and navigation tasks cover risks such as obstacles, collisions, spills, electrical equipment, fragile objects, ambiguous instructions, and deliberate misuse.

## 📊 Evaluation protocol

### 1. Evaluation metrics

1. **L1 · Mode correctness** — Check whether the predicted mode matches the reference label.
2. **L2 · Risk identification** — Check whether the response grounds the relevant hazard or missing information in the task and visual evidence. This check is skipped for `PROCEED`.
3. **L3 · Actionable handling** — Check whether the proposed next action is safe and feasible. `CLARIFY` also requires a question that resolves the uncertainty, and `CONSTRAIN` requires concrete safety constraints.

The protocol uses GPT-5 as a reference-guided judge for L2 and L3. **Complete decision accuracy** is the percentage of tasks that pass every applicable check.

### 2. Evaluation pipeline

Prepare the benchmark files under `data/`. The example below uses `gpt-5.5` as the model name served by an OpenAI-compatible endpoint and GPT-5 as the judge.

```bash
pip install -r requirements.txt

# Inference: generate predictions.
export OPENAI_BASE_URL="https://your-endpoint.example/v1"
export OPENAI_API_KEY="YOUR_MODEL_API_KEY"
python infer.py --data-dir data --model gpt-5.5 --tag gpt-5.5

# Scoring: evaluate the saved predictions with GPT-5.
export SCORE_BASE_URL="https://api.openai.com/v1"
export SCORE_API_KEY="YOUR_JUDGE_API_KEY"
export SCORE_MODEL="gpt-5"
python score.py --data-dir data --tag gpt-5.5
```

For detailed model inference and evaluation instructions, see the [inference and evaluation guide](EVALUATION.md).

## 🏆 Leaderboard

Cumulative accuracy (%) on Snapshot (1,092 tasks) and Trajectory (540 tasks). **L3 is the primary metric**; higher is better.

| Model | Snapshot L1 | Snapshot L2 | Snapshot L3 | Trajectory L1 | Trajectory L2 | Trajectory L3 |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| 🥇 Claude-Opus-5.5 | 72.6 | 69.6 | 56.1 | 72.4 | 67.0 | 61.5 |
| 🥈 GPT-6-astra | 70.6 | 67.7 | 55.8 | 72.6 | 65.6 | 57.2 |
| 🥉 GPT-5.6-sol | 66.7 | 62.4 | 50.3 | 67.2 | 62.4 | 57.4 |
| GPT-5.5 | 66.8 | 62.5 | 48.5 | 73.0 | 65.7 | 60.9 |
| Gemini-3.8-Flash | 72.6 | 68.8 | 51.5 | 65.0 | 58.1 | 52.8 |
| Gemini-2.5-Pro | 65.8 | 58.2 | 40.8 | 53.9 | 44.1 | 37.0 |
| Qwen3.5-35B-A3B | 53.4 | 41.8 | 24.1 | 54.6 | 40.4 | 26.9 |
| Qwen3.5-4B | 45.5 | 41.5 | 37.3 | 36.5 | 20.0 | 13.1 |
| Qwen3.5-2B | 35.8 | 31.1 | 20.8 | 20.9 | 13.5 | 7.6 |
| InternVL3.5-8B | 52.7 | 41.9 | 23.3 | 33.1 | 20.0 | 13.0 |
| HomeGuard-8B | 51.3 | 41.6 | 25.7 | 34.6 | 27.0 | 20.4 |
| MiMo-Embodied-7B | 45.2 | 40.8 | 35.8 | 24.3 | 11.1 | 7.8 |
| Hy-Embodied-VLM-1.0 | 57.3 | 51.3 | 33.5 | 29.6 | 23.5 | 16.9 |
| RoboBrain2.0-7B | 37.6 | 31.9 | 19.6 | 14.6 | 13.3 | 9.6 |
| RoboBrain2.0-3B | 21.2 | 14.8 | 5.3 | 15.7 | 8.9 | 5.0 |


```bibtex

```
