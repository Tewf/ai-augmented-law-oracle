# 0001: Accept a model's encoding only at zero disagreement

**Decision:** an encoding a model writes is accepted only when a solver finds no case on which it and a reference
encoding decide differently, and law-oracle's own checks pass on it.

**Status:** accepted, 2026-11-12. **Shape:** [decision record](https://github.com/Tewf/reader-ergonomics-software-design/blob/8896e91b1a354a565bb0266a26b314329d82db98/shapes/decision-record.md). **Proposed in:** no proposal; argued in [law-oracle's review](https://github.com/Tewf/law-oracle/blob/c07cb0c92a16ed70f4961db9322e7a5afabb4848/evidence/statutes-as-code/against.md).

## Context

A model translating a statute writes code that compiles far more often than it decides right. Few-shot models gave
88.8% syntactically valid Catala (Lorenzo 2025, secondary), while the best model reached 44.4% on DeonticBench's hard
tax cases (Dou 2026a). Two encodings that look alike can still decide differently (Vernie 2026). A model can also report
an answer as the solver's when the solver never gave it (Wang 2026b).

## Decision

The model proposes; a program decides. An encoding is accepted when it compiles, passes law-oracle's checks, and a solver
enumerates no case on which it and the reference encoding disagree. A disagreement found is verbalised as a concrete
case and returned with the rejection.

## Options

| Option | Consequences | Outcome |
|---|---|---|
| Zero disagreement with a reference, by enumeration | the strictest gate that a program can run; needs a reference encoding for each provision | chosen |
| Passing the provision's test cases | cheap; tests cover the cases someone thought of, not the edge cases where encodings split | rejected |
| A model judging the encoding | no reference needed; the judge launders the same errors it should catch | rejected |

## Consequences

- Good: the acceptance rule is a program the model cannot argue with, as the ai-augmented family asks of a central check.
- Bad: a provision with no reference encoding cannot be accepted this way; it waits for one written by a person.
- Bad: the enumeration is bounded, so zero disagreement is "none inside the box drawn".
- To keep it true: the bound of each enumeration is written beside its result.

## See also

- [decisions/](README.md); [law-oracle's decisions](https://github.com/Tewf/law-oracle/tree/c07cb0c92a16ed70f4961db9322e7a5afabb4848/decisions).
