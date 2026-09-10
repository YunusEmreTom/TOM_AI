# TOM AI

An early, unfinished attempt at a Turkish-speaking desktop voice assistant:
speech to text, an LLM bridge, and speech synthesis as separate swappable
modules.

> **Status: archived exploration, not a working assistant.**
> The module boundaries and the individual pieces exist, but they were never
> integrated behind a single entry point. The ideas here were carried forward
> and finished in **[astr1 / ASTRO V1](https://github.com/yunusemretom/astr1)**,
> which does run end to end. Read this repository as a design sketch.

![demo](docs/demo.gif)

## Tech stack

![Python](https://img.shields.io/badge/Python-3.10-3776AB?logo=python&logoColor=white)
![Whisper](https://img.shields.io/badge/Whisper-STT-412991?logo=openai&logoColor=white)
![Coqui TTS](https://img.shields.io/badge/Coqui_TTS-Synthesis-4B32C3)
![CUDA](https://img.shields.io/badge/CUDA-Benchmarks-76B900?logo=nvidia&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-blue)

## What actually runs

Only the transcription module is usable on its own.

```bash
git clone https://github.com/yunusemretom/TOM_AI.git
cd TOM_AI

python3 -m venv .venv
source .venv/bin/activate
pip install -r TOM-Listener/requirements.txt

python TOM-Listener/main.py      # microphone to text
```

`TOM-Main/llm_test.py` and the CUDA and Vulkan probes under `TOM-Bridge/` are
standalone experiments, not a pipeline.

## How it works

The intended architecture split the assistant along the boundaries where I
expected to swap implementations:

| Module | Responsibility | State |
|---|---|---|
| `TOM-Listener/` | speech to text | transcription works |
| `TOM-Bridge/` | LLM request and response | GPU backend probes only |
| `TOM-Voice/` | speech synthesis | not implemented |
| `TOM-UI/` | interface | not implemented |
| `TOM-Utils/` | shared helpers | partial |

### What I got right and what I got wrong

The module split was the right instinct. Speech recognition, language model and
synthesis really are independent choices with independent failure modes, and
separating them is what later made a fallback chain possible.

What I got wrong was **building the boundaries before building anything that
crossed them.** I designed five modules and a directory per module before a
single sentence had made it from microphone to speaker. Each module then got
optimized in isolation, which is why this repository contains Vulkan and CUDA
backend benchmarks for a system that could not yet hold a conversation.

The concrete lesson, and the reason `astr1` is structured differently: get one
ugly path working end to end first, then let the seams appear where the pain
actually is. In ASTRO the module boundaries look similar, but they were drawn
after a working loop existed, and the fallback behavior at each boundary was
driven by failures I had actually hit.

`tests_old/` holds the earlier iterations, kept as a record rather than as code
to use.

## Known limitations

- No integrated entry point. The modules do not talk to each other.
- Synthesis and interface modules are empty.
- No tests, no packaging, no error handling worth the name.
- The GPU benchmarks were run on one machine and are not comparable across
  hardware.

## Roadmap

None. This project is superseded by
[astr1](https://github.com/yunusemretom/astr1). It is kept public because the
design notes and the mistake described above are worth more than deleting it.

## License

MIT. See [LICENSE](LICENSE).

Original Turkish notes are preserved in [README.tr.md](README.tr.md).
