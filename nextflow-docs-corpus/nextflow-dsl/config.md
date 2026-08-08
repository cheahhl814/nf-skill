# Configuration

Nextflow configuration lets you control how your pipeline runs without modifying the pipeline code. You can set execution parameters, define resource requirements, configure executors and containers, and create profiles for different environments.

::: section
## Configuration files[​](#configuration-files "Direct link to Configuration files"){.hash-link aria-label="Direct link to Configuration files" translate="no"} {#configuration-files .anchor .anchorTargetStickyNavbar_Vzrq}

When you launch a pipeline script, Nextflow detects configuration files from multiple sources and applies them in the following order (from lowest to highest priority):

1.  `$NXF_HOME/config` (defaults to `$HOME/.nextflow/config`)
2.  `nextflow.config` in the project directory
3.  `nextflow.config` in the launch directory
4.  Config files specified with `-c <config-files>`

tip

You can use the `-C <config-file>` option to specify a fixed set of configuration files and ignore all other files.

:::::: section
## Syntax[​](#syntax "Direct link to Syntax"){.hash-link aria-label="Direct link to Syntax" translate="no"} {#syntax .anchor .anchorTargetStickyNavbar_Vzrq}

Nextflow configuration uses a similar syntax as Nextflow scripts. Configuration options must be set declaratively, but the value of a config option can be an arbitrary expression.

A config file may contain any number of [assignments](#assignments), [blocks](#blocks), and [includes](#includes). You can add comments just like in scripts.

::: section
### Assignments[​](#assignments "Direct link to Assignments"){.hash-link aria-label="Direct link to Assignments" translate="no"} {#assignments .anchor .anchorTargetStickyNavbar_Vzrq}

A config assignment sets a config option to a value:

``` {.prism-code .language-groovy .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
workDir = 'work'
docker.enabled = true
process.maxErrors = 10
```

The config option consists of an *option name* prefixed by any number of *scopes*. Scopes group related config options. See [Configuration options](/nextflow/reference/config) for the full set of config options.

The value is typically a literal value, such as a number, boolean, or string. However, you can use any [expression](/nextflow/reference/syntax#expressions):

``` {.prism-code .language-groovy .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
params.helper_file = "${projectDir}/assets/helper.txt"
```

::: section
### Blocks[​](#blocks "Direct link to Blocks"){.hash-link aria-label="Direct link to Blocks" translate="no"} {#blocks .anchor .anchorTargetStickyNavbar_Vzrq}

You can specify config options in the same scope as a block:

``` {.prism-code .language-groovy .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
// dot syntax
docker.enabled = true
docker.runOptions = '-u $(id -u):$(id -g)'

// block syntax
docker {
    enabled = true
    runOptions = '-u $(id -u):$(id -g)'
}
```

As a result, deeply nested config options can be assigned in multiple ways. The following three assignments are equivalent:

``` {.prism-code .language-groovy .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
executor.retry.maxAttempt = 5

executor {
    retry.maxAttempt = 5
}

executor {
    retry {
        maxAttempt = 5
    }
}
```

::: section
### Includes[​](#includes "Direct link to Includes"){.hash-link aria-label="Direct link to Includes" translate="no"} {#includes .anchor .anchorTargetStickyNavbar_Vzrq}

You can include configuration files in other configuration files with the `includeConfig` keyword:

``` {.prism-code .language-groovy .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process.executor = 'sge'
process.queue = 'long'
process.memory = '10G'

includeConfig 'path/extra.config'
```

Relative paths are resolved against the location of the including file.

note

Config files should only be included at the top level or in a [profile](#config-profiles). This ensures the included config file is valid on its own and in the context in which it is included.

:::::::: section
## Constants and functions[​](#constants-and-functions "Direct link to Constants and functions"){.hash-link aria-label="Direct link to Constants and functions" translate="no"} {#constants-and-functions .anchor .anchorTargetStickyNavbar_Vzrq}

The following constants are globally available in a Nextflow configuration file:

::: section
##### `launchDir: Path`[​](#launchdir-path "Direct link to launchdir-path"){.hash-link aria-label="Direct link to launchdir-path" translate="no"} {#launchdir-path .anchor .anchorTargetStickyNavbar_Vzrq}

The directory where the workflow was launched.

::: section
##### `projectDir: Path`[​](#projectdir-path "Direct link to projectdir-path"){.hash-link aria-label="Direct link to projectdir-path" translate="no"} {#projectdir-path .anchor .anchorTargetStickyNavbar_Vzrq}

The directory where the main script is located.

::: section
##### `workDir: Path`[​](#workdir-path "Direct link to workdir-path"){.hash-link aria-label="Direct link to workdir-path" translate="no"} {#workdir-path .anchor .anchorTargetStickyNavbar_Vzrq}

The directory where the workflow work directory is located.

::: section
##### `secrets: Map<String,String>`[​](#secrets-mapstringstring "Direct link to secrets-mapstringstring"){.hash-link aria-label="Direct link to secrets-mapstringstring" translate="no"} {#secrets-mapstringstring .anchor .anchorTargetStickyNavbar_Vzrq}

Map of pipeline secrets. See [Secrets](/nextflow/secrets) for more information.

The following functions are globally available in a Nextflow configuration file:

::: section
##### `env( name: String ) -> String`[​](#env-name-string----string "Direct link to env-name-string----string"){.hash-link aria-label="Direct link to env-name-string----string" translate="no"} {#env-name-string----string .anchor .anchorTargetStickyNavbar_Vzrq}

Added in version 25.04

Get the value of the environment variable with the specified name in the Nextflow launch environment.
::::::::

::: section
## Parameters[​](#parameters "Direct link to Parameters"){.hash-link aria-label="Direct link to Parameters" translate="no"} {#parameters .anchor .anchorTargetStickyNavbar_Vzrq}

You can define pipeline parameters in config files using the `params` scope:

``` {.prism-code .language-groovy .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
// dot syntax
params.max_cpus = 64
params.publish_mode = 'copy'

// block syntax
params {
    max_cpus = 64
    publish_mode = 'copy'
}
```

note

When including a config file, the included config is evaluated with the parameters defined before the include. The included config cannot see parameters defined after the include.

You should declare parameters in the config file only when other config options use them. When you use a parameter in the script, you should declare it there and override it in config profiles as needed:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
// main.nf
params.input = null
```

``` {.prism-code .language-groovy .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
// nextflow.config
params {
    publish_mode = 'copy'
}

workflow.output.mode = params.publish_mode

profiles {
    test {
        params.input = "${projectDir}/test/input.txt"
    }
}
```

See [Pipeline parameters](/nextflow/cli#pipeline-parameters) for information.

:::::: section
## Process configuration[​](#process-configuration "Direct link to Process configuration"){.hash-link aria-label="Direct link to Process configuration" translate="no"} {#process-configuration .anchor .anchorTargetStickyNavbar_Vzrq}

You can use the `process` scope to specify [process directives](/nextflow/reference/process) separately from the pipeline code.

For example:

``` {.prism-code .language-groovy .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process {
    executor = 'sge'
    queue = 'long'
    clusterOptions = '-pe smp 10 -l virtual_free=64G,h_rt=30:00:00'
}
```

This configuration executes all processes using the SGE executor with the given settings.

::: section
### Process selectors[​](#process-selectors "Direct link to Process selectors"){.hash-link aria-label="Direct link to Process selectors" translate="no"} {#process-selectors .anchor .anchorTargetStickyNavbar_Vzrq}

You can use the `withLabel` selector to configure all processes annotated with a [label](/nextflow/reference/process#label):

``` {.prism-code .language-groovy .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process {
    withLabel: big_mem {
        cpus = 16
        memory = 64.GB
        queue = 'long'
    }
}
```

This configuration assigns 16 CPUs, 64 GB of memory, and the `long` queue to all processes with the `big_mem` label.

You can use the `withName` selector to configure a specific process by its name:

``` {.prism-code .language-groovy .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process {
    withName: hello {
        cpus = 4
        memory = 8.GB
        queue = 'short'
    }
}
```

When the selector is an unqualified process name, it matches both processes *defined* with that name and processes *included* under that alias. Selectors for an alias take priority over selectors for the original name. For example, if you define a process as `hello` and include it as `sayHello`, both `withName: hello` and `withName: sayHello` apply, with `withName: sayHello` taking priority.

When the selector is a qualified process name, it matches only processes invoked with the exact fully-qualified name. For example, `withName: 'PARENT:hello'` matches `hello` invoked by `PARENT`. It does not match:

- `GRANDPARENT:PARENT:hello` (nested invocation)
- `PARENT:sayHello` (different process alias)
- `PARENT2:hello` (different workflow alias)

In each case, use the fully-qualified process name instead. Alternatively, use a regular expression (e.g. `.*:PARENT:hello`) to match multiple variants.

tip

You don\'t need to enclose label and process names in quotes unless they contain special characters (`-`, `:`, `!`, etc.) or are keywords or built-in type identifiers.

::: section
### Selector expressions[​](#selector-expressions "Direct link to Selector expressions"){.hash-link aria-label="Direct link to Selector expressions" translate="no"} {#selector-expressions .anchor .anchorTargetStickyNavbar_Vzrq}

You can use regular expressions in process selectors to apply the same configuration to all processes matching the pattern.

Use the `|` operator to select multiple names or labels:

``` {.prism-code .language-groovy .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process {
    withLabel: 'hello|bye' {
        cpus = 2
        memory = 4.GB
    }
}
```

This configuration requests 2 CPUs and 4 GB of memory for processes labeled as `hello` or `bye`.

Use the `!` operator to negate a selector expression:

``` {.prism-code .language-groovy .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process {
    withLabel: 'hello' { cpus = 2 }
    withLabel: '!hello' { cpus = 4 }
    withName: '!align.*' { queue = 'long' }
}
```

This configuration requests 2 CPUs for processes labeled as `hello` and 4 CPUs for all processes *not* labeled as `hello`. It also specifies the `long` queue for processes whose name does *not* start with `align`.

::: section
### Selector priority[​](#selector-priority "Direct link to Selector priority"){.hash-link aria-label="Direct link to Selector priority" translate="no"} {#selector-priority .anchor .anchorTargetStickyNavbar_Vzrq}

Nextflow applies process configuration settings in the following order (from lowest to highest priority):

1.  Process configuration settings (without a selector)
2.  Process directives in the process definition
3.  `withLabel` selectors matching any of the process labels
4.  `withName` selectors matching the process name
5.  `withName` selectors matching the process included alias
6.  `withName` selectors matching the process fully qualified name

For example:

``` {.prism-code .language-groovy .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process {
    cpus = 4
    withLabel: hello { cpus = 8 }
    withName: bye { cpus = 16 }
    withName: 'aloha:bye' { cpus = 32 }
}
```

This configuration:

- Sets 4 CPUs for all processes (unless otherwise specified in their process definition)
- Sets 8 CPUs for processes annotated with the `hello` label
- Sets 16 CPUs for any process named `bye` (or imported as `bye`)
- Sets 32 CPUs for any process named `bye` (or imported as `bye`) invoked by a workflow named `aloha`

::: section
## Config profiles[​](#config-profiles "Direct link to Config profiles"){.hash-link aria-label="Direct link to Config profiles" translate="no"} {#config-profiles .anchor .anchorTargetStickyNavbar_Vzrq}

Configuration files can define one or more *profiles*. A profile is a set of configuration settings that can be selected at runtime using the `-profile` command line option.

Configuration profiles are defined in the `profiles` scope. For example:

``` {.prism-code .language-groovy .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
profiles {
    standard {
        process.executor = 'local'
    }

    cluster {
        process.executor = 'sge'
        process.queue = 'long'
        process.memory = '10GB'
    }

    cloud {
        process.executor = 'cirrus'
        process.container = 'cbcrg/imagex'
        docker.enabled = true
    }
}
```

This configuration defines three profiles: `standard`, `cluster`, and `cloud`. Each profile provides a different configuration for a given execution environment. When you do not specify a profile, Nextflow uses the `standard` profile by default.

You can enable configuration profiles at runtime as a comma-separated list:

``` {.prism-code .language-bash .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
nextflow run main.nf -profile standard,cloud
```

Nextflow applies config profiles in the order in which they were defined in the config, regardless of the order you specify them on the command line.

Added in version 25.04

When using the [strict parser](/nextflow/strict-syntax), Nextflow applies profiles in the order you specify them on the command line.

::: section
## Workflow handlers[​](#workflow-handlers "Direct link to Workflow handlers"){.hash-link aria-label="Direct link to Workflow handlers" translate="no"} {#workflow-handlers .anchor .anchorTargetStickyNavbar_Vzrq}

Use a trace observer in a plugin to add custom workflow handlers to a pipeline via configuration.

You can define workflow event handlers in the config file:

``` {.prism-code .language-groovy .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
workflow.onComplete = {
    println "Pipeline complete"
    println "Command line: $workflow.commandLine"
}

workflow.onError = {
    println "Error: something went wrong"
}
```

This approach is useful for handling workflow events without modifying the pipeline code. See [Workflow handlers](/nextflow/notifications#workflow-handlers) for more information.

:::: {.col .col--3}
::: {.text-[.7rem] .mb-1 .uppercase .text-primary .font-semibold}
On this Page

- [Configuration files](#configuration-files){.table-of-contents__link .toc-highlight}
- [Syntax](#syntax){.table-of-contents__link .toc-highlight}
  - [Assignments](#assignments){.table-of-contents__link .toc-highlight}
  - [Blocks](#blocks){.table-of-contents__link .toc-highlight}
  - [Includes](#includes){.table-of-contents__link .toc-highlight}
- [Constants and functions](#constants-and-functions){.table-of-contents__link .toc-highlight}
- [Parameters](#parameters){.table-of-contents__link .toc-highlight}
- [Process configuration](#process-configuration){.table-of-contents__link .toc-highlight}
  - [Process selectors](#process-selectors){.table-of-contents__link .toc-highlight}
  - [Selector expressions](#selector-expressions){.table-of-contents__link .toc-highlight}
  - [Selector priority](#selector-priority){.table-of-contents__link .toc-highlight}
- [Config profiles](#config-profiles){.table-of-contents__link .toc-highlight}
- [Workflow handlers](#workflow-handlers){.table-of-contents__link .toc-highlight}
::::