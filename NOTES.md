# Notes — RAG Cookbook

Working through [Gauntlet-AIDP/rag-cookbook](https://github.com/Gauntlet-AIDP/rag-cookbook) as a hands-on study of Retrieval-Augmented Generation. This fork captures what I do, what I learn, and what I break along the way.

**Author:** Alexander Gouyet — [portfolio](https://alexandergouyet.com) · [LinkedIn](https://www.linkedin.com/in/alexander-gouyet)
**Status:** In progress
**Context:** Working toward reapplying to [Gauntlet AI](https://gauntletai.com/) after rejection feedback asked for more visible technical work.

## Goals

- Understand each of the 5 RAG patterns (naive → metadata → hybrid → graph → agentic) by running the real code end-to-end, not just reading about it
- Develop an opinion on eval design: how scoring choices (binary vs. continuous, strict vs. lenient judges) shape what you measure
- Build the vocabulary to talk about RAG at multiple levels of precision (see [docs/glossary.md](docs/glossary.md))
- Identify bugs or friction points worth contributing back as PRs

## Progress

### Step 01 — Naive RAG ✅
- Cloned repo, set up venv, installed deps
- Configured MongoDB Atlas M0 + Vector Search index
- Ran `01-naive-rag/ingestion.py` → 2,021 chunks embedded into Atlas
- **Found a bug:** `INDEX_NAME = "naive"` in Ash's code, but Atlas's default index name is `vector_index`. Retrieval silently returned zero. Fixed in `retrieval.py`, `ingestion.py`, `evals/groundedness.py`, `evals/precision.py`. Candidate for an upstream PR.
- Ran retrieval + generation end-to-end; got grounded answers with cited sources
- Tested out-of-scope query ("what's his favorite French llama?") — system correctly refused. Prompt engineering + `debug_collection()` diagnostic both paid off.
- Ran Ash's evals:
  - **Groundedness: 75%** (6/8) — 2 failures were meta-commentary about context coverage, which Ash's strict binary judge flagged. Not hallucination — a prompt-tension finding.
  - **Precision: 85%** (34/40) — strong; weakest on broad philosophical queries.

### Step 02 — Metadata-filtered RAG — ⬜ next

### Step 03 — Hybrid Search — ⬜

### Step 04 — Graph RAG — ⬜

### Step 05 — Agentic RAG — ⬜

## What I'm taking away so far

- **Evals aren't truth; they're calibrated measurements.** The same RAG output scored 75% on Ash's binary-verdict judge and ~98% on my earlier continuous-score judge. Same system, different numbers. What "good" means depends on the rubric.
- **Prompt tensions are real.** Ash's generation prompt says "acknowledge what's missing"; his judge penalizes acknowledgments. The LLM did what it was told; the judge flagged it. This is the kind of thing evals surface.
- **Separation of concerns protects you.** Retrieval always returns *something* — irrelevant chunks included. The grounding prompt is what forces honest refusals. The two layers do different jobs.
- **Naive RAG's known weaknesses are exactly what steps 02-05 fix.** Year-scoping (step 02's metadata filter), proper-noun queries (step 03's hybrid), multi-hop reasoning (step 04's graph), and tool selection (step 05's agent). The cookbook is a structured progression, not a random list.

## Changes I've made in this fork

- `01-naive-rag/retrieval.py`, `ingestion.py`, `evals/groundedness.py`, `evals/precision.py` — changed `INDEX_NAME` from `"naive"` to `"vector_index"` so code matches Atlas's default-suggested index name. Silent failure before; working now.

## Related reference

- [docs/glossary.md](docs/glossary.md) — living dictionary of ~40 RAG terms
- [docs/eval-framework.md](docs/eval-framework.md) — the 7-metric framework I think about evals through (Ragas-aligned)

## License

Inherits MIT from upstream.
