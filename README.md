# AI-augmented law oracle

[![Checks against reader-ergonomics](https://github.com/Tewf/ai-augmented-law-oracle/actions/workflows/checks.yml/badge.svg)](https://github.com/Tewf/ai-augmented-law-oracle/actions/workflows/checks.yml)

A model translates a statute into code and a verdict back into words; [law-oracle](https://github.com/Tewf/law-oracle)
decides whether the translation is accepted. The model proposes, a program disposes. The field calls the first direction
**autoformalization** of law, and the second **explanation** or **verbalization**.

> **Status (2026-11-12):** one decision taken; nothing built of our own. B2 ran on its smoke split with a local 7B model:
> the Prolog route abstains where the direct answer is wrong ([runs](evidence/baseline-runs.md)).
>
> **How it is made:** with Claude, an AI assistant by Anthropic, under the maintainer's direction. Nothing here is legal
> advice. Corrections are welcome as issues.

## The loop

![A model writes an encoding from the statute; law-oracle's checks and a solver compare it with the reference; a disagreement goes back to the model as a concrete case; an accepted encoding gives verdicts, which the model words from the trace.](loop.svg)

<details>
<summary>Graphviz source</summary>

```dot
// The loop. Colours: model green, program blue, person orange; this repository's part has the thick border.
digraph loop {
  graph [rankdir=LR, nodesep=0.4, ranksep=0.5, bgcolor="white", pad=0.2, fontname="Inter"];
  node  [shape=box, style="rounded,filled", fontname="Inter", fontsize=13, margin="0.2,0.1", penwidth=1.5, fontcolor="#1F2328"];
  edge  [fontname="Inter", fontsize=11, color="#3D434B", fontcolor="#55575F", penwidth=1.3, arrowsize=0.8];
  S [label=<<b>statute</b><br/><font point-size="11" color="#55575F">read at its source</font>>, fillcolor="#FFFFFF", color="#3D434B"];
  M [label=<<b>model</b><br/><font point-size="11" color="#55575F">writes an encoding</font>>, fillcolor="#E6F2EA", color="#2E7D4F", penwidth=2.6];
  O [label=<<b>law-oracle's checks</b><br/><font point-size="11" color="#55575F">compile, prove, enumerate</font>>, fillcolor="#E8EEF8", color="#2F5DA8"];
  V [label=<<b>verdict</b><br/><font point-size="11" color="#55575F">yes, no, or not decided</font>>, fillcolor="#E8EEF8", color="#2F5DA8"];
  W [label=<<b>words</b><br/><font point-size="11" color="#55575F">from the trace, by the model</font>>, fillcolor="#E6F2EA", color="#2E7D4F", penwidth=2.6];
  P [label=<<b>person</b><br/><font point-size="11" color="#55575F">reads, or answers the third value</font>>, fillcolor="#FBEDE4", color="#B4541A"];
  S -> M;
  M -> O [label=" proposes"];
  O -> M [label=" a disagreeing case"];
  O -> V [label=" accepted"];
  V -> W;
  W -> P;
}
```

</details>

- Acceptance is [decision 0001](decisions/0001-accept-an-encoding-only-at-zero-disagreement.md): zero disagreement with
  a reference encoding, on every case a solver enumerates.
- The verdict's three values and the formal layers are law-oracle's: its
  [decisions](https://github.com/Tewf/law-oracle/tree/c07cb0c92a16ed70f4961db9322e7a5afabb4848/decisions).

## Baseline

**B2:** DeonticBench's symbolic setting ([guangyaodou/DeonticBench](https://github.com/guangyaodou/DeonticBench)): a model
writes Prolog, SWI-Prolog runs it, scored against audited reference programs. Best published: 44.4% on the hard SARA
numeric split (Dou 2026a). The review behind it is law-oracle's
[evidence/statutes-as-code](https://github.com/Tewf/law-oracle/tree/c07cb0c92a16ed70f4961db9322e7a5afabb4848/evidence/statutes-as-code),
and its [bibliography](https://github.com/Tewf/law-oracle/blob/c07cb0c92a16ed70f4961db9322e7a5afabb4848/evidence/bibliography.md) holds every key cited here.

## Scope

| | What | Why |
|---|---|---|
| in | a model writing an encoding from a statute, and the words of a verdict from its trace | the two directions of the idea |
| in | runs against B2, then against law-oracle's French encodings | B2 is the measured baseline; the French encodings are the target |
| out | the encodings a person writes, the checks, the Lean claims | [law-oracle](https://github.com/Tewf/law-oracle) |
| out | legal advice | a verdict states what a text computes |

## Open questions

- Does a verdict's explanation that survives a round trip, re-formalised by a second model and compared by decisions,
  mislead less than one that does not? Not measured.
- Does solver plus model answer the third value, not decided by the text, better than the model alone? Not measured.

## Licence

Code under the [Apache License 2.0](LICENSE); prose under [CC BY 4.0](LICENSES/CC-BY-4.0.txt).
