# Nextflow Docs Corpus

Verified Markdown corpus ingested from official Nextflow documentation via the [`docs-ingest`](../docs-ingest/) skill. This corpus is **read-only** — it is a snapshot of upstream docs, not a substitute for the canonical sources.

## Contents

### `nextflow-dsl/` — Nextflow DSL2 reference (Nextflow.io docs, 2026-08-08)

12 pages ingested from `https://www.nextflow.io/docs/latest/` (Docusaurus-rendered HTML, 169 URLs in sitemap, filtered to current docs, 4 URLs in sitemap are 404s and were skipped: `dnanexus`, `docker`, `ignite`, `metadata`):

| File | Source | Lines | Bytes | Purpose |
|---|---|---|---|---|
| `index.md` | https://www.nextflow.io/docs/latest/index.html | 59 | 5,822 | Landing page, links to other docs |
| `getstarted.md` | https://www.nextflow.io/docs/latest/getstarted.html | 160 | 8,639 | First-script tutorial, requirements, install paths |
| `basic.md` | https://www.nextflow.io/docs/latest/basic.html | 125 | 8,057 | Script basics, parameters, channels intro |
| `script.md` | https://www.nextflow.io/docs/latest/script.html | 377 | 17,808 | Script-level constructs: `if`/`else`, `println`, `workflow` glue |
| `process.md` | https://www.nextflow.io/docs/latest/process.html | 390 | 17,322 | All `process { ... }` directives: `input`, `output`, `script`, `shell`, `exec`, `stub`, `conda`, `container`, `cpus`, `memory`, `time`, `queue`, `disk`, `label`, `tag`, `errorStrategy`, `publishDir`, `cache`, etc. |
| `channel.md` | https://www.nextflow.io/docs/latest/channel.html | 893 | 32,280 | Channel factories (`Channel.from`, `Channel.fromPath`, `Channel.of`) |
| `operator.md` | https://www.nextflow.io/docs/latest/operator.html | 2,085 | 93,619 | Channel operators: `map`, `collect`, `groupTuple`, `flatten`, `combine`, `join`, `mix`, `tap`, `branch`, `buffer`, etc. |
| `config.md` | https://www.nextflow.io/docs/latest/config.html | 374 | 17,194 | `nextflow.config` schema: executors, profiles, process selectors, manifest |
| `executor.md` | https://www.nextflow.io/docs/latest/executor.html | 156 | 3,256 | Executor list: `local`, `sge`, `pbs`, `pbspro`, `slurm`, `lsf`, `nqsii`, `condor`, `k8s`, `awsbatch`, `google-batch`, etc. |
| `sharing.md` | https://www.nextflow.io/docs/latest/sharing.html | 118 | 9,220 | Module sharing, plugins, Wave, Fusion file system |
| `tracing.md` | https://www.nextflow.io/docs/latest/tracing.html | 562 | 15,964 | Execution trace, reports, timeline, resource usage |
| `amazons3.md` | https://www.nextflow.io/docs/latest/amazons3.html | 113 | 7,384 | AWS S3 storage (cloud-only) |

### `nfcore-style/` — nf-core contributing guidelines (nf-co.re docs, 2026-08-08)

1 page ingested from `https://nf-co.re/docs/contributing/guidelines` (Astro/Starlight-rendered HTML):

| File | Source | Lines | Bytes | Purpose |
|---|---|---|---|---|
| `guidelines.md` | https://nf-co.re/docs/contributing/guidelines | 602 | 48,248 | Mandatory requirements (DSL2, MIT license, Docker support, CI testing, semantic versioning, nf-core lint, etc.) + recommended best practices |

## Verification

Re-run these checks after any future re-ingestion:

```bash
# 1. Zero boilerplate contamination
grep -rE '(^|\n)\s*<(nav|header|footer)|class="[^"]*(sidebar|breadcrumb)[^"]*"|toggle navigation|site-footer' \
  nextflow-docs-corpus/ | wc -l
# Should be 0

# 2. All key Nextflow DSL keywords present
for kw in 'process \w+ \{' 'workflow \w+ \{' 'Channel\.from' '\.collect\(' '\.groupTuple' \
          'nextflow.enable.dsl' 'container ' 'cpus ' 'memory ' 'errorStrategy' 'publishDir'; do
    n=$(grep -rhE "$kw" nextflow-docs-corpus/nextflow-dsl/ | wc -l)
    echo "  $kw: $n"
done

# 3. nf-core key terms present
for kw in DSL2 modules subworkflow MultiQC nf-test 'process.high' 'process.medium'; do
    n=$(grep -ric "$kw" nextflow-docs-corpus/nfcore-style/ | wc -l)
    echo "  $kw: $n"
done
```

## Provenance

This corpus was ingested using:

- `docs-ingest` skill v4 (commit 74a5baa, 2026-08-08)
- `filters/strip_docusaurus.lua` — unwraps framework-noise divs
- `pandoc 3.9.0.2` with `--lua-filter` and `-t markdown --wrap=none --strip-comments`

The nf-core page required manual `markdown-content` div extraction because the Astro/Starlight framework's content wrapper is not yet supported by `docs-ingest`'s default content selectors. See `obs-2026-08-08-battle-tested-docs-ingest-against-7-sites-found-3-real-defec.md` in the wiki for context.

## When to re-ingest

Re-run the docs-ingest pipeline when:

- A new Nextflow minor release ships (24.10, 25.04, 25.10, …) — DSL additions are usually in release notes first.
- nf-core publishes a major style-guide revision (e.g., DSL2 → DSL3 if/when announced).
- The corpus's md5 fingerprints no longer match what's in `nf-skill/nextflow-docs-corpus/.fingerprints`.
