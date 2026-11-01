# Agentic AI Assignment: Inference-time methods and a calculator agent on GSM8K

Model: `Qwen/Qwen2.5-0.5B-Instruct` (frozen weights, no fine-tuning, no paid APIs).
Data: GSM8K (`openai/gsm8k`, config `main`).

| Split | Examples |
|---|---|
| Dev | train 1-10 |
| Demonstrations | train 11-14 |
| Test | test 1-30 (same questions for every method) |

## What is in this repository

- `Agentic_AI_Assignment1.ipynb`: the full code with saved outputs (Q1 and Q2).
- `report.pdf`: the written report (approach, experiments, testing, improvements, results, Q3 critique).

## How to run

1. Open the notebook in Google Colab (<https://colab.research.google.com>), e.g. via *File -> Open notebook -> GitHub*.
2. Select a GPU: *Runtime -> Change runtime type -> T4 GPU* (free tier is enough).
3. Run all cells in order: *Runtime -> Run all*. If the first cell upgrades `transformers` and Colab asks for a restart, restart the runtime and run all again.
4. The model (about 1 GB) and the dataset are downloaded automatically on the first run.

The full notebook takes roughly 40-60 minutes on a T4 (the four Q1 methods on the 30 test questions alone take about 20 minutes, self-consistency and reflection being the slowest).

**Do not run cells one by one out of order.** The notebook is in chronological order of development, so some functions (`extract_number`, `generate`, `agent`) are defined more than once and the later definition replaces the earlier one. The final configuration is the last definition of each.

## Structure of the notebook

1. Setup: model, tokenizer, dataset splits.
2. Q1 prompts and methods: zero-shot, four-shot, self-consistency (4 samples, T=0.7, top-p=0.95, majority vote), reflection (initial answer + one revision call).
3. Q1 development experiments (dev set only): error analysis, stricter prompt (SYSTEM B, rejected), parser versions.
4. Q1 final test run and the per-question comparison (cases where extra inference helped or harmed).

## Final results (test, 30 questions)

| Method | Correct | Accuracy | Generated tokens | Model calls |
|---|---|---|---|---|
| Zero-shot | 11 | 36.7% | 9,669 | 30 |
| Four-shot | 11 | 36.7% | 4,906 | 30 |
| Self-consistency | 8 | 26.7% | 29,800 | 120 |
| Reflection | 11 | 36.7% | 12,304 | 60 |
| Calculator agent (Q2) | 8 | 26.7% | 756 | 68 generations (38 calculator calls) |

Input tokens: four-shot 26,655; agent 55,201.

One question is 3.3 percentage points on a 30-question test, so small differences are within noise.

## Reproducibility notes

- Greedy decoding for zero-shot, four-shot, reflection and the agent; sampling only in self-consistency.
- `torch.manual_seed(42)` is set before every question, so rerunning should reproduce the numbers above on the same hardware and library versions. Small differences are possible on a different GPU or `transformers` version.
- Prompts and the answer parser were chosen using the dev set only. The test set was run more than once while debugging the code, but no prompt or parser decision was based on test results.
- Test reference solutions are never shown to the model or the tools.

## Author

<Your name> - <course / instructor: Adnan Jafar>
