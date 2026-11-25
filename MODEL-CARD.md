# Dostoyev 2.0, the FineNotes handwriting writer

**Status: work in progress.** Released with FineNotes' Beautify.

| | |
|---|---|
| Files | `dostoyev-2.0.json` (manifest), `dostoyev-2.0-encode.int8.onnx` (1.6 MB), `dostoyev-2.0-durations.int8.onnx` (1.6 MB), `dostoyev-2.0-velocity.int8.onnx` (10.2 MB) |
| Licence | Apache 2.0 (`LICENSE`); attributions in `NOTICE`, which must travel with the files |
| Language | English (Latin letters, digits and common symbols; 95 characters, Grape 2.0's alphabet) |
| Manifest format | 1, kind `pieces` (see `FORMAT.md`) |
| Research name | C2 long (`GC2-long`, 2026-11-24) |

## What it does

Given a line of text and some lines of one person's handwriting with their
text, it writes the line as pen strokes in that person's hand. It does not
copy strokes: it writes new ones, and tends to write them neater than the
person did.

- **Model:** a flow-matching generator over cubic Bézier curves ("curve
  rows", Grape 2.0's input format), about 8.4 million parameters. A
  transformer encodes what it is shown into a memory; a duration head gives
  each character its number of curves; a velocity network turns noise into
  curves in 8 steps.
- **Shown:** one whole line of the writer, then pieces cut from their other
  lines: words that hold the characters the text needs, and lone letters
  (tried first half the time). A capital the writer never wrote is looked
  for as the small letter, and the other way round. About 256 curves in all,
  each tagged with its place in its piece, the piece's number and its kind
  (line, word, letter). Long pen jumps are shortened.
- **In FineNotes:** written at guidance 1.0, which needs no "no writer"
  pass, so a line takes half the work of 1.0. Each line is written twice and
  Grape keeps the try it reads best; words Grape still misreads are written
  again at low guidance (the repair). Only words Grape is sure of are
  replaced, and a replacement Grape does not read back as the word keeps the
  writer's own strokes. A neatness slider moves the guidance from 1.2 (most
  like the writer) to 0.75 (neatest).

## Intended use

- Making a person's own handwritten notes neater, in their own hand, on
  their own device (FineNotes' Beautify).
- Writing training ink for Grape, FineNotes' open-source handwriting
  recogniser.

Out of scope:

- Writing in someone else's hand to deceive. Dostoyev is shown the
  writer's own practice sheets in FineNotes; it is not a forgery tool, and
  must not be used as one.
- Mathematical layout, tables and drawings; other scripts and accented
  letters (not in the alphabet).
- Identifying or profiling writers.

## How well it works

Measured 2026-11-24. Character error rate in %; likeness is a writer-style
score (higher is closer to the writer).

One try at guidance 1.0, no reader's pull, judged by Grape 1.0 (which took
no part in the writing):

| Test | Writers' own ink | Dostoyev 2.0 | Likeness |
|---|---|---|---|
| 283 lines of 72 writers never seen in training | 3.5 | 3.5 | 0.54 (own ink 0.52) |
| The author's second copy sheet and 6 probe lines (his first sheet as the reference) | 23.1 | 9.4 | 0.78 |

FineNotes' own code (best of 2 and the repair, with Grape 2.0 both picking
and reading back, so that reader is not independent), on the author's
second sheet: 4.3 % at the slider's middle, 4.1 % at "most like my ink",
2.0 % at "neatest" (his own ink: 15.4 % by the same reader).

Shown lone letters and the other case, it writes capitals the writer only
wrote in small letters (a J from j's, an I from i's), which version 1.0
could not. Compared with 1.0 it keeps more of the writer's hand (size, word
gaps, letter shapes) and reads a little worse to a stranger.

On a laptop CPU (WebAssembly, one thread) a 31-character line takes 114 ms
of model time per try. Not yet timed on an iPad.

Known weaknesses: it copies habits of a hand that make letters ambiguous,
and it still slips on single letters. Lines with many rare characters get a
less complete reference.

## Version 1.0

Research name A long (`GA-long`), kind `dual`: shown up to 144 curves from
the six of the writer's lines that share most letters with the text,
guidance 1.5, best of 3. Other writers 3.0 % (their ink 2.0 %, Grape 1.0,
FineNotes' recipe), the author's sheet 5.3 % (his ink 23.1 %); on an iPad
0.66 s a try of a 31-character line, about 48 MB while writing.

## Training data

Both versions were trained only on public handwriting collections (listed in
`NOTICE`): BRUSH, IAM-OnDB, UNIPEN and PluColl, with lines joined from their
words, under the permissions and licences in `NOTICE`. Grape 2.0 (Apache
2.0) aligned the training lines' letters to their curves. The author's own
copy sheets were used only to test the finished models, not to train them.

They were trained without any part of the CROHME data sets; each run's
lineage record (every input file, by hash, checked against the time CROHME
first reached the training machines) is kept with FineNotes' research and
backs that claim.
