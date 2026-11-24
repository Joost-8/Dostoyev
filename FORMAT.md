# The Dostoyev manifest, format 1

Every release has a manifest, `dostoyev-<version>.json`. FineNotes reads it
first and runs the model only if it understands it: a higher `format`, an
unknown `kind`, curve rows of another encoding, or graphs laid out otherwise
than the kind expects are refused with a message, never run.

```jsonc
{
  "format": 1,                 // this document; a reader refuses anything higher
  "id": "dostoyev",
  "version": "1.0",
  "name": "Dostoyev",
  "research": "A long (models/GA-long-last.npz)",
  "kind": "dual",              // which code runs it (FineNotes: src/recognition/writer/kinds.ts)
  "graphs": {                  // by the role the kind gives each graph
    "encode": {
      "file": "dostoyev-1.0-encode.int8.onnx",
      "inputs":  [{ "role": "ref.rows", "name": "rx", "type": "float32", "dims": [1, "R", 10] }, …],
      "outputs": [{ "role": "memory", "name": "mem", "type": "float32", "dims": [1, "R", 256] }, …]
    },
    "durations": { … },
    "velocity": { … }
  },
  "rows": {                    // the curve rows the model reads and writes
    "encoding": "grape2-raw", "channels": 10,
    "tol": 0.01, "norm": "skew=0,slant=0,scale=core",   // must equal the reader's (Grape 2)
    "mean": [ … 10 … ], "std": [ … 10 … ]               // the dual kind normalises by these
  },
  "reference": { "lines": 6, "curves": 144, "hold": 6.0 },
  "sampling": { "steps": 8, "sigma": 0.7, "guidance": 1.5, "lowGuidance": 0.35, "lowMix": 0.35, "maxDur": 14 },
  "chars": ["<blank>", " ", "!", … ]   // index 0 is the blank; a character's id is its index
}
```

## Every kind has

`format`, `id`, `version`, `kind`, `graphs` (each with `file`, `inputs` and
`outputs`; each tensor with `role`, `name`, `type` and `dims`), `rows`
(`encoding`, `channels`, `tol`, `norm`), `reference.lines`, and `chars`.

A tensor's `role` says what it is to the kind; the kind passes inputs and
reads outputs by position and checks each position's role and type, so two
inputs of one type in swapped places are refused rather than fed wrongly.
`name` is the tensor's name inside the ONNX graph.

## The dual kind (Dostoyev 1.0) also has

`rows.mean` and `rows.std` (one per channel), `reference.curves` and
`reference.hold`, and `sampling`. Its graphs, in order:

- `encode`: `ref.rows` float32, `ref.mask` bool, `ref.char` int64,
  `ref.place` int64, `ref.length` int64, `ref.separator` bool, `drop` bool →
  `memory` float32, `valid` bool, `summary` float32
- `durations`: `text.char` int64, `text.mask` bool, `summary` float32 →
  `logits` float32
- `velocity`: `rows` float32, `time` float32, `mask` bool, `char` int64,
  `place` int64, `length` int64, `memory` float32, `valid` bool, `summary`
  float32 → `velocity` float32

A later model of another kind (a pieces model, which is shown the letters a
text needs rather than whole lines) brings its own sections and graphs, and
a FineNotes that knows that kind.
