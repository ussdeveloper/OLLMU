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

1. **processing is optical** — a diffractive ROM performs linear MAC in transit,
2. a **light cache** (slower medium / slow light) holds only a short, useful echo,
3. **SiliconCache** and **context memory** sit off-crystal (nonlinearity, thinking, long state),
4. the model is trained — **before writing into the crystal** — when and how to use those external memories,
5. a large cluster of bricks uses **C-band (~1550 nm)** silica, **SOA/EDFA** between experts, and **3R regeneration** on SiliconCache (fibre dB/km is not ROM loss),
6. an autoregressive recode loop returns the next token (not free).

The Polish room-temperature blue quasi-BIC laser (Fąs et al., *Nano Letters*, 2026) is treated as a **candidate source**, after electrical pumping and addressable arrays exist. It is not treated as the compute core.

A full transformer in one crystal is not a near-term claim. The honest split in the papers is: static FFN / MoE experts as optical ROM; input-dependent attention as a hybrid electronic or reconfigurable photonic block.

## Co to jest (PL)

**OLLMU** to koncepcja pasywnego rdzenia dyfrakcyjnego: processing w świetle, light cache na krótkie echo, SiliconCache i pamięć kontekstu poza kryształem. Model uczy się z nich korzystać **zanim** geometria zostanie zapisana.

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
