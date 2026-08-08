# Nextflow Pipelines

Build scalable, containerized, reproducible bioinformatics pipelines with Nextflow DSL2. Covers clean code standards, modular design, config separation, containerization, cloud execution, and version-aware syntax maintenance.

## What's in this skill

- **`SKILL.md`** — Clean code standards for Nextflow DSL2 pipelines (498 lines, 18 KB): pipeline skeleton, `main.nf`, `nextflow.config`, process modules, subworkflows, channel operations, error handling, caching, container best practices, cloud execution, quantitative thresholds, and pitfalls.
- **`nextflow-docs-corpus/`** — Verified Markdown corpus of the Nextflow DSL2 reference (12 pages from `nextflow.io/docs/latest/`) and nf-core contributing guidelines (1 page from `nf-co.re/docs/contributing/guidelines`). Total ~285 KB of clean Markdown, ingested on 2026-08-08 via the [`docs-ingest`](../docs-ingest/) skill. See `nextflow-docs-corpus/README.md` for the full inventory and per-file provenance.

## When to use

- "Build a Nextflow pipeline for variant calling"
- "Write a DSL2 process with container + cpus + memory directives"
- "Convert my Snakemake workflow to Nextflow"
- "Set up a multi-profile config (local / SLURM / AWS Batch)"
- "What's the exact syntax for `Channel.fromFilePairs` with glob patterns?"
- "What does nf-core require for the lint tests?"

## Two complementary resources

This skill teaches **how to write pipelines well** (judgment, standards, architecture). The `nextflow-docs-corpus/` provides **verbatim syntax reference** (every `process` directive, every channel operator, every executor name) ingested from official sources.

Use the skill for "should I…" questions. Use the corpus for "what is…" questions.

## Re-ingesting the corpus

The corpus was ingested on 2026-08-08. Re-ingest when:

- A new Nextflow minor release ships (24.10, 25.04, 25.10, …)
- nf-core publishes a major style-guide revision
- The corpus's `.fingerprints` no longer match what you see

See `nextflow-docs-corpus/README.md` for the re-ingestion procedure.

## Quick start

```bash
# Read the skill
cat SKILL.md

# Search the corpus for syntax
grep -rn "errorStrategy 'retry'" nextflow-docs-corpus/nextflow-dsl/
grep -rn "process.high" nextflow-docs-corpus/nfcore-style/

# Verify corpus integrity
md5sum nextflow-docs-corpus/nextflow-dsl/*.md nextflow-docs-corpus/nfcore-style/*.md \
  | diff - nextflow-docs-corpus/.fingerprints
# Should be identical (after stripping the leading '# md5 fingerprints...' comment line)
```

## Battle test

| Source | Strategy | Outcome |
|---|---|---|
| `https://www.nextflow.io/docs/latest/` (Nextflow DSL docs, Docusaurus) | docs-ingest Strategy 2 (sitemap) + `filters/strip_docusaurus.lua` | 12 pages, 237 KB, 0 boilerplate hits, all key DSL keywords present (23 `process` declarations, 11 `workflow` blocks, 19 `Channel.from`, 16 `script:`, 13 `output:`) |
| `https://nf-co.re/docs/contributing/guidelines` (nf-core style, Astro/Starlight) | manual extraction + docs-ingest lua-filter | 1 page, 48 KB, 0 boilerplate hits, 34 `modules` mentions, 17 `subworkflow`, 39 `label` mentions |

The Astro/Starlight framework used by nf-co.re is not yet supported by docs-ingest's default content selectors; the nf-core page was extracted manually using a `markdown-content` div heuristic. Future work: add Astro/Starlight to docs-ingest's content-selector list.

## Deployment status

This skill exists in `@skills/` but is **not currently deployed** to `~/.pi/agent/skills/`. To deploy:

```bash
mkdir -p ~/.pi/agent/skills/nextflow-pipelines
cp -r /home/cheahhl814/claude_workspace/bioinformatics/AIx-BIO/skills/nextflow-pipelines/* ~/.pi/agent/skills/nextflow-pipelines/
```