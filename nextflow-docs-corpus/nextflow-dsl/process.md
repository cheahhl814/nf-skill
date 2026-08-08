# Processes

In Nextflow, a **process** is a specialized function for executing scripts in a scalable and portable manner.

Here is an example process definition:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process hello {
    output:
    path 'hello.txt'

    script:
    """
    echo 'Hello world!' > hello.txt
    """
}
```

See [Process](/nextflow/reference/syntax#process) for a full description of the process syntax.

note

This page describes the original process syntax. Nextflow also supports a [typed process syntax](/nextflow/process-typed) with statically typed inputs and outputs. See [Static typing](/nextflow/static-typing) for an overview.

:::::::: section
## Script[​](#script "Direct link to Script"){.hash-link aria-label="Direct link to Script" translate="no"} {#script .anchor .anchorTargetStickyNavbar_Vzrq}

The `script` section defines, as a string expression, the script that is executed by the process.

A process may contain only one script, and if the `script` guard is not explicitly declared, the script must be the final statement in the process definition.

The script string is executed as a [Bash](http://en.wikipedia.org/wiki/Bash_(Unix_shell)){target="_blank" rel="noopener noreferrer"} script in the host environment. It can be any command or script that you would normally execute on the command line or in a Bash script. Naturally, the script may only use commands that are available in the host environment.

The script section can be a simple string or a multi-line string. The latter approach makes it easier to write scripts with multiple commands spanning multiple lines. For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process blast {
  """
  blastp -db $db -query query.fa -outfmt 6 > blast_result
  cat blast_result | head -n 10 | cut -f 2 > top_hits
  blastdbcmd -db $db -entry_batch top_hits > sequences
  """
}
```

As explained in the script tutorial section, strings can be defined using single-quotes or double-quotes, and multi-line strings are defined by three single-quote or three double-quote characters.

There is a subtle but important difference between them. Like in Bash, strings delimited by a `"` character support variable substitutions, while strings delimited by `'` do not.

In the above code fragment, the `$db` variable is replaced by the actual value defined elsewhere in the pipeline script.

warning

Since Nextflow uses the same Bash syntax for variable substitutions in strings, you must manage them carefully depending on whether you want to evaluate a *Nextflow* variable or a *Bash* variable.

When you need to access a system environment variable in your script, you have two options.

If you don\'t need to access any Nextflow variables, you can define your script section with single-quotes:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process echo_path {
  '''
  echo "The path is: $PATH"
  '''
}
```

Otherwise, you can define your script with double-quotes and escape the system environment variables by prefixing them with a back-slash `\` character, as shown in the following example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process blast {
  """
  blastp -db \$DB -query query.fa -outfmt 6 > blast_result
  cat blast_result | head -n $MAX | cut -f 2 > top_hits
  blastdbcmd -db \$DB -entry_batch top_hits > sequences
  """
}
```

In this example, `$MAX` is a Nextflow variable that must be defined elsewhere in the pipeline script. Nextflow replaces it with the actual value before executing the script. Meanwhile, `$DB` is a Bash variable that must exist in the execution environment, and Bash will replace it with the actual value during execution.

::: section
### Scripts *à la carte*[​](#scripts-à-la-carte "Direct link to scripts-à-la-carte"){.hash-link aria-label="Direct link to scripts-à-la-carte" translate="no"} {#scripts-à-la-carte .anchor .anchorTargetStickyNavbar_Vzrq}

The process script is interpreted by Nextflow as a Bash script by default, but you are not limited to Bash.

You can use your favourite scripting language (Perl, Python, R, etc), or even mix them in the same pipeline.

A pipeline may be composed of processes that execute very different tasks. With Nextflow, you can choose the scripting language that best fits the task performed by a given process. For example, for some processes R might be more useful than Perl, whereas for others you may need to use Python because it provides better access to a library or an API, etc.

To use a language other than Bash, simply start your process script with the corresponding [shebang](http://en.wikipedia.org/wiki/Shebang_(Unix)){target="_blank" rel="noopener noreferrer"}. For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process perl_task {
    """
    #!/usr/bin/perl

    print 'Hi there!' . '\n';
    """
}

process python_task {
    """
    #!/usr/bin/python

    x = 'Hello'
    y = 'world!'
    print "%s - %s" % (x,y)
    """
}

workflow {
    perl_task()
    python_task()
}
```

tip

Since the actual location of the interpreter binary file can differ across platforms, it is wise to use the `env` command followed by the interpreter name, e.g. `#!/usr/bin/env perl`, instead of the absolute path, in order to make your script more portable.

::: section
### Conditional scripts[​](#conditional-scripts "Direct link to Conditional scripts"){.hash-link aria-label="Direct link to Conditional scripts" translate="no"} {#conditional-scripts .anchor .anchorTargetStickyNavbar_Vzrq}

The `script` section is like a function that returns a string. This means that you can write arbitrary code to determine the script, as long as the final statement is a string.

If-else statements based on task inputs can be used to produce a different script. For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
mode = 'tcoffee'

process align {
    input:
    path sequences

    script:
    if( mode == 'tcoffee' )
        """
        t_coffee -in $sequences > out_file
        """

    else if( mode == 'mafft' )
        """
        mafft --anysymbol --parttree --quiet $sequences > out_file
        """

    else if( mode == 'clustalo' )
        """
        clustalo -i $sequences -o out_file
        """

    else
        error "Invalid alignment mode: ${mode}"
}
```

In the above example, the process will execute one of several scripts depending on the value of the `mode` parameter. By default it will execute the `tcoffee` command.

::: section
### Template[​](#template "Direct link to Template"){.hash-link aria-label="Direct link to Template" translate="no"} {#template .anchor .anchorTargetStickyNavbar_Vzrq}

Process scripts can be externalized to **template** files, which allows them to be reused across different processes and tested independently from the pipeline execution.

A template can be used in place of an embedded script using the `template` function in the script section:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process hello {
    input:
    val STR

    output:
    stdout

    script:
    template 'hello.sh'
}

workflow {
    hello('Hello!').view()
}
```

By default, Nextflow looks for the template script in the `templates` directory located alongside the Nextflow script in which the process is defined. An absolute path can be used to specify a different location. However, this practice is discouraged because it hinders pipeline portability.

An example template script is provided below:

``` {.prism-code .language-bash .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
#!/bin/bash
echo "process started at `date`"
echo $STR
echo "process completed"
```

Variables prefixed with the dollar character (`$`) are interpreted as Nextflow variables when the template script is executed by Nextflow and Bash variables when executed directly. For example, the above script can be executed from the command line by providing each input as an environment variable:

``` {.prism-code .language-bash .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
STR='Hello!' bash templates/hello.sh
```

The following caveats should be considered:

- Template scripts are recommended only for Bash scripts. Languages that do not prefix variables with `$` (e.g. Python and R) can\'t be executed directly as a template script.

- Variables escaped with `\$` will be interpreted as Bash variables when executed by Nextflow, but will not be interpreted as variables when executed from the command line. This practice should be avoided to ensure that the template script behaves consistently.

- Template variables are evaluated even if they are commented out in the template script. If a template variable is missing, it will cause the pipeline to fail regardless of where it occurs in the template.

tip

Template scripts are generally discouraged due to the caveats described above. The best practice for using a custom script is to embed it in the process definition at first and move it to a separate file with its own command line interface once the code matures.

::: section
### Shell[​](#shell "Direct link to Shell"){.hash-link aria-label="Direct link to Shell" translate="no"} {#shell .anchor .anchorTargetStickyNavbar_Vzrq}

Deprecated in version 25.04

Use the `script` section instead.

The `shell` section is a string expression that defines the script that is executed by the process. It is an alternative to the [Script](/nextflow/process#script) definition with one important difference: it uses the exclamation mark `!` character, instead of the usual dollar `$` character, to denote Nextflow variables.

This way, it is possible to use both Nextflow and Bash variables in the same script without having to escape the latter, which makes process scripts easier to read and maintain. For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process hello {
    input:
    val str

    shell:
    '''
    echo "User $USER says !{str}"
    '''
}

workflow {
    channel.of('Hello', 'Hola', 'Bonjour') | hello
}
```

In the above example, `$USER` is treated as a Bash variable, while `!{str}` is treated as a Nextflow variable.

note

- Shell script definitions require the use of single-quote `'` delimited strings. When using double-quote `"` delimited strings, dollar variables are interpreted as Nextflow variables as usual. See [String interpolation](/nextflow/script#string-interpolation) for more information.
- Variables prefixed with `!` must always be enclosed in curly brackets, i.e. `!{str}` is a valid variable whereas `!str` is ignored.
- Shell scripts support the use of the [Template](/nextflow/process#template) mechanism. The same rules are applied to the variables defined in the template script.

::: section
### Native execution[​](#native-execution "Direct link to Native execution"){.hash-link aria-label="Direct link to Native execution" translate="no"} {#native-execution .anchor .anchorTargetStickyNavbar_Vzrq}

The `exec` section executes the given code without launching a job.

For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process hello {
    input:
    val name

    exec:
    println "Hello Mr. $name"
}

workflow {
    channel.of('a', 'b', 'c') | hello
}
```

will display:

``` {.prism-code .language-text .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
Hello Mr. b
Hello Mr. a
Hello Mr. c
```

A native process is very similar to a [function](/nextflow/reference/syntax#function). However, it provides additional capabilities such as parallelism, caching, and progress logging.
::::::::

::: section
## Stub[​](#stub "Direct link to Stub"){.hash-link aria-label="Direct link to Stub" translate="no"} {#stub .anchor .anchorTargetStickyNavbar_Vzrq}

You can define a command *stub*, which replaces the actual process command when the `-stub-run` or `-stub` command-line option is enabled:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process salmon_index {
    input:
    path transcriptome

    output:
    path 'index'

    script:
    """
    salmon index --threads $task.cpus -t $transcriptome -i index
    """

    stub:
    """
    mkdir index
    touch index/seq.bin
    touch index/info.json
    touch index/refseq.bin
    """
}
```

The `stub` section can be defined before or after the `script` section. When the pipeline is executed with the `-stub-run` option and a process\'s `stub` is not defined, the `script` section is executed.

This feature makes it easier to quickly prototype the workflow logic without using the real commands. The developer can use it to provide a dummy script that mimics the execution of the real one in a quicker manner. In other words, it is a way to perform a dry-run.

:::: section
## Inputs[​](#inputs "Direct link to Inputs"){.hash-link aria-label="Direct link to Inputs" translate="no"} {#inputs .anchor .anchorTargetStickyNavbar_Vzrq}

The `input` section defines the input of a process, similar to function arguments. A process may have at most one input section, which must contain at least one input declaration.

The input section follows the syntax shown below:

``` {.prism-code .language-text .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
input:
  <input qualifier> <input name>
```

An input declaration consists of a *qualifier* and a *name*. The input qualifier defines the type of data to be received. This information is used by Nextflow to apply the semantic rules associated with each qualifier, and handle it properly depending on the target execution platform (grid, cloud, etc).

When a process is invoked in a workflow, it must be provided a channel or dataflow value for each input in the process input section, similar to calling a function with specific arguments. The examples provided in the following sections demonstrate how a process is invoked.

The following input qualifiers are available:

- `val`: Access the input value by name in the process script.
- `path`: Handle the input value as a path, staging the file properly in the execution context.
- `env`: Use the input value to set an environment variable in the process script.
- `stdin`: Forward the input value to the process `stdin` special file.
- `tuple`: Handle a group of input values having any of the above qualifiers.
- `each`: Execute the process for each element in the input collection.

See [process reference](/nextflow/reference/process#inputs) for the full list of input methods and options.

::: section
### Input variables (`val`)[​](#input-variables-val "Direct link to input-variables-val"){.hash-link aria-label="Direct link to input-variables-val" translate="no"} {#input-variables-val .anchor .anchorTargetStickyNavbar_Vzrq}

The `val` qualifier accepts any data type. It can be accessed in the process script by using the specified input name, as shown in the following example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process echo {
  input:
  val x

  script:
  """
  echo "process job $x"
  """
}

workflow {
  def num = channel.of(1,2,3)
  echo(num)
}
```

In the above example, the process is executed three times: once for each value emitted by the `num` channel. The resulting output is similar to the one shown below:

``` {.prism-code .language-text .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process job 3
process job 1
process job 2
```

note

Processes do not necessarily process items in the order that they are received. In the above example, the value `3` was processed before the others.

note

When the process declares exactly one input, the pipe `|` operator can be used to provide inputs to the process, instead of passing it as a parameter. Both methods have identical semantics:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
process echo {
  input:
  val x

  script:
  """
  echo "process job $x"
  """
}

workflow {
  channel.of(1,2,3) | echo
}
```
::::