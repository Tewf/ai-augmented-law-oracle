# Baseline runs

B2 run as it is, before any code of our own, on a local model, so later work is compared with numbers measured here.
The baseline is named in law-oracle's [review](https://github.com/Tewf/law-oracle/blob/c07cb0c92a16ed70f4961db9322e7a5afabb4848/evidence/statutes-as-code/baseline.md).

## B2: DeonticBench's smoke split, 2026-11-12

| Item | Value |
|---|---|
| Benchmark | [guangyaodou/DeonticBench](https://github.com/guangyaodou/DeonticBench) at `3c168f9`, scripts unchanged |
| Split | smoke: the first 5 cases of each hard split; one generation per case |
| Model | Qwen2.5-7B-Instruct, quantised to 4 bits, served locally behind an OpenAI-compatible endpoint, context 16,384 tokens |
| Solver | SWI-Prolog, the system's, timeout 10 s |
| Wall time | 9 min 12 s for three domains and two modes, on an RTX 4060 Mobile |

Accuracy, abstention and wrong rate are the benchmark's own `scripts/bootstrap_outputs.py`, 1,000 replicates, seed 0:
bootstrap mean with its 95% interval. With 5 cases and one generation, each mean is close to a count out of 5.

| Domain | Mode | Accuracy | Abstained | Wrong |
|---|---|---|---|---|
| SARA numeric (tax owed) | few-shot Prolog | 0.00 [0.0, 0.0] | 1.00 | 0.00 |
| SARA numeric | direct answer | 0.00 [0.0, 0.0] | 0.00 | 1.00 |
| SARA binary (entailment) | few-shot Prolog | 0.40 [0.0, 0.8] | 0.40 | 0.20 |
| SARA binary | direct answer | 0.81 [0.4, 1.0] | 0.00 | 0.19 |
| Housing (yes/no) | few-shot Prolog | 0.00 [0.0, 0.0] | 0.61 | 0.40 |
| Housing | direct answer | 0.19 [0.0, 0.6] | 0.00 | 0.81 |

- **What it shows:** with a small model, the Prolog route fails mostly by abstaining (a program that errors or returns
  nothing), where the direct answer fails by being wrong. On tax amounts, five wrong numbers against five abstentions.
  An abstention is what [decision 0001](../decisions/0001-accept-an-encoding-only-at-zero-disagreement.md) and law-oracle's
  third value want instead of a guess.
- **What it does not show:** anything about the published scores. Five cases, one generation, a 7B model quantised to
  4 bits, against the paper's frontier models on the full hard splits (best 44.4% on SARA numeric, Dou 2026a). The
  intervals are as wide as the scale.

## To run it again

```sh
# Serve Qwen2.5-7B-Instruct (4-bit) with a 16,384-token context behind any OpenAI-compatible endpoint,
# under the model name qwen2.5-7b-16k, at $BASE_URL.
git clone https://github.com/guangyaodou/DeonticBench && cd DeonticBench && git checkout 3c168f9
pip install openai tenacity tqdm httpx pyyaml numpy pandas datasets
SPLIT=smoke DOMAINS="sara_numeric sara_binary housing" MODELS=qwen2.5-7b-16k MODES="few-shot direct" \
  NUM_GENERATIONS=1 BASE_URL=$BASE_URL OPENAI_API_KEY=local bash experiments/run_openai.sh
python scripts/bootstrap_outputs.py --rng-seed 0
```

The context is raised because the SARA statutes and two worked examples come to about 10,000 tokens, past the
server's default context. Without it the prompt would be cut silently. `vllm`, in the benchmark's requirements, is not needed for an
API run and is left out.
