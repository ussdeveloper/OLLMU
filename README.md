# OLLMU

**Optical Large Language Model Unit** — a public, bilingual concept paper, not a working optical LLM.

[English concept](CONCEPT.en.md) · [Polska koncepcja](CONCEPT.pl.md) · [References](REFERENCES.md) · [Attribution](ATTRIBUTION.md)

License: [OLLMU Idea Attribution License 1.0](LICENSE) — realisation is unrestricted; the public originator credit must be kept. The license does not protect the idea. This repository is a public disclosure.

Pomysł / idea originator: **Anisul Wałsymzerp**

---

## Status

This repository is **public**. It holds a **concept paper**. Nothing here is a fabricated chip, a trained model, a measured energy number for an LLM, or a claim that the 2026 GaN quasi-BIC laser already powers photonic AI.

Every technical statement in the papers is labelled:

| Label | Meaning |
| --- | --- |
| **Fact** | Reported in a cited publication |
| **Literature** | Established method, not original to OLLMU |
| **Hypothesis** | The architecture proposed here |
| **Estimate** | Order-of-magnitude reasoning, not a measurement |

## What OLLMU proposes

The load-bearing claim is about **weights**, not about the multiply. In token-by-token decoding the expensive part is reading the weights from memory on every token. OLLMU freezes those weights into a diffractive core so light performs the linear MAC in transit and the weight bus is gone. The same move, in digital silicon, is what Taalas does by hard-wiring a model into a chip. That citation is industrial context, not an OLLMU measurement.

Around that claim, the honest split is:

1. **static linear maps only** — FFN matrices and MoE expert matrices as optical ROM,
2. **electronics for everything input-dependent** — ReLU/GELU, softmax, attention, and the MoE router (a fixed linear medium cannot choose top-k),
3. a **light cache** (slower medium / slow light) that holds only a short, useful echo,
4. **SiliconCache** and **context memory** off-crystal, including the light–current–light conversion after every optical matrix,
5. a large cluster of bricks on **C-band (~1550 nm)** silica, with **SOA/EDFA** between experts and **3R regeneration** on SiliconCache (fibre dB/km is not ROM loss),
6. an autoregressive recode loop that returns the next token (not free).

The papers give a rough energy balance per token (§8.8). It is a threshold under named assumptions, not a win over a GPU. A burned snapshot cannot be fine-tuned, and frontier snapshots move on a scale of months.

The Polish room-temperature blue quasi-BIC laser (Fąs et al., *Nano Letters*, 2026) is treated as a **candidate source**, after electrical pumping and addressable arrays exist. It is not treated as the compute core.

## Co to jest (PL)

**OLLMU** to publiczna koncepcja nośnika wag: zamrożony operator liniowy siedzi w szkle, światło liczy MAC w przelocie, a nieliniowość, uwaga i router zostają w elektronice. Zysk istnieje tylko wtedy, gdy zniknięcie magistrali wag przeżyje koszt przetworników. Bilans w pracy jest progiem, nie pomiarem.

Licencja **nie ogranicza realizacji** i **nie chroni pomysłu**. Wymaga zachowania publicznej formy autora: **Anisul Wałsymzerp**.

Pełny tekst: [CONCEPT.pl.md](CONCEPT.pl.md).

## What this is not

- Not a replacement for EUV lithography.
- Not an electrically pumped BIC laser array.
- Not a measured competitor to GPUs.
- Not a patent filing. Publication is a disclosure; under Article 54 EPC it is part of the state of the art.
- Not a restriction on building hardware.

## Cite

See [CITATION.cff](CITATION.cff) and [ATTRIBUTION.md](ATTRIBUTION.md). Public repository: [github.com/ussdeveloper/OLLMU](https://github.com/ussdeveloper/OLLMU).

## Scope of this repo

Public documentation. No simulator and no hardware description language in this repository. The next artifact is the phase-1 differentiable simulator described in the papers: one FFN layer, analog noise, and energy per token.
