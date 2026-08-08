# Overview

The rise of big data has made it increasingly necessary to be able to analyze and perform experiments on large datasets in a portable and reproducible manner. Parallelization and distributed computing are the best ways to tackle this challenge. However, the tools commonly available to computational scientists often lack good support for these techniques or provide a model that fits poorly with the needs of computational scientists and require knowledge of complex tools and APIs. Nextflow was created to address these challenges.

The Nextflow language is inspired by [the Unix philosophy](https://en.wikipedia.org/wiki/Unix_philosophy){target="_blank" rel="noopener noreferrer"}, in which many simple command line tools can be composed into powerful workflows. Similarly, a Nextflow script consists of many simple processes composed into pipelines. Each process executes a given tool or script in any supported language, and by specifying the process inputs and outputs, Nextflow coordinates the execution of tasks for you.

The Nextflow runtime integrates with popular execution platforms (i.e., HPC schedulers and cloud providers) and software tools (e.g., Git, Docker, and Conda), allowing you to fully describe a computational pipeline with all of its dependencies and run it in nearly any environment - write once, run anywhere.

::: section
## Processes and dataflow[​](#processes-and-dataflow "Direct link to Processes and dataflow"){.hash-link aria-label="Direct link to Processes and dataflow" translate="no"} {#processes-and-dataflow .anchor .anchorTargetStickyNavbar_Vzrq}

A Nextflow pipeline script is made by joining together different processes. Each process can be written in any scripting language that can be executed by the Linux platform (Bash, Perl, Ruby, Python, etc.).

A process can define one or more *inputs* and *outputs*. Data flows from process to process through asynchronous dataflow structures, known as *channels* and *values*. The data dependencies between these processes implicitly determines the flow of execution. For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
// Script parameters
params.query = "/some/data/sample.fa"
params.db = "/some/path/pdb"

// Define the blast_search process
process blast_search {
  input:
  path query
  path db

  output:
  path "top_hits.txt"

  script:
  """
  blastp -db $db -query $query -outfmt 6 > blast_result
  cat blast_result | head -n 10 | cut -f 2 > top_hits.txt
  """
}

// Define the extract_top_hits process
process extract_top_hits {
  input:
  path top_hits
  path db

  output:
  path "sequences.txt"

  script:
  """
  blastdbcmd -db $db -entry_batch $top_hits > sequences.txt
  """
}

// Define the workflow
workflow {
  def query_ch = channel.fromPath(params.query)
  blast_search(query_ch, params.db)
  extract_top_hits(blast_search.out, params.db).view()
}
```

The above example defines two processes. Their execution order is not determined by the `blast_search` process comes before `extract_top_hits` in the script (it could be written the other way around). Instead, execution order is determined by their *dependencies* --- `extract_top_hits` depends on the output of `blast_search`, so `blast_search` will be executed first, and then `extract_top_hits`.

When the workflow is executed, it creates two processes (`blast_search` and `extract_top_hits`) that are connected by the `query_ch` channel. Each process executes a task and emits a value for each input that it receives. Whenever `blast_search` emits a value, `extract_top_hits` receives it through the `query_ch` channel.

See [Dataflow](/nextflow/process) to learn more.

::: section
## Execution abstraction[​](#execution-abstraction "Direct link to Execution abstraction"){.hash-link aria-label="Direct link to Execution abstraction" translate="no"} {#execution-abstraction .anchor .anchorTargetStickyNavbar_Vzrq}

While a process defines *what* command or script has to be executed, the *executor* determines *how* that script is run on the target system.

If not otherwise specified, processes are executed on the local computer. The local executor is useful for pipeline development and testing purposes, but for real world computational pipelines an HPC or cloud platform is often required.

Nextflow provides an abstraction between the pipeline\'s functional logic and the underlying execution system. Thus, it is possible to write a pipeline once and to seamlessly run it on your computer, a grid platform, or the cloud, by defining the target execution platform in the configuration file.

The following batch schedulers are supported:

- [Open Grid Engine](http://gridscheduler.sourceforge.net/){target="_blank" rel="noopener noreferrer"}
- [Univa Grid Engine](http://www.univa.com/){target="_blank" rel="noopener noreferrer"}
- [Platform LSF](http://www.ibm.com/systems/technicalcomputing/platformcomputing/products/lsf/){target="_blank" rel="noopener noreferrer"}
- [SLURM](https://computing.llnl.gov/linux/slurm/){target="_blank" rel="noopener noreferrer"}
- [Flux Framework](https://flux-framework.org/){target="_blank" rel="noopener noreferrer"}
- [PBS](http://www.pbsworks.com/gridengine/){target="_blank" rel="noopener noreferrer"}
- [Torque](http://www.adaptivecomputing.com/products/open-source/torque/){target="_blank" rel="noopener noreferrer"}
- [HTCondor](https://research.cs.wisc.edu/htcondor/){target="_blank" rel="noopener noreferrer"}

The following cloud platforms are supported:

- [Amazon Web Services (AWS)](https://aws.amazon.com/){target="_blank" rel="noopener noreferrer"}
- [Microsoft Azure](https://azure.microsoft.com/){target="_blank" rel="noopener noreferrer"}
- [Google Cloud Platform (GCP)](https://cloud.google.com/){target="_blank" rel="noopener noreferrer"}
- [Kubernetes](https://kubernetes.io/){target="_blank" rel="noopener noreferrer"}

See [Executors](/nextflow/executor) to learn more.

::: section
## Scripting language[​](#scripting-language "Direct link to Scripting language"){.hash-link aria-label="Direct link to Scripting language" translate="no"} {#scripting-language .anchor .anchorTargetStickyNavbar_Vzrq}

Nextflow is a workflow language based on [Java](https://en.wikipedia.org/wiki/Java_(programming_language)){target="_blank" rel="noopener noreferrer"} and [Groovy](https://groovy-lang.org/){target="_blank" rel="noopener noreferrer"}. It is designed to simplify writing scalable and reproducible pipelines. In most cases, you can leverage your existing programming skills to develop Nextflow pipelines without the steep learning curve that usually comes with a new programming language.

See [Scripts](/nextflow/script) for more information about the Nextflow scripting language.

::: section
## Configuration options[​](#configuration-options "Direct link to Configuration options"){.hash-link aria-label="Direct link to Configuration options" translate="no"} {#configuration-options .anchor .anchorTargetStickyNavbar_Vzrq}

Pipeline configuration properties are defined in a file named `nextflow.config` in the pipeline execution directory.

This file can be used to define which executor to use, the process\'s environment variables, pipeline parameters etc. For example:

``` {.prism-code .language-groovy .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process {
  executor = 'sge'
  queue = 'cn-el6'
}
```

See [Configuration](/nextflow/config) to learn more.

:::: {.col .col--3}
::: {.text-[.7rem] .mb-1 .uppercase .text-primary .font-semibold}
On this Page

- [Processes and dataflow](#processes-and-dataflow){.table-of-contents__link .toc-highlight}
- [Execution abstraction](#execution-abstraction){.table-of-contents__link .toc-highlight}
- [Scripting language](#scripting-language){.table-of-contents__link .toc-highlight}
- [Configuration options](#configuration-options){.table-of-contents__link .toc-highlight}
::::