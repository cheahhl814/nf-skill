---
name: nf-skill
description: Build scalable, containerized, reproducible bioinformatics pipelines with Nextflow DSL2. Covers clean code standards, modular design, config separation, containerization, cloud execution, and version-aware syntax maintenance. Use when creating portable workflows, nf-core style pipelines, or multi-environment analyses.
version: 5
updated: "2026-09-16"
triggers:
  - "nextflow pipeline"
  - "nextflow workflow"
  - "run nextflow"
  - "DSL2 pipeline"
  - "pipeline execution"
  - "build a bioinformatics pipeline"
  - "nf-core"
  - "containerized pipeline"
  - "reproducible pipeline"
  - "nextflow config"
  - "nextflow process"
  - "nextflow channel"
  - "workflow orchestration"
  - "HPC pipeline"
  - "cloud pipeline nextflow"
---

# Nextflow Pipelines

**"Build a reproducible, scalable bioinformatics pipeline."** -> Write modular DSL2 workflows with explicit channels, containerized processes, separate configuration, and environment profiles for portable execution on local, HPC, and cloud platforms.

## The Single Most Important Modern Insight

**A pipeline is software — treat it like code.** Nextflow's power is not just parallel execution; it is the separation of *logic* (processes/workflows), *configuration* (profiles/resources), and *data* (channels/parameters). Pipelines that mix all three become unmaintainable. The DSL2 module system lets you compose pipelines from tested, reusable components. **Write small, pure processes with explicit inputs/outputs; compose them in workflow blocks; configure them in profiles.**

## Clean Code Standards for Nextflow

These rules keep pipelines readable, maintainable, and debuggable:

| Rule                  | Convention                        | Example                                      |
| --------------------- | --------------------------------- | -------------------------------------------- |
| **DSL version**       | Always DSL2                       | `nextflow.enable.dsl = 2`                    |
| **Process names**     | UPPER_CASE                        | `process FASTQC { ... }`                     |
| **Workflow names**    | lowerCamelCase                    | `workflow rnaseqPipeline { ... }`            |
| **Channel names**     | lower_snake + `_ch` suffix        | `reads_ch`, `bam_ch`                         |
| **Emit names**        | Always explicit                   | `emit: html`                                 |
| **Tags**              | Always for traceability           | `tag "${sample_id}"`                         |
| **Comments**          | Explain channel transformations   | `// Group by sample, collect per chromosome` |
| **Process length**    | ≤ 50 lines; refactor if longer    | Split into subworkflows                      |
| **Config separation** | Never hardcode paths in `.nf`     | Use `params` + `nextflow.config`             |
| **Containers**        | One per process; pinned tags      | `container 'biocontainers/fastqc:0.12--...'` |
| **Resources**         | Use `label` not raw `cpus/memory` | `label 'process_medium'`                     |

## Prerequisites

- **Environment**: Active Pixi environment. Refer to [pixi-skill](https://github.com/cheahhl814/pixi-skill).
- **Input**: FASTQ, BAM, VCF, or other bioinformatics data files.
- **Compute**: Local, SLURM, AWS Batch, Google Life Sciences, or Kubernetes.

## Installation

```bash
pixi add nextflow
# For containers: Docker or Singularity/Apptainer pre-installed

# Verify version (check for DSL2 compatibility)
nextflow -version
```

## Version Awareness — Keeping Syntax Current

Nextflow evolves rapidly. **Always check your version before writing or adapting code.**

### How to check current syntax

| Command             | Purpose                               |
| ------------------- | ------------------------------------- |
| `nextflow -version` | Installed version                     |
| `nextflow help`     | All CLI flags (check for new options) |
| `nextflow help run` | Run-specific flags                    |
| `nextflow info`     | System info + plugin versions         |

### Where to find authoritative syntax

| Resource                   | URL                                              | What it covers                                      |
| -------------------------- | ------------------------------------------------ | --------------------------------------------------- |
| **Official docs (latest)** | https://www.nextflow.io/docs/latest/             | DSL2 syntax, processes, channels, config, executors |
| **DSL2 migration guide**   | https://www.nextflow.io/docs/latest/dsl2.html    | DSL1 → DSL2 differences (DSL1 is deprecated)        |
| **nf-core style guide**    | https://nf-co.re/docs/contributing/guidelines    | Production-grade conventions, labels, linting       |
| **Release notes**          | https://github.com/nextflow-io/nextflow/releases | Breaking changes, new features, deprecated syntax   |
| **nf-core modules**        | https://github.com/nf-core/modules               | Community-tested process wrappers                   |
| **📦 Ingested corpus**     | `nextflow-docs-corpus/` (this skill, 12 + 1 pages) | Offline Markdown snapshot for verbatim lookups — see "Verified Syntax Reference" section below |

### Syntax change red flags

| Version era | Key changes                                              |
| ----------- | -------------------------------------------------------- |
| ≤ 20.10     | DSL1 only; no `include`                                  |
| 21.04+      | DSL2 stable; `nextflow.enable.dsl = 2` required          |
| 22.04+      | `workflow` keyword mandatory in DSL2; `emit:` preferred  |
| 23.04+      | Strict mode warnings; plugin system overhaul             |
| 23.10       | `nextflow.config` auto-discovery changes                 |
| 25.04       | Workflow outputs 3rd preview (BREAKING: `publish:` section allowed only in the entry workflow; outputs assigned, not `>>`-chained; default publish dir is the base outdir, not a subdirectory) |
| 25.10 (stable LTS) | BREAKING: `google-lifesciences` executor **removed** (Cloud Life Sciences API discontinued Jul 2025 — use `google-batch`); AWS Java SDK v1 → v2 (credential/endpoint config changes); workflow-outputs preview changes — remove legacy feature flags when upgrading |
| 25.10.x     | Current stable line (25.10.5); v2 parser fixes; strict-syntax runtime checks tightened |
| 26.x-edge   | Experimental: AI agent primitive (agents as first-class tasks), `smolvm` container engine, `singularity.resourceLimits`, formatter preserves comments — do not depend on edge features in production pipelines |

**Rule:** Before writing a new pipeline, run `nextflow -version`, open the docs for that version, and cross-check any syntax that looks unfamiliar. **Never assume DSL2 syntax from a 2-year-old tutorial still works.**

### Modern directions (next: workflow outputs & strict syntax)

- **Workflow outputs (`publish:` in the entry workflow)** — the declarative successor to `publishDir` (preview since 25.04, refined in 25.10). Outputs are *assigned* in the workflow `publish:` block, and only the entry workflow may declare it. **Continue using `publishDir` per-process for now** (it remains fully supported and nf-core still uses it); port to `publish:` only when targeting ≥ 25.10 exclusively — the two mechanisms must not be mixed on the same output.
- **Strict syntax** — opt in with `nextflow enable strict` (or `nextflow run -strict-syntax`): catches undeclared variables/params, deprecated syntax, and ambiguous channel composition at parse time. Recommended for new pipelines; fixes for 25.10.x strict-mode false positives landed through 25.10.4/5.

## Verified Syntax Reference — Ingested Corpus

For verbatim syntax lookups (the exact form of `errorStrategy 'retry'`, the full list of channel operators, the executor names available in `nextflow.config`), this skill ships a pre-ingested Markdown corpus at `nextflow-docs-corpus/`. The corpus is **read-only** — a snapshot of upstream docs, not a substitute.

### What's in the corpus

| Directory | Source | Pages | Total size |
|---|---|---|---|
| `nextflow-docs-corpus/nextflow-dsl/` | `https://www.nextflow.io/docs/latest/` (Nextflow.io official docs, Docusaurus) | 12 | ~237 KB |
| `nextflow-docs-corpus/nfcore-style/` | `https://nf-co.re/docs/contributing/guidelines` (nf-core community guidelines, Astro/Starlight) | 1 | ~48 KB |

The 12 Nextflow pages cover the core DSL2 surface: `process`, `channel`, `operator`, `config`, `workflow`/`script`, `executor`, plus `sharing`, `tracing`, and `getstarted`. The nf-core guidelines cover all mandatory requirements (DSL2, MIT license, Docker, CI testing, semantic versioning, nf-core lint, …) and best-practice recommendations (Bioconda, nf-test, DOIs, cloud portability, …).

See `nextflow-docs-corpus/README.md` for the full file inventory, per-file sizes, and provenance.

### When to use the corpus vs the standards in this skill

- **Use the corpus** for: *"What are all the executor names?"*, *"What's the syntax for `errorStrategy 'retry'` with a max retry count?"*, *"What does `groupTuple( by: 0, sort: 'col1' )` do exactly?"*, *"What are the nf-core lint rules?"*
- **Use this skill** for: style and standards (*"Use UPPER_CASE process names"*, *"Separate config from logic"*, *"Always pin container versions"*), judgment calls (*"When should I use a subworkflow?"*), and pipeline architecture (*"How do I structure a multi-sample RNA-seq pipeline?"*).

The skill teaches **how to write pipelines**. The corpus provides **verbatim syntax reference**. Both work together.

### Re-ingesting the corpus

The corpus was ingested on **2026-08-08** using the [`docs-ingest`](https://github.com/cheahhl814/docs-ingest) skill. To re-ingest when Nextflow ships a new release or nf-core revises guidelines, re-crawl the target pages into HTML first (docs-ingest workflow: fetch `https://www.nextflow.io/docs/latest/<page>.html` for the 12 core pages + the nf-core guidelines page), then convert:

```bash
# Convert freshly crawled HTML to Markdown (adjust page list to what was re-crawled)
python3 ../docs-ingest/html_to_md.py --batch <(ls <crawl-dir>/*.html) --outdir nextflow-docs-corpus/nextflow-dsl/
python3 ../docs-ingest/html_to_md.py <crawl-dir>/nfcore_guidelines.html > nextflow-docs-corpus/nfcore-style/guidelines.md

# Verify zero boilerplate
grep -rE '(^|\n)\s*<(nav|header|footer)|class="[^"]*(sidebar|breadcrumb)[^"]*"|toggle navigation|site-footer' nextflow-docs-corpus/ | wc -l
# Should be 0
```

After re-ingestion, regenerate the fingerprint file:

```bash
find nextflow-docs-corpus -type f -name '*.md' -exec md5sum {} + | sort > nextflow-docs-corpus/.fingerprints
```

The current fingerprints are in `nextflow-docs-corpus/.fingerprints`. Use them to detect when a re-ingestion is needed (or to confirm none was).

## Procedure

### 1. Pipeline Skeleton — Clean Structure

```
pipeline/
├── main.nf              # Entry point: workflow composition
├── nextflow.config      # All parameters, profiles, resources
├── modules/
│   ├── fastqc.nf
│   ├── fastp.nf
│   └── salmon.nf
├── subworkflows/
│   ├── qc.nf
│   └── quant.nf
└── conf/
    ├── base.config        # Default resource labels
    ├── modules.config     # Per-process publishDir
    └── test.config        # Test dataset parameters
```

### 2. main.nf — Entry Point

```groovy
#!/usr/bin/env nextflow
nextflow.enable.dsl = 2

/*
 * RNA-seq quantification pipeline
 * Requires: Nextflow 23.10+, DSL2
 */

include { QC } from './subworkflows/qc'
include { QUANT } from './subworkflows/quant'

workflow {
    // Channel: file pairs → tuple(sample_id, [R1, R2])
    reads_ch = Channel.fromFilePairs(params.reads, checkIfExists: true)

    // Run QC subworkflow
    QC(reads_ch)

    // Run quantification subworkflow
    QUANT(reads_ch)
}
```

### 3. nextflow.config — All Configuration

```groovy
// No logic here. Only params, profiles, and resources.

params {
    reads        = "data/*_{1,2}.fq.gz"
    salmon_index = "ref/salmon_index"
    outdir       = "results"
}

// Validate required parameters
if (!params.salmon_index) {
    error "Missing required parameter: --salmon_index"
}

profiles {
    standard {
        process.executor = 'local'
    }
    slurm {
        process.executor = 'slurm'
        process.queue    = 'normal'
    }
    docker {
        docker.enabled    = true
        docker.runOptions = '-u $(id -u):$(id -g)'
    }
    singularity {
        singularity.enabled    = true
        singularity.autoMounts = true
    }
    test {
        params.reads        = "${projectDir}/test_data/*_{1,2}.fq.gz"
        params.salmon_index = "${projectDir}/test_data/salmon_index"
    }
}

// Resource labels (nf-core style)
process {
    withLabel: 'process_single' {
        cpus   = 1
        memory = 6.GB
        time   = 4.h
    }
    withLabel: 'process_low' {
        cpus   = 2
        memory = 12.GB
        time   = 4.h
    }
    withLabel: 'process_medium' {
        cpus   = 6
        memory = 36.GB
        time   = 8.h
    }
    withLabel: 'process_high' {
        cpus   = 12
        memory = 72.GB
        time   = 16.h
    }
    withLabel: 'process_long' {
        cpus   = 1
        memory = 6.GB
        time   = 20.d
    }
}

// Trace and reporting
trace {
    enabled = true
    file    = "${params.outdir}/pipeline_info/execution_trace.txt"
}

report {
    enabled = true
    file    = "${params.outdir}/pipeline_info/execution_report.html"
}

timeline {
    enabled = true
    file    = "${params.outdir}/pipeline_info/execution_timeline.html"
}
```

### 4. Process Module — Clean Pattern

```groovy
// modules/fastp.nf

process FASTP {
    tag "${sample_id}"
    label 'process_medium'

    container 'quay.io/biocontainers/fastp:0.23.4--h5f740d0_0'

    input:
    tuple val(sample_id), path(reads)

    output:
    tuple val(sample_id), path("${sample_id}_{1,2}.trimmed.fq.gz"), emit: reads
    path "${sample_id}_fastp.json"                                 , emit: json
    path "${sample_id}_fastp.html"                                 , emit: html

    script:
    def r1 = reads[0]
    def r2 = reads[1]
    """
    fastp \
        -i ${r1} -I ${r2} \
        -o ${sample_id}_1.trimmed.fq.gz -O ${sample_id}_2.trimmed.fq.gz \
        --json ${sample_id}_fastp.json \
        --html ${sample_id}_fastp.html \
        --thread ${task.cpus} \
        --detect_adapter_for_pe
    """
}
```

**Clean process checklist:**

- [ ] `tag` for traceability in logs
- [ ] `label` for resource allocation
- [ ] `container` with pinned version
- [ ] `emit` names on every output
- [ ] Multiline script with `\` continuation
- [ ] No hardcoded paths — only `${params}` or channel inputs

### 5. Subworkflow — Logical Grouping

```groovy
// subworkflows/qc.nf

include { FASTQC } from '../modules/fastqc'
include { FASTP  } from '../modules/fastp'
include { MULTIQC } from '../modules/multiqc'

workflow QC {
    take:
    reads_ch   // tuple(val(sample_id), path([R1, R2]))

    main:
    // Run FastQC on raw reads
    FASTQC(reads_ch)

    // Trim reads
    FASTP(reads_ch)

    // Collect all QC outputs for MultiQC
    qc_files_ch = FASTQC.out.zip
        .mix(FASTP.out.json)
        .collect()

    MULTIQC(qc_files_ch)

    emit:
    trimmed_reads = FASTP.out.reads
    qc_report     = MULTIQC.out.report
}
```

### 6. Channel Operations — Explicit and Commented

```groovy
// From file pairs (most common)
reads_ch = Channel.fromFilePairs(params.reads, checkIfExists: true)

// From samplesheet CSV
samples_ch = Channel.fromPath(params.samplesheet)
    .splitCsv(header: true)
    .map { row ->
        // Validate required columns exist
        if (!row.sample || !row.fastq_1 || !row.fastq_2) {
            error "Missing columns in samplesheet: ${row}"
        }
        tuple(row.sample, file(row.fastq_1), file(row.fastq_2))
    }

// Combine with reference (cartesian product)
reads_ch.combine(reference_ch)
    .set { aligned_ch }

// Group by key after per-chromosome processing
per_chrom_ch.groupTuple(by: 0)
    .set { merged_ch }
```

### 7. Error Handling and Resilience

```groovy
process MEMORY_HEAVY {
    label 'process_high'
    errorStrategy 'retry'
    maxRetries 3
    memory { 32.GB * task.attempt }  // Double memory on retry

    input:
    path bam

    output:
    path "*.sorted.bam", emit: bam

    script:
    """
    samtools sort -@ ${task.cpus} -m ${(task.memory.toBytes() / task.cpus / 2).intValue()}M \
        -o ${bam.baseName}.sorted.bam ${bam}
    """
}
```

| Strategy          | When to use                                    |
| ----------------- | ---------------------------------------------- |
| `retry` (default) | Transient failures (memory, I/O)               |
| `ignore`          | Optional processes whose failure is acceptable |
| `finish`          | Run started tasks to completion, then stop     |
| `terminate`       | Kill everything immediately                    |

### 8. Caching and Resume

```bash
# Resume from last successful run (uses task hash caching)
nextflow run main.nf -resume

# Clean work directory (removes intermediate files)
nextflow clean -f

# Show execution trace
nextflow log

# Run specific profile + resume
nextflow run main.nf -profile docker,test -resume
```

**Cache invalidation:** Change any input file, script block, or container image → hash changes → task reruns. Change only `nextflow.config` → task hashes unchanged → resume works.

### 9. Container Best Practices

```groovy
// Always pin container versions — never use :latest
process ALIGN {
    container 'quay.io/biocontainers/bwa:0.7.17--h5bf99c6_8'
    // NOT: container 'biocontainers/bwa:latest'
}
```

| Container source           | Pin format                                  | Example                                   |
| -------------------------- | ------------------------------------------- | ----------------------------------------- |
| Biocontainers (Quay)       | `quay.io/biocontainers/tool:version--build` | `salmon:1.10.0--h7e5ed60_0`               |
| Biocontainers (Docker Hub) | `biocontainers/tool:version`                | `fastqc:0.12.1`                           |
| Custom                     | `registry/repo:tag@sha256:digest`           | `myrepo/pipeline:v1.2.3@sha256:abc123...` |

### 10. Cloud Execution

```groovy
// nextflow.config — AWS Batch profile
profiles {
    awsbatch {
        process.executor = 'awsbatch'
        process.queue    = 'my-batch-queue'

        aws {
            region = 'us-east-1'
            batch {
                cliPath = '/usr/local/bin/aws'
            }
        }
    }
    google {
        process.executor = 'google-life-sciences'
        google.project   = 'my-gcp-project'
        google.location  = 'us-central1'
    }
}
```

```bash
# Run on AWS with S3 work directory
nextflow run main.nf \
    -profile awsbatch,docker \
    -bucket-dir s3://my-bucket/work \
    --outdir s3://my-bucket/results
```

## Pitfalls

### Mixing config and logic

**Symptom:** Hardcoded paths in `.nf` files; pipeline breaks on new machines. **Fix:** All paths in `params` or `nextflow.config`. No strings in process scripts that aren't channel inputs or params.

### Missing `emit` names

**Symptom:** `process.out[0]` brittle indexing; workflow composition breaks when outputs change order. **Fix:** Always name outputs: `emit: reads`, `emit: report`.

### Unpinned containers

**Symptom:** Pipeline behavior changes silently when upstream image updates. **Fix:** Pin to exact version + build hash. Never `:latest`.

### DSL1 syntax in DSL2 pipeline

**Symptom:** `process foo { ... }` without `workflow` block; channels implicitly connected. **Fix:** `nextflow.enable.dsl = 2` and explicit `workflow { ... }` block.

### Ignoring `-resume`

**Symptom:** Re-running expensive processes unnecessarily. **Fix:** Always use `-resume` during development; only `-resume false` for clean full re-runs.

### Memory explosion with `collect()`

**Symptom:** `collect()` on thousands of files crashes the driver JVM. **Fix:** Use `buffer(size: 100)` or `groupTuple(size: 100)` to batch; or use `toList()` only when the list is small.

## Quantitative Thresholds

| Threshold                                    | Rationale                                         |
| -------------------------------------------- | ------------------------------------------------- |
| Nextflow ≥ 23.10                             | DSL2 stable; current LTS features                 |
| Process ≤ 50 lines                           | Maintainable; refactor into subworkflow if longer |
| Container pinned to version+build            | Reproducibility                                   |
| `maxRetries` ≤ 3                             | Diminishing returns; investigate systemic failure |
| `memory { x * task.attempt }` ≤ 4× base      | Prevent runaway resource consumption              |
| Channel batch size ≤ 100 for `collect()`     | Prevent driver JVM heap exhaustion                |
| ESS ≥ 200 (if Bayesian step inside pipeline) | MCMC convergence inside Nextflow process          |

## Verification

- [ ] `nextflow.enable.dsl = 2` set in `main.nf`.
- [ ] All processes have `tag`, `label`, `container`, and named `emit` outputs.
- [ ] No hardcoded paths in `.nf` files — only `params` and channel inputs.
- [ ] `nextflow.config` contains all parameters, profiles, and resource labels.
- [ ] Containers pinned to exact version (not `:latest`).
- [ ] `include` used for modularity; subworkflows for logical grouping.
- [ ] `-resume` tested and working.
- [ ] `nextflow run main.nf -profile test` succeeds on small dataset.
- [ ] Trace/report/timeline enabled for debugging.
- [ ] Syntax checked against Nextflow version-specific docs.

## Update Check

This skill ships a self-update check that compares the deployed `SKILL.md` (and the rest of the skill) against the upstream `github.com/cheahhl814/nf-skill` repo via `git fetch` + SHA diff — no GitHub API call, no extra dependencies.

```bash
# From this skill's directory:
python3 bin/skill-update-check.py
# Or, for skills with pixi.toml:
pixi run update-check
# Verdict legend (exit code in parens):
#   UP-TO-DATE       (0)  local HEAD matches origin/HEAD
#   LOCAL-AHEAD      (0)  unpushed local commits; no action needed
#   BEHIND-BY-N      (1)  upstream is N commits ahead → pull: `git -C ~/.agents/skills/nf-skill pull`
#   OFFLINE          (2)  git fetch failed (no network / no credentials); informational
#   NO-ORIGIN        (2)  no `origin` remote configured; informational
```

When `BEHIND-BY-N`, the script prints the canonical fix. Deployment is a **symlink** (`~/.agents/skills/nf-skill` → the workspace copy in `@skills/nf-skill/`), so a `git pull` inside the deployed path updates the live skill — no rsync needed. When `OFFLINE`, the script still prints `local_sha` + deployed version so the user can compare by hand. The full implementation is in `bin/skill-update-check.py` (synced from the [bioinfo-skill-creator](https://github.com/cheahhl814/bioinfo-skill-creator) meta-skill v1.1.0+).

## Related Skills

- [pixi-skill](https://github.com/cheahhl814/pixi-skill) — Environment setup for Nextflow and container tools
- `skills/read-qc-analysis/SKILL.md` — FastQC/MultiQC processes for QC pipelines
- `skills/read-trimming-cleaning/SKILL.md` — fastp/Trimmomatic processes for trimming pipelines
- `skills/gene-quantification/SKILL.md` — Salmon/kallisto processes for quantification pipelines
- `skills/differential-expression-analysis/SKILL.md` — DESeq2/edgeR processes for DE pipelines
- `skills/ortholog-inference/SKILL.md` — OrthoFinder processes for phylogenomics pipelines

## References

- Di Tommaso P, et al. 2017. Nextflow enables reproducible computational workflows. *Nat Biotechnol* 35:316.
- Ewels PA, et al. 2020. The nf-core framework for community-curated bioinformatics pipelines. *Nat Biotechnol* 38:276.
- Nextflow DSL2 documentation. https://www.nextflow.io/docs/latest/dsl2.html
- nf-core style guidelines. https://nf-co.re/docs/contributing/guidelines
- nf-core modules repository. https://github.com/nf-core/modules
- Biocontainers registry. https://biocontainers.pro/
