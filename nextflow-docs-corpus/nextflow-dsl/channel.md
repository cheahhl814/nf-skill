# Workflows

In Nextflow, a **workflow** is a specialized function for composing [processes](/nextflow/process) and dataflow logic:

- An [entry workflow](#entry-workflow) is the entrypoint of a pipeline. It can take [parameters](#parameters) as inputs using the `params` block, and it can publish [outputs](#outputs) using the `output` block.

- A [named workflow](#named-workflows) is a workflow that can be called by other workflows. It can define its own inputs and outputs, which are called *takes* and *emits*.

- Both entry workflows and named workflows can contain [dataflow logic](#dataflow) such as calling processes, workflows, and channel operators.

note

This page describes the original workflow syntax. Nextflow also supports a [typed workflow syntax](/nextflow/workflow-typed) with statically typed inputs and outputs. See [Static typing](/nextflow/static-typing) for an overview.

::: section
## Entry workflow[​](#entry-workflow "Direct link to Entry workflow"){.hash-link aria-label="Direct link to Entry workflow" translate="no"} {#entry-workflow .anchor .anchorTargetStickyNavbar_Vzrq}

A script can define up to one *entry workflow*, which does not have a name and serves as the entrypoint of the script:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
workflow {
    channel.of('Bonjour', 'Ciao', 'Hello', 'Hola')
        .map { v -> "$v world!" }
        .view()
}
```

::: section
## Parameters[​](#parameters "Direct link to Parameters"){.hash-link aria-label="Direct link to Parameters" translate="no"} {#parameters .anchor .anchorTargetStickyNavbar_Vzrq}

Parameters can be declared by assigning a `params` property to a default value:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
params.input = '/some/data/file'
params.save_intermeds = false

workflow {
    if( params.input )
        analyze(params.input, params.save_intermeds)
    else
        analyze(fake_input(), params.save_intermeds)
}
```

The default value can be overridden by the command line, params file, or config file. Parameters from multiple sources are resolved in the order described in [Pipeline parameters](/nextflow/cli#pipeline-parameters).

::::::::::::: section
## Outputs[​](#outputs "Direct link to Outputs"){.hash-link aria-label="Direct link to Outputs" translate="no"} {#outputs .anchor .anchorTargetStickyNavbar_Vzrq}

Added in version 25.10

Workflow outputs are available as a preview in Nextflow [24.04](/nextflow/migrations/24-04#workflow-outputs-first-preview), [24.10](/nextflow/migrations/24-10#workflow-outputs-second-preview), and [25.04](/nextflow/migrations/25-04#workflow-outputs-third-preview).

note

Workflow outputs are intended to replace the [publishDir](/nextflow/reference/process#publishdir) directive. See [Migrating to workflow outputs](/nextflow/tutorials/workflow-outputs) for guidance on migrating from `publishDir` to workflow outputs.

A script can define an *output block* to declare the top-level workflow outputs. Each output should be assigned in the `publish` section of the entry workflow. Any channel in the workflow can be assigned to an output, including process and subworkflow outputs.

**Example:**

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process fetch {
    // ...

    output:
    path 'sample.txt'

    // ...
}

workflow {
    main:
    ch_samples = fetch(params.input)

    publish:
    samples = ch_samples
}

output {
    samples {
        path '.'
    }
}
```

In the above example, the output of process `fetch` is assigned to the `samples` workflow output. How this output is published to a directory structure is described in the next section.

::: section
### Publishing files[​](#publishing-files "Direct link to Publishing files"){.hash-link aria-label="Direct link to Publishing files" translate="no"} {#publishing-files .anchor .anchorTargetStickyNavbar_Vzrq}

Each workflow output can define how files are *published* from the work directory to a designated *output directory*.

**Output directory**

You can set the top-level output directory for a run using the `-output-dir` command-line option or the `outputDir` config option:

``` {.prism-code .language-bash .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
nextflow run main.nf -output-dir 'my-results'
```

``` {.prism-code .language-groovy .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
// nextflow.config
outputDir = 'my-results'
```

The default output directory is `results` in the launch directory.

**Publish path**

By default, Nextflow publishes all output files to the output directory. Each workflow output can define where to publish files within the output directory using the `path` directive:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
workflow {
    main:
    ch_step1 = step1()
    ch_step2 = step2(ch_step1)

    publish:
    step1 = ch_step1
    step2 = ch_step2
}

output {
    step1 {
        path 'step1'
    }
    step2 {
        path 'step2'
    }
}
```

The following directory structure is created:

``` {.prism-code .language-text .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
results/
└── step1/
    └── ...
└── step2/
    └── ...
```

Nextflow publishes all files received by an output into the specified directory. Nextflow recursively scans lists, maps, and tuples for nested files:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
workflow {
    main:
    ch_samples = channel.of(
        tuple( [id: 'SAMP1'], [ file('1.txt'), file('2.txt') ] )
    )

    publish:
    samples = ch_samples // 1.txt and 2.txt are published
}
```

note

Files that do not originate from the work directory are not published.

**Dynamic publish path**

The `path` directive can also be a closure which defines a custom publish path for each channel value:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
workflow {
    main:
    ch_samples = channel.of(
        [id: 'SAMP1', fastq_1: file('1.fastq'), fastq_2: file('2.fastq')]
    )

    publish:
    samples = ch_samples
}

output {
    samples {
        path { sample -> "fastq/${sample.id}/" }
    }
}
```

The above example publishes each channel value to a different subdirectory. In this case, each pair of FASTQ files is published into a subdirectory based on the sample ID.

Alternatively, you can define a different path for each individual file using the `>>` operator:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
output {
    samples {
        path { sample ->
            sample.fastq_1 >> "fastq/${sample.id}/"
            sample.fastq_2 >> "fastq/${sample.id}/"
        }
    }
}
```

Each `>>` specifies a *source file* and *publish target*. The source file should be a file or collection of files, and the publish target should be a directory or file name. If the publish target ends with a slash, Nextflow treats it as the directory in which to publish source files.

When using this syntax, only files captured with the `>>` operator are saved to the output directory.

**Conditional publishing**

Outputs can be conditionally published using pipeline parameters:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
output {
    samples {
        path { sample ->
            sample.fastqc >> "fastqc/"
            sample.bam >> (params.save_bams ? "align/" : null)
        }
    }
}
```

In the above example, the BAM files specified by `sample.bam` are published only when `params.save_bams` is `true`.

::: section
### Index files[​](#index-files "Direct link to Index files"){.hash-link aria-label="Direct link to Index files" translate="no"} {#index-files .anchor .anchorTargetStickyNavbar_Vzrq}

Index files are structured metadata files that catalog published outputs and their associated metadata. An index file preserves the structure of channel values, including metadata, which is more robust than encoding this information into file paths. The index file can be a CSV (`.csv`), JSON (`.json`), or YAML (`.yml`, `.yaml`) file. The channel values should be files, lists, maps, or tuples.

Each output can create an index file of its published values:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
workflow {
    main:
    ch_samples = channel.of(
        [id: 1, name: 'sample 1', fastq_1: '1a.fastq', fastq_2: '1b.fastq'],
        [id: 2, name: 'sample 2', fastq_1: '2a.fastq', fastq_2: '2b.fastq'],
        [id: 3, name: 'sample 3', fastq_1: '3a.fastq', fastq_2: null]
    )

    publish:
    samples = ch_samples
}

output {
    samples {
        path 'fastq'
        index {
            path 'samples.csv'
        }
    }
}
```

The above example writes the following CSV file to `results/samples.csv`:

``` {.prism-code .language-text .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
"1","sample 1","results/fastq/1a.fastq","results/fastq/1b.fastq"
"2","sample 2","results/fastq/2a.fastq","results/fastq/2b.fastq"
"3","sample 3","results/fastq/3a.fastq",""
```

You can customize the index file with additional directives, for example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
index {
    path 'samples.csv'
    header true
    sep '|'
}
```

This example produces the following index file:

``` {.prism-code .language-text .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
"id"|"name"|"fastq_1"|"fastq_2"
"1"|"sample 1"|"results/fastq/1a.fastq"|"results/fastq/1b.fastq"
"2"|"sample 2"|"results/fastq/2a.fastq"|"results/fastq/2b.fastq"
"3"|"sample 3"|"results/fastq/3a.fastq"|""
```

note

Files that do not originate from the work directory are not published, but are included in the index file.

See [Output directives](#output-directives) for the list of available index directives.

::: section
### Labels[​](#labels "Direct link to Labels"){.hash-link aria-label="Direct link to Labels" translate="no"} {#labels .anchor .anchorTargetStickyNavbar_Vzrq}

You can apply labels to each workflow output using the `label` directive:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
output {
    multiqc_report {
        label 'qc'
        label 'summary'
    }
}
```

Labels can be used to find and filter output files across workflow runs with data lineage. See [Data lineage](/nextflow/tutorials/data-lineage) for details on how to query output files by label.

::::::::: section
### Output directives[​](#output-directives "Direct link to Output directives"){.hash-link aria-label="Direct link to Output directives" translate="no"} {#output-directives .anchor .anchorTargetStickyNavbar_Vzrq}

The following directives are available for each output in the output block:

:::::: section
##### `index`[​](#index "Direct link to index"){.hash-link aria-label="Direct link to index" translate="no"} {#index .anchor .anchorTargetStickyNavbar_Vzrq}

Create an index file containing a record of each published value.

The following directives are available in an index definition:

::: section
###### `header`[​](#header "Direct link to header"){.hash-link aria-label="Direct link to header" translate="no"} {#header .anchor .anchorTargetStickyNavbar_Vzrq}

When `true`, the keys of the first record are used as the column names (default: `false`). Can also be a list of column names. Only used for CSV files.

::: section
###### `path`[​](#path "Direct link to path"){.hash-link aria-label="Direct link to path" translate="no"} {#path .anchor .anchorTargetStickyNavbar_Vzrq}

The name of the index file relative to the base output directory (required). Can be a CSV, JSON, or YAML file.

::: section
###### `sep`[​](#sep "Direct link to sep"){.hash-link aria-label="Direct link to sep" translate="no"} {#sep .anchor .anchorTargetStickyNavbar_Vzrq}

The character used to separate values (default: `','`). Only used for CSV files.

::: section
##### `label`[​](#label "Direct link to label"){.hash-link aria-label="Direct link to label" translate="no"} {#label .anchor .anchorTargetStickyNavbar_Vzrq}

Attach a label to every file published by this output. Can be specified multiple times to attach multiple labels.

Labels are stored in the `labels` field of `FileOutput` records in the [lineage store](/nextflow/tutorials/data-lineage).

::: section
##### `path`[​](#path-1 "Direct link to path-1"){.hash-link aria-label="Direct link to path-1" translate="no"} {#path-1 .anchor .anchorTargetStickyNavbar_Vzrq}

Specify the publish path relative to the output directory (default: `'.'`). Can be a path, a closure that defines a custom directory for each published value, or a closure that publishes individual files using the `>>` operator.

Additionally, the following options from the [workflow](/nextflow/reference/config/workflow) config scope can be specified as directives:

- `contentType`
- `enabled`
- `ignoreErrors`
- `mode`
- `overwrite`
- `storageClass`
- `tags`

For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
output {
    samples {
        mode 'copy'
    }
}
```
:::::::::
:::::::::::::

:::: section
## Named workflows[​](#named-workflows "Direct link to Named workflows"){.hash-link aria-label="Direct link to Named workflows" translate="no"} {#named-workflows .anchor .anchorTargetStickyNavbar_Vzrq}

A *named workflow* is a workflow that can be called by other workflows:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
workflow my_workflow {
    ch_hello = hello()
    bye( ch_hello.collect() )
}

workflow {
    my_workflow()
}
```

The above example defines a workflow named `my_workflow` which is called by the entry workflow. Both `hello` and `bye` could be any other process or workflow.

::: section
### Takes and emits[​](#takes-and-emits "Direct link to Takes and emits"){.hash-link aria-label="Direct link to Takes and emits" translate="no"} {#takes-and-emits .anchor .anchorTargetStickyNavbar_Vzrq}

The `take:` section declares the inputs of a named workflow:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
workflow my_workflow {
    take:
    data1
    data2

    main:
    ch_hello = hello(data1, data2)
    bye(hello.out)
}
```

Inputs can be specified like arguments when calling the workflow:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
workflow {
    my_workflow( channel.of('/some/data') )
}
```

The `emit:` section declares the outputs of a named workflow:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
workflow my_workflow {
    main:
    ch_hello = hello(data)
    ch_bye = bye(ch_hello)

    emit:
    hello = ch_hello
    bye = ch_bye
}

workflow {
    result = my_workflow()
    result.hello.view()
    result.bye.view()
}
```

The name should be omitted when only one output is declared:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
workflow my_workflow {
    take:
    data

    main:
    ch_bye = bye(hello(data))

    emit:
    ch_bye
}

workflow {
    result = my_workflow()
    result.view()
}
```
::::

::::::::: section
## Dataflow[​](#dataflow "Direct link to Dataflow"){.hash-link aria-label="Direct link to Dataflow" translate="no"} {#dataflow .anchor .anchorTargetStickyNavbar_Vzrq}

Workflows consist of *dataflow* logic, in which processes are connected to each other through *dataflow channels* and *dataflow values*.

::: section
### Channels and values[​](#channels-and-values "Direct link to Channels and values"){.hash-link aria-label="Direct link to Channels and values" translate="no"} {#channels-and-values .anchor .anchorTargetStickyNavbar_Vzrq}

A *dataflow channel* (or simply *channel*) is an asynchronous sequence of values.

The values in a channel cannot be accessed directly, but only through an operator or process. For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of(1, 2, 3).view { v -> "channel emits ${v}" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel emits 1
channel emits 2
channel emits 3
```

A *dataflow value* is an asynchronous value.

Dataflow values can be created using the [channel.value](/nextflow/reference/stdlib-namespaces/channel#value) factory, and they are created by processes (under [certain conditions](/nextflow/process#singleton-outputs)).

A dataflow value cannot be accessed directly, but only through an operator or process. For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.value(1).view { v -> "dataflow value is ${v}" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
dataflow value is 1
```

::: section
### Factories[​](#factories "Direct link to Factories"){.hash-link aria-label="Direct link to Factories" translate="no"} {#factories .anchor .anchorTargetStickyNavbar_Vzrq}

A channel can be created by factories in the `channel` namespace. For example, the `channel.fromPath()` factory creates a channel from a file name or glob pattern, similar to the `files()` function:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.fromPath('input/*.txt').view()
```

See the [`channel` namespace](/nextflow/reference/stdlib-namespaces/channel) for the full list of channel factories.

::: section
### Operators[​](#operators "Direct link to Operators"){.hash-link aria-label="Direct link to Operators" translate="no"} {#operators .anchor .anchorTargetStickyNavbar_Vzrq}

Channel operators, or *operators* for short, are functions that consume and produce channels. Because channels are asynchronous, operators are necessary to manipulate the values in a channel. Operators are particularly useful for implementing glue logic between processes.

Commonly used operators include:

- [collect](/nextflow/reference/operator#collect): collect the channel values into a collection

- [combine](/nextflow/reference/operator#combine): emit the combinations of two channels

- [filter](/nextflow/reference/operator#filter): emit only the channel values that satisfy a condition

- [flatMap](/nextflow/reference/operator#flatmap): emit multiple values for each channel value with a closure

- [grouptuple](/nextflow/reference/operator#grouptuple): group the channel values based on a grouping key

- [join](/nextflow/reference/operator#join): join the values from two channels based on a matching key

- [map](/nextflow/reference/operator#map): transform each channel value with a mapping function

- [mix](/nextflow/reference/operator#mix): emit the values from multiple channels

- [view](/nextflow/reference/operator#view): print each channel value to standard output

See [Operators](/nextflow/reference/operator) for the full set of operators. See [Value\<V\>](/nextflow/reference/stdlib-types/value) for the set of available methods for dataflow values.

::: section
### Calling processes and workflows[​](#calling-processes-and-workflows "Direct link to Calling processes and workflows"){.hash-link aria-label="Direct link to Calling processes and workflows" translate="no"} {#calling-processes-and-workflows .anchor .anchorTargetStickyNavbar_Vzrq}

Processes and workflows are called like functions, passing their inputs as arguments:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process hello {
    output:
    path 'hello.txt', emit: txt

    script:
    """
    your_command > hello.txt
    """
}

process bye {
    input:
    path 'hello.txt'

    output:
    path 'bye.txt', emit: txt

    script:
    """
    another_command hello.txt > bye.txt
    """
}

workflow hello_bye {
    take:
    data

    main:
    hello()
    bye(data)
}

workflow {
    data = channel.fromPath('/some/path/*.txt')
    hello_bye(data)
}
```

Processes and workflows can only be called by workflows. A given process or workflow can only be called once in a given workflow. To use a process or workflow multiple times in the same workflow, [include](/nextflow/reference/syntax#include) it from another script with multiple aliases:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
include { hello_bye as hello_bye1 } from './modules/hello_bye'
include { hello_bye as hello_bye2 } from './modules/hello_bye'

workflow {
    data1 = channel.fromPath('data1/*.txt')
    data2 = channel.fromPath('data2/*.txt')
    hello_bye1(data1)
    hello_bye2(data2)
}
```

The \"return value\" of a process or workflow call is the process outputs or workflow emits, respectively. The return value can be assigned to a variable or passed into another call:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
workflow hello_bye {
    take:
    data

    main:
    bye_out = bye(hello(data))

    emit:
    bye_out
}

workflow {
    data = channel.fromPath('/some/path/*.txt')
    bye_out = hello_bye(data)
}
```

Named outputs can be accessed as properties of the return value:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
workflow hello_bye {
    take:
    data

    main:
    hello_out = hello(data)
    bye_out = bye(hello_out.txt)

    emit:
    hello = hello_out.txt
    bye = bye_out.txt
}

workflow {
    data = channel.fromPath('/some/path/*.txt')
    flow_out = hello_bye(data)
    bye_out = flow_out.bye
}
```

As a convenience, process and workflow outputs can also be accessed without first assigning to a variable, by using the `.out` property of the process or workflow name:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
workflow hello_bye {
    take:
    data

    main:
    hello(data)
    bye(hello.out)

    emit:
    hello = hello.out
    bye = bye.out
}

workflow {
    data = channel.fromPath('/some/path/*.txt')
    hello_bye(data)
    hello_bye.out.bye.view()
}
```

note

Process named outputs are defined using the `emit` option on a process output. See [naming process outputs](/nextflow/process#naming-outputs) for more information.

Workflows can be composed in the same way:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
workflow flow1 {
    take:
    data

    emit:
    tack(tick(data))
}

workflow flow2 {
    take:
    data

    emit:
    tock(tick(data))
}

workflow {
    data = channel.fromPath('/some/path/*.txt')
    flow2(flow1(data))
}
```

The same process can be called in different workflows without using an alias, like `tick` in the above example, which is used in both `flow1` and `flow2`. The workflow call stack determines the *fully qualified process name*, which is used to distinguish the different process calls, i.e. `flow1:tick` and `flow2:tick` in the above example.

tip

The fully qualified process name can be used as a [process selector](/nextflow/config#process-selectors) in a Nextflow configuration file, and it takes priority over the simple process name.

::: section
### Special operators (`|` and `&`)[​](#special-operators--and- "Direct link to special-operators--and-"){.hash-link aria-label="Direct link to special-operators--and-" translate="no"} {#special-operators--and- .anchor .anchorTargetStickyNavbar_Vzrq}

Deprecated in version 26.04

These operators are not supported when [static typing](/nextflow/tutorials/static-types) is enabled. Use standard method calls and assignments instead.

The following operators have a special meaning when used with process and workflow calls in a workflow:

- The `|` *pipe* operator can be used to chain processes, operators, and workflows.
- The `&` *fork* operator can be used to call multiple processes in parallel with the same channel(s).

For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process greet {
    input:
    val data

    output:
    val result

    exec:
    result = "$data world"
}

process to_upper {
    input:
    val data

    output:
    val result

    exec:
    result = data.toUpperCase()
}

workflow {
    channel.of('Hello')
        | map { v -> v.reverse() }
        | (greet & to_upper)
        | mix
        | view
}
```

In the above snippet, the initial channel is piped to the [map](/nextflow/reference/operator#map) operator, which reverses the string value. Then, the result is passed to the processes `greet` and `to_upper`, which are executed in parallel. Each process outputs a channel, and the two channels are combined using the [mix](/nextflow/reference/operator#mix) operator. Finally, the result is printed using the [view](/nextflow/reference/operator#view) operator.

The same code can also be written as:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
workflow {
    ch = channel.of('Hello').map { v -> v.reverse() }
    ch_greet = greet(ch)
    ch_upper = to_upper(ch)
    ch_greet.mix(ch_upper).view()
}
```

::: section
### Process and workflow recursion[​](#process-and-workflow-recursion "Direct link to Process and workflow recursion"){.hash-link aria-label="Direct link to Process and workflow recursion" translate="no"} {#process-and-workflow-recursion .anchor .anchorTargetStickyNavbar_Vzrq}

Added in version 22.04

note

This is a preview feature and requires the `nextflow.preview.recursion` feature flag to be enabled. The syntax and behavior may change in future releases.

Processes can be invoked recursively using the `recurse` method.

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
nextflow.preview.recursion = true

params.start = 10

workflow {
    count_down
        .recurse(params.start)
        .until { v -> v == 0 }
        .view { v -> "${v}..." }
}

process count_down {
    input:
    val v

    output:
    val v

    exec:
    sleep(1000)
    v = v - 1
}
```

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
9...
8...
7...
6...
5...
4...
3...
2...
1...
0...
```

In the above example, the `count_down` process is first invoked with the value `params.start`. On each subsequent iteration, the process is invoked again using the output from the previous iteration. The recursion continues until the specified condition is satisfied, as defined by the `until` method, which terminates the recursion.

The recursive output can also be limited using the `times` method:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
count_down
    .recurse(params.start)
    .times(3)
    .view { v -> "${v}..." }
```

Workflows can also be invoked recursively:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
nextflow.preview.recursion = true

params.input = "recurse-workflow.in"

workflow {
    clock
        .recurse(file(params.input))
        .until { file -> file.size() > 64 }
        .view { file -> file.text }
}

workflow clock {
    take:
    logfile

    emit:
    tock(tick(logfile))
}

process tick {
    input:
    path 'input.txt'

    output:
    path 'result.txt'

    script:
    """
    cat input.txt > result.txt
    echo "Task ${task.index} : tick" >> result.txt
    """
}

process tock {
    input:
    path 'input.txt'

    output:
    path 'result.txt'

    script:
    """
    cat input.txt > result.txt
    echo "Task ${task.index} : tock" >> result.txt
    """
}
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
hello
Task 1 : tick
Task 1 : tock

hello
Task 1 : tick
Task 1 : tock
Task 2 : tick
Task 2 : tock

hello
Task 1 : tick
Task 1 : tock
Task 2 : tick
Task 2 : tock
Task 3 : tick
Task 3 : tock
```

**Limitations**

- A recursive process or workflow must have matching inputs and outputs, such that the outputs for each iteration can be supplied as the inputs for the next iteration.

- Recursive workflows cannot use *reduction* operators such as `collect`, `reduce`, and `toList`, because these operators cause the recursion to hang indefinitely after the initial iteration.
:::::::::

:::: {.col .col--3}
::: {.text-[.7rem] .mb-1 .uppercase .text-primary .font-semibold}
On this Page

- [Entry workflow](#entry-workflow){.table-of-contents__link .toc-highlight}
- [Parameters](#parameters){.table-of-contents__link .toc-highlight}
- [Outputs](#outputs){.table-of-contents__link .toc-highlight}
  - [Publishing files](#publishing-files){.table-of-contents__link .toc-highlight}
  - [Index files](#index-files){.table-of-contents__link .toc-highlight}
  - [Labels](#labels){.table-of-contents__link .toc-highlight}
  - [Output directives](#output-directives){.table-of-contents__link .toc-highlight}
- [Named workflows](#named-workflows){.table-of-contents__link .toc-highlight}
  - [Takes and emits](#takes-and-emits){.table-of-contents__link .toc-highlight}
- [Dataflow](#dataflow){.table-of-contents__link .toc-highlight}
  - [Channels and values](#channels-and-values){.table-of-contents__link .toc-highlight}
  - [Factories](#factories){.table-of-contents__link .toc-highlight}
  - [Operators](#operators){.table-of-contents__link .toc-highlight}
  - [Calling processes and workflows](#calling-processes-and-workflows){.table-of-contents__link .toc-highlight}
  - [Special operators (`|` and `&`)](#special-operators--and-){.table-of-contents__link .toc-highlight}
  - [Process and workflow recursion](#process-and-workflow-recursion){.table-of-contents__link .toc-highlight}
::::