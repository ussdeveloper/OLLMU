# OLLMU

**Optical Large Language Model Unit** — a bilingual concept paper, not a working optical LLM.

[English concept](CONCEPT.en.md) · [Polska koncepcja](CONCEPT.pl.md) · [References](REFERENCES.md) · [Attribution](ATTRIBUTION.md)

License: [OLLMU Idea Attribution License 1.0](LICENSE) — realisation is unrestricted; originator credit must be kept.

Pomysł / idea originator: **Anisul Wałsymzerp**

---

## Status

This repository holds a **concept paper**. Nothing here is a fabricated chip, a trained model, a measured energy number for an LLM, or a claim that the 2026 GaN quasi-BIC laser already powers photonic AI.

Every technical statement in the papers is labelled:

| Label | Meaning |
| --- | --- |
| **Fact** | Reported in a cited publication |
| **Literature** | Established method, not original to OLLMU |
| **Hypothesis** | The architecture proposed here |
| **Estimate** | Order-of-magnitude reasoning, not a measurement |

## What OLLMU proposes

A frozen-weight inference path in which:

1. tokens are encoded onto a coherent emitter array,
2. a three-dimensional diffractive core implements linear MAC as light propagates (**optical ROM**),
3. selected ports feed **slow-light side loops** (delay memory + Kerr-type nonlinearity),
4. the model is trained, before fabrication, to open or close those ports by interference,
5. an **autoregressive recode loop** returns detected fields as the next token (this loop is not free).

The Polish room-temperature blue quasi-BIC laser (Fąs et al., *Nano Letters*, 2026) is treated as a **candidate source**, after electrical pumping and addressable arrays exist. It is not treated as the compute core.

A full transformer in one crystal is not a near-term claim. The honest split in the papers is: static FFN / MoE experts as optical ROM; input-dependent attention as a hybrid electronic or reconfigurable photonic block.

## Co to jest (PL)

**OLLMU** to koncepcja pasywnego rdzenia dyfrakcyjnego (optyczny ROM wag) z bocznicami wolnego światła i routingiem interferencyjnym. Laser BIC GaN jest kandydatem na źródło, nie dowodem, że optyczny LLM już działa.

Licencja **nie dotyczy realizacji** układu. Wymaga zachowania informacji, że pomysł wskazał **Anisul Wałsymzerp**.

Pełny tekst: [CONCEPT.pl.md](CONCEPT.pl.md).

## What this is not

- Not a replacement for EUV lithography.
- Not an electrically pumped BIC laser array.
- Not a measured competitor to GPUs.
- Not a patent filing.
- Not a restriction on building hardware.

## Cite

See [CITATION.cff](CITATION.cff) and [ATTRIBUTION.md](ATTRIBUTION.md). Intended remote: [github.com/ussdeveloper/OLLMU](https://github.com/ussdeveloper/OLLMU).

## Scope of this repo

Documentation only. No simulator, no hardware description language, no push to GitHub from this draft.
