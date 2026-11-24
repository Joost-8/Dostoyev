# Dostoyev 1.0, the FineNotes handwriting writer

**Status: work in progress.** Released with FineNotes' Neaten.

| | |
|---|---|
| Files | `dostoyev-1.0.json` (manifest), `dostoyev-1.0-encode.int8.onnx` (2.1 MB), `dostoyev-1.0-durations.int8.onnx` (1.6 MB), `dostoyev-1.0-velocity.int8.onnx` (10.3 MB) |
| Licence | Apache 2.0 (`LICENSE`); attributions in `NOTICE`, which must travel with the files |
| Language | English (Latin letters, digits and common symbols; 95 characters, Grape 2.0's alphabet) |
| Manifest format | 1, kind `dual` (see `FORMAT.md`) |
| Research name | A long (`GA-long`, 2026-11-24) |

## What it does

Given a line of text and a few lines of one person's handwriting with their
text, it writes the line as pen strokes in that person's hand. It does not
copy strokes: it writes new ones, and tends to write them neater than the
person did.

- **Model:** a flow-matching generator over cubic Bézier curves ("curve
  rows", Grape 2.0's input format), about 9 million parameters. A
  transformer encodes the writer's lines into a memory and a style summary;
  a duration head gives each character its number of curves; a velocity
  network turns noise into curves in 8 steps, mixed between "this writer"
  and "no writer" by a guidance weight (1.5).
- **Shown:** up to 144 curves from the six of the writer's lines that share
  most letters with the text; long pen jumps in them are shortened.
- **In FineNotes:** each line is written three times and Grape keeps the try
  it reads best; words Grape still misreads are written again at low
  guidance (the repair). Only words Grape is sure of are replaced; the rest
  keep the writer's own strokes.

## Intended use

- Making a person's own handwritten notes neater, in their own hand, on
  their own device (FineNotes' Neaten).

Out of scope:

- Writing in someone else's hand to deceive. Dostoyev is shown the
  writer's own practice sheets in FineNotes; it is not a forgery tool, and
  must not be used as one.
- Mathematical layout, tables and drawings; other scripts and accented
  letters (not in the alphabet).
- Identifying or profiling writers.

## How well it works

Measured 2026-11-24 with the recipe FineNotes runs (no reader's pull, best of
3 by Grape 2.0, repair), judged by Grape 1.0, which took no part in the
writing. Character error rate in %; likeness is a writer-style score (higher
is closer to the writer).

| Test | Writers' own ink | Dostoyev 1.0 | Likeness |
|---|---|---|---|
| 283 lines of 72 writers never seen in training | 2.0 | 3.0 | 0.53 (own ink 0.51) |
| The author's second copy sheet (his first as the reference) | 23.1 | 5.3 | 0.78 |

On an iPad (Obsidian 1.15.1, WebAssembly, one thread): one try of a
31-character line in 0.66 s; about 48 MB of memory while writing. A whole
13-line page took 34 s in FineNotes.

Known weaknesses: it copies habits of a hand that make letters ambiguous (a
barred I can come out as a 7, a k written like a K stays broken), and it
still slips on single letters ("toder" for "today"). Capitals or digits the
writer never showed it are written in a plain hand.

## Training data

The model was trained only on public handwriting collections (listed in
`NOTICE`): BRUSH, IAM-OnDB, UNIPEN and PluColl, with lines joined from their
words. They were cleared for training Grape, a reader; whether each
permission also covers a writer is being confirmed before this model is made
public (marked OPEN in `NOTICE`). Grape 2.0 (Apache 2.0) aligned the training lines' letters to
their curves. The author's own copy sheets were used only to test the
finished model, not to train it.

It was trained without any part of the CROHME data sets; the run's lineage
record (every input file, by hash, checked against the time CROHME first
reached the training machines) is kept with FineNotes' research and backs
that claim.
