# Nextflow Pipelines

Build scalable, containerized, reproducible bioinformatics pipelines with Nextflow DSL2. This skill teaches clean-code standards and ships a verified Markdown corpus of the official Nextflow DSL2 reference and nf-core contributing guidelines, so an AI agent can answer both "how should I write this?" and "what is the exact syntax?".

## What's in this skill

- **`SKILL.md`** — Clean code standards for Nextflow DSL2 pipelines: pipeline skeleton, `main.nf`, `nextflow.config`, process modules, subworkflows, channel operations, error handling, caching, container best practices, cloud execution, quantitative thresholds, and pitfalls.
- **`nextflow-docs-corpus/`** — Verified Markdown corpus (~285 KB) ingested from official sources on 2026-08-08:
  - `nextflow-dsl/` — 12 pages from the official Nextflow DSL2 reference (process, channel, operator, config, executor, script, sharing, tracing, basic, getstarted, index, amazons3).
  - `nfcore-style/guidelines.md` — nf-core mandatory requirements (DSL2, MIT license, Docker, CI testing, semantic versioning, nf-core lint, …) and best-practice recommendations (Bioconda, nf-test, DOIs, cloud portability, …).

## When to use

- "Build a Nextflow pipeline for variant calling / RNA-seq / ChIP-seq"
- "Write a DSL2 process with container, cpus, memory, and time directives"
- "Convert a Snakemake workflow to Nextflow"
- "Set up a multi-profile config (local / SLURM / AWS Batch)"
- "What's the exact syntax for `Channel.fromFilePairs` with glob patterns?"
- "What does nf-core require for the lint tests?"

## Two complementary resources

The skill and the corpus answer different kinds of questions:

| Question type | Resource |
|---|---|
| "Should I…?" (judgment, style, architecture) | **`SKILL.md`** — standards and rationale |
| "What is…?" (verbatim syntax, official options) | **`nextflow-docs-corpus/`** — every process directive, channel operator, executor name |

A typical workflow: read `SKILL.md` for the architecture, then `grep` the corpus for the exact syntax of the directive you're writing.

## How to use this skill

This skill is consumed by **AI coding agents** (Pi, Claude Code, Codex, Opencode, etc.), not by humans directly. To use it, point your agent at the repository URL:

```
Import the skill from https://github.com/cheahhl814/nextflow-pipelines
into your skills directory.
```

The agent harness loads `SKILL.md` for instructions and can read the corpus files verbatim. After import, the agent will recognise triggers like *"build a Nextflow pipeline"*, *"DSL2 module"*, or *"nf-core lint"* and route to this skill.

## File structure

```
nextflow-pipelines/
├── README.md                       # This file
├── SKILL.md                        # Clean-code standards (read this first)
└── nextflow-docs-corpus/
    ├── README.md                   # Corpus inventory + provenance
    ├── .fingerprints               # md5sums (staleness detection)
    ├── nextflow-dsl/               # 12 Nextflow DSL reference pages
    │   ├── process.md              # All process directives
    │   ├── channel.md              # Channel factories
    │   ├── operator.md             # Channel operators (largest file, ~94 KB)
    │   ├── config.md               # nextflow.config schema
    │   ├── executor.md             # Executor list
    │   ├── script.md               # Script-level constructs
    │   ├── sharing.md              # Module sharing, Wave, Fusion
    │   ├── tracing.md              # Execution trace, reports, timeline
    │   ├── basic.md                # Script basics
    │   ├── getstarted.md           # First-script tutorial
    │   ├── index.md                # Landing page
    │   └── amazons3.md             # AWS S3 (cloud-only)
    └── nfcore-style/
        └── guidelines.md           # nf-core requirements + recommendations
```

## Corpus re-ingestion

The corpus is a snapshot of upstream documentation. To refresh it when Nextflow ships a new release or nf-core revises guidelines, you need an HTML→Markdown converter (the corpus was produced with `pandoc` plus a Lua filter that unwraps framework noise like Docusaurus's `theme-*` divs). See `nextflow-docs-corpus/README.md` for the full procedure.

If you only consume the corpus and don't re-ingest, you don't need any external tool — the Markdown files are self-contained and read like normal documentation.

## Provenance

| Source | Date | Pages | Total size |
|---|---|---|---|
| `https://www.nextflow.io/docs/latest/` (Nextflow DSL docs, Docusaurus) | 2026-08-08 | 12 | 237 KB |
| `https://nf-co.re/docs/contributing/guidelines` (nf-core style, Astro/Starlight) | 2026-08-08 | 1 | 48 KB |

The nf-core page required manual extraction of the `<div class="markdown-content">` block because the Astro/Starlight framework is not yet supported by docs-ingest's default content selectors. See `nextflow-docs-corpus/README.md` for the technical detail.

## Citation

If this skill helps you write or review a Nextflow pipeline, please cite the upstream sources:

- **Nextflow**: Di Tommaso P, et al. Nextflow enables reproducible computational workflows. *Nature Biotechnology* 35, 316–319 (2017). doi: [10.1038/nbt.3820](https://doi.org/10.1038/nbt.3820)
- **nf-core**: Ewels PA, et al. The nf-core framework for community-curated bioinformatics pipelines. *Nature Biotechnology* 38, 276–278 (2020). doi: [10.1038/s41587-020-0439-x](https://doi.org/10.1038/s41587-020-0439-x)
- **This skill**: `cheahhl814/nextflow-pipelines` (https://github.com/cheahhl814/nextflow-pipelines)

## License

This skill's text and code are released under the MIT License. The corpus content is sourced from the official Nextflow documentation (Apache 2.0) and the nf-core contributing guidelines (MIT) and retains those licenses; see `nextflow-docs-corpus/.fingerprints` for the per-file source URLs.
