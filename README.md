# Dostoyev

**Dostoyev writes handwriting in your own hand.** Show it a few lines you
wrote and give it a line of text, and it writes that text the way you would,
neater. It is the model behind **Beautify** in
[FineNotes](https://github.com/Joost-8/FineNotes), the Obsidian plugin for
handwritten notebooks: select some handwriting, choose Beautify, and the words
are written again, neater, in your hand, where yours were.

It runs on the device. FineNotes downloads it once, and no ink or text
leaves the device. It works from pen strokes, not from images.

**Status: work in progress.** Version 2.0 (the current one) writes English text. Its sister
model, [Grape](https://github.com/Joost-8/Grape), reads handwriting, and
Beautify uses both: Grape reads what you wrote, Dostoyev writes it again.

## Files

A version is a release of this repository (`v1.0`, `v2.0`, …). Each release holds:

| File | What it is |
|---|---|
| `dostoyev-<version>.json` | The manifest: which kind of model it is, its graphs and their inputs and outputs, the curve rows it reads and writes, its settings and alphabet ([FORMAT.md](FORMAT.md)) |
| `dostoyev-<version>-<role>.int8.onnx` | The model's ONNX graphs, weights stored as 8-bit integers (for 1.0 and 2.0: `encode`, `durations`, `velocity`) |

FineNotes pins every file by size and SHA-256. Versions can be built
differently: 1.0 is a `dual` model, 2.0 a `pieces` model. The manifest
says which, and a FineNotes too old to run a version says so rather than
running it wrongly.

## Licence

Apache 2.0 (`LICENSE`). The attributions in `NOTICE` must travel with the
model files. See [MODEL-CARD.md](MODEL-CARD.md) for what it was trained on,
how well it works and what it is not for.
