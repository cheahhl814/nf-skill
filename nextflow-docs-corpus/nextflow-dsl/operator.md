# Operators (legacy)

Added in version 26.04

See [Operators](/nextflow/reference/stdlib-types/channel) for the core operator reference. See [Using operators with static typing](/nextflow/tutorials/static-types-operators) for the migration guide.

::: section
## branch[​](#branch "Direct link to branch"){.hash-link aria-label="Direct link to branch" translate="no"} {#branch .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: multiple channels*

The `branch` operator forwards each item from a source channel to one of multiple output channels, based on a selection criteria.

The selection criteria is a [closure](/nextflow/script#closures) that defines, for each output channel, a unique label followed by a boolean expression. When an item is received, it is routed to the first output channel whose expression evaluates to `true`. For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of(1, 2, 3, 40, 50)
    .branch { v ->
        small: v < 10
        large: v > 10
    }
    .set { result }

result.small.view { v -> "$v is small" }
result.large.view { v -> "$v is large" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
1 is small
2 is small
3 is small
40 is large
50 is large
```

note

The above output may be printed in any order since the two `view` operations are executed asynchronously.

A fallback condition can be specified using `true` as the last branch condition:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of(1, 2, 3, 40, 50)
    .branch { v ->
        small: v < 10
        large: v < 50
        other: true
    }
    .set { result }

result.small.view { v -> "$v is small" }
result.large.view { v -> "$v is large" }
result.other.view { v -> "$v is other" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
1 is small
2 is small
3 is small
40 is large
50 is other
```

The value emitted to each branch can be customized with an expression statement (or statements) after the branch condition:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of(1, 2, 3, 40, 50)
    .branch { v ->
        alpha: v < 10
            return v + 2

        beta: v < 50
            return v - 2

        other: true
            return 0
    }
    .set { result }

result.alpha.view { v -> "$v is alpha" }
result.beta.view { v -> "$v is beta" }
result.other.view { v -> "$v is other" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
3 is alpha
4 is alpha
5 is alpha
38 is beta
0 is other
```

tip

When the `return` keyword is omitted, the value of the last expression statement is implicitly returned.

The `branchCriteria()` method can be used to create a branch criteria as a variable that can be passed as an argument to any number of `branch` operations, as shown below:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
def criteria = branchCriteria { v ->
    small: v < 10
    large: v > 10
}

channel.of(1, 2, 30).branch(criteria).set { ch1 }
channel.of(10, 20, 3).branch(criteria).set { ch2 }

ch1.small.view { v -> "$v is small" }
ch1.large.view { v -> "$v is large" }
ch2.small.view { v -> "$v is small" }
ch2.large.view { v -> "$v is large" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
1 is small
2 is small
3 is small
20 is large
30 is large
```

::::::: section
## buffer[​](#buffer "Direct link to buffer"){.hash-link aria-label="Direct link to buffer" translate="no"} {#buffer .anchor .anchorTargetStickyNavbar_Vzrq}

warning

This operator depends on the ordering of values in the source channel. It can lead to [non-deterministic behavior](/nextflow/cache-and-resume#non-deterministic-process-inputs) if used improperly.

*Returns: queue channel*

The `buffer` operator collects items from a source channel into subsets and emits each subset separately.

This operator has multiple variants:

::: section
##### `buffer( closingCondition )`[​](#buffer-closingcondition- "Direct link to buffer-closingcondition-"){.hash-link aria-label="Direct link to buffer-closingcondition-" translate="no"} {#buffer-closingcondition- .anchor .anchorTargetStickyNavbar_Vzrq}

Emits each subset when `closingCondition` is satisfied. The closing condition can be a literal value, a [regular expression](/nextflow/script#regular-expressions), a type qualifier (i.e. Java class), or a boolean predicate. For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 1, 2, 3, 1, 2, 3 )
    .buffer { v -> v == 2 }
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
[1, 2]
[3, 1, 2]
```

::: section
##### `buffer( openingCondition, closingCondition )`[​](#buffer-openingcondition-closingcondition- "Direct link to buffer-openingcondition-closingcondition-"){.hash-link aria-label="Direct link to buffer-openingcondition-closingcondition-" translate="no"} {#buffer-openingcondition-closingcondition- .anchor .anchorTargetStickyNavbar_Vzrq}

Creates a new subset when `openingCondition` is satisfied and emits the subset when is `closingCondition` is satisfied. The opening and closing conditions can each be a literal value, a [regular expression](/nextflow/script#regular-expressions), a type qualifier (i.e. Java class), or a boolean predicate. For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
// emits bundles starting with `2` and ending with `4`
channel.of( 1, 2, 3, 4, 5, 1, 2, 3, 4, 5, 1, 2 )
    .buffer( 2, 4 )
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
[2, 3, 4]
[2, 3, 4]
```

::: section
##### `buffer( size: n, remainder: true | false )`[​](#buffer-size-n-remainder-true--false- "Direct link to buffer-size-n-remainder-true--false-"){.hash-link aria-label="Direct link to buffer-size-n-remainder-true--false-" translate="no"} {#buffer-size-n-remainder-true--false- .anchor .anchorTargetStickyNavbar_Vzrq}

Emits a new subset for every `n` items. Remaining items are discarded. For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 1, 2, 3, 1, 2, 3, 1 )
    .buffer( size: 2 )
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
[1, 2]
[3, 1]
[2, 3]
```

The `remainder` option can be used to emit any remaining items as a partial subset:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 1, 2, 3, 1, 2, 3, 1 )
    .buffer( size: 2, remainder: true )
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
[1, 2]
[3, 1]
[2, 3]
[1]
```

::: section
##### `buffer( size: n, skip: m, remainder: true | false )`[​](#buffer-size-n-skip-m-remainder-true--false- "Direct link to buffer-size-n-skip-m-remainder-true--false-"){.hash-link aria-label="Direct link to buffer-size-n-skip-m-remainder-true--false-" translate="no"} {#buffer-size-n-skip-m-remainder-true--false- .anchor .anchorTargetStickyNavbar_Vzrq}

Emits a new subset for every `n` items, skipping `m` items before collecting each subset. For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 1, 2, 3, 4, 5, 1, 2, 3, 4, 5, 1, 2 )
    .buffer( size: 3, skip: 2 )
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
[3, 4, 5]
[3, 4, 5]
```

The `remainder` option can be used to emit any remaining items as a partial subset.
:::::::

::::: section
## collate[​](#collate "Direct link to collate"){.hash-link aria-label="Direct link to collate" translate="no"} {#collate .anchor .anchorTargetStickyNavbar_Vzrq}

warning

This operator depends on the ordering of values in the source channel. It can lead to [non-deterministic behavior](/nextflow/cache-and-resume#non-deterministic-process-inputs) if used improperly.

*Returns: channel*

The `collate` operator collects items from a source channel into groups of *N* items.

This operator has multiple variants:

::: section
##### `collate( size, remainder = true )`[​](#collate-size-remainder--true- "Direct link to collate-size-remainder--true-"){.hash-link aria-label="Direct link to collate-size-remainder--true-" translate="no"} {#collate-size-remainder--true- .anchor .anchorTargetStickyNavbar_Vzrq}

Collects items into groups of `size` items:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of(1, 2, 3, 1, 2, 3, 1)
    .collate( 3 )
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
[1, 2, 3]
[1, 2, 3]
[1]
```

By default, any remaining items are emitted as a partial group. You can specify `false` as the second parameter to discard them instead:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of(1, 2, 3, 1, 2, 3, 1)
    .collate( 3, false )
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
[1, 2, 3]
[1, 2, 3]
```

note

This version of `collate` is equivalent to `buffer( size: n, remainder: true | false )`.

::: section
##### `collate( size, step, remainder = true )`[​](#collate-size-step-remainder--true- "Direct link to collate-size-step-remainder--true-"){.hash-link aria-label="Direct link to collate-size-step-remainder--true-" translate="no"} {#collate-size-step-remainder--true- .anchor .anchorTargetStickyNavbar_Vzrq}

Collects items into groups of `size` items using a *sliding window* that moves by `step` items at a time:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of(1, 2, 3, 4)
    .collate( 3, 1 )
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
[1, 2, 3]
[2, 3, 4]
[3, 4]
[4]
```

You can specify `false` as the third parameter to discard any remaining items.

::::: section
## collect[​](#collect "Direct link to collect"){.hash-link aria-label="Direct link to collect" translate="no"} {#collect .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: dataflow value*

The `collect` operator collects all items from a source channel into a list and emits it as a single item:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 1, 2, 3, 4 )
    .collect()
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
[1, 2, 3, 4]
```

An optional [closure](/nextflow/script#closures) can be used to transform each item before it is collected:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 'hello', 'ciao', 'bonjour' )
    .collect { v -> v.length() }
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
[5, 4, 7]
```

Available options:

::: section
##### `flat`[​](#flat "Direct link to flat"){.hash-link aria-label="Direct link to flat" translate="no"} {#flat .anchor .anchorTargetStickyNavbar_Vzrq}

When `true`, nested list structures are flattened and their items are collected individually (default: `true`).

::: section
##### `sort`[​](#sort "Direct link to sort"){.hash-link aria-label="Direct link to sort" translate="no"} {#sort .anchor .anchorTargetStickyNavbar_Vzrq}

When `true`, the collected items are sorted by their natural ordering (default: `false`). Can also be a [closure](/nextflow/script#closures) or a [Comparator](https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html){target="_blank" rel="noopener noreferrer"} which defines how items are compared during sorting.

:::::::::::::: section
## collectFile[​](#collectfile "Direct link to collectFile"){.hash-link aria-label="Direct link to collectFile" translate="no"} {#collectfile .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: channel*

The `collectFile` operator collects the items from a source channel and saves them to one or more files, emitting the collected file(s).

This operator has multiple variants:

::: section
##### `collectFile( name: '...', [options] )`[​](#collectfile-name--options- "Direct link to collectfile-name--options-"){.hash-link aria-label="Direct link to collectfile-name--options-" translate="no"} {#collectfile-name--options- .anchor .anchorTargetStickyNavbar_Vzrq}

Collects the items and saves them to a single file specified by the `name` option:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of('alpha', 'beta', 'gamma')
    .collectFile(name: 'sample.txt', newLine: true)
    .subscribe { file ->
        println "Entries are saved to file: $file"
        println "File content is: ${file.text}"
    }
```

::: section
##### `collectFile( closure, [options] )`[​](#collectfile-closure-options- "Direct link to collectfile-closure-options-"){.hash-link aria-label="Direct link to collectfile-closure-options-" translate="no"} {#collectfile-closure-options- .anchor .anchorTargetStickyNavbar_Vzrq}

Collects the items into groups and saves each group to a file, using a grouping criteria. The grouping criteria is a [closure](/nextflow/script#closures) that maps each item to a pair, where the first element is the file name for the group and the second element is the content to be appended to that file. For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of('Hola', 'Ciao', 'Hello', 'Bonjour', 'Halo')
    .collectFile { item ->
        [ "${item[0]}.txt", item + '\n' ]
    }
    .subscribe { file ->
        println "File '${file.name}' contains:"
        println file.text
    }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
File 'B.txt' contains:
Bonjour

File 'C.txt' contains:
Ciao

File 'H.txt' contains:
Halo
Hola
Hello
```

When the items from the source channel are files, the grouping criteria can be omitted. In this case, the items will be grouped by their source filename.

The following example shows how to use a closure to collect and sort all sequences in a FASTA file from shortest to longest:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.fromPath('/data/sequences.fa')
    .splitFasta( record: [id: true, sequence: true] )
    .collectFile( name: 'result.fa', sort: { v -> v.size() } ) {
        v -> v.sequence
    }
    .view { fa -> fa.text }
```

warning

The `collectFile` operator needs to store files in a temporary directory that is automatically deleted on workflow completion. For performance reasons, this directory is located in the machine\'s local storage, and it should have as much free space as the data that is being collected. The `tempDir` option can be used to specify a different temporary directory.

Available options:

::: section
##### `cache`[​](#cache "Direct link to cache"){.hash-link aria-label="Direct link to cache" translate="no"} {#cache .anchor .anchorTargetStickyNavbar_Vzrq}

Controls the caching ability of the `collectFile` operator when using the *resume* feature. It follows the same semantic of the [cache](/nextflow/reference/process#cache) directive (default: `true`).

::: section
##### `keepHeader`[​](#keepheader "Direct link to keepheader"){.hash-link aria-label="Direct link to keepheader" translate="no"} {#keepheader .anchor .anchorTargetStickyNavbar_Vzrq}

Prepend the resulting file with the header fetched in the first collected file. The header size (i.e. lines) can be specified by using the `skip` option (default: `0`), to determine how many lines to remove from all collected files except for the first (where no lines will be removed).

::: section
##### `name`[​](#name "Direct link to name"){.hash-link aria-label="Direct link to name" translate="no"} {#name .anchor .anchorTargetStickyNavbar_Vzrq}

Name of the file where all received values are stored.

::: section
##### `newLine`[​](#newline "Direct link to newline"){.hash-link aria-label="Direct link to newline" translate="no"} {#newline .anchor .anchorTargetStickyNavbar_Vzrq}

Appends a `newline` character automatically after each entry (default: `false`).

::: section
##### `seed`[​](#seed "Direct link to seed"){.hash-link aria-label="Direct link to seed" translate="no"} {#seed .anchor .anchorTargetStickyNavbar_Vzrq}

A value or a map of values used to initialize the files content.

::: section
##### `skip`[​](#skip "Direct link to skip"){.hash-link aria-label="Direct link to skip" translate="no"} {#skip .anchor .anchorTargetStickyNavbar_Vzrq}

Skip the first `n` lines e.g. `skip: 1` (default: `0`).

::: section
##### `sort`[​](#sort-1 "Direct link to sort-1"){.hash-link aria-label="Direct link to sort-1" translate="no"} {#sort-1 .anchor .anchorTargetStickyNavbar_Vzrq}

Defines sorting criteria of content in resulting file(s). Can be one of the following values:

- `false`: Disable content sorting. Entries are appended as they are produced.
- `true`: Order the content by the entry\'s natural ordering, i.e. numerical for number, lexicographic for string, etc. See the [Java documentation](http://docs.oracle.com/javase/tutorial/collections/interfaces/order.html){target="_blank" rel="noopener noreferrer"} for more information.
- `'index'`: Order the content by the incremental index number assigned to each entry while they are collected.
- `'hash'`: (default) Order the content by the hash number associated to each entry
- `'deep'`: Similar to the previous, but the hash number is created on actual entry content, e.g. when the entry is a file, the hash is created on the actual file content.
- A custom sorting criteria can be specified with a [closure](/nextflow/script#closures) or a [Comparator](http://docs.oracle.com/javase/7/docs/api/java/util/Comparator.html){target="_blank" rel="noopener noreferrer"} object.

The file content is sorted in such a way that it does not depend on the order in which entries were added to it, which guarantees that it is consistent (i.e. does not change) across different executions with the same data.

::: section
##### `storeDir`[​](#storedir "Direct link to storedir"){.hash-link aria-label="Direct link to storedir" translate="no"} {#storedir .anchor .anchorTargetStickyNavbar_Vzrq}

Folder where the resulting file(s) are stored.

::: section
##### `tempDir`[​](#tempdir "Direct link to tempdir"){.hash-link aria-label="Direct link to tempdir" translate="no"} {#tempdir .anchor .anchorTargetStickyNavbar_Vzrq}

Folder where temporary files, used by the collecting process, are stored.
::::::::::::::

::: section
## combine[​](#combine "Direct link to combine"){.hash-link aria-label="Direct link to combine" translate="no"} {#combine .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: channel*

The `combine` operator produces the combinations (i.e. cross product, \"Cartesian\" product) of two source channels, or a channel and a list (as the right operand), emitting each combination separately.

For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
numbers = channel.of(1, 2, 3)
words = channel.of('hello', 'ciao')

numbers
    .combine(words)
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
[1, hello]
[2, hello]
[3, hello]
[1, ciao]
[2, ciao]
[3, ciao]
```

The `by` option can be used to combine items that share a matching key. The value should be the zero-based index of the tuple, or a list of indices. For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
source = channel.of( [1, 'alpha'], [2, 'beta'] )
target = channel.of( [1, 'x'], [1, 'y'], [1, 'z'], [2, 'p'], [2, 'q'], [2, 't'] )

source.combine(target, by: 0).view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
[1, alpha, x]
[1, alpha, y]
[1, alpha, z]
[2, beta, p]
[2, beta, q]
[2, beta, t]
```

note

The `combine` operator is similar to `cross` and `join`, making them easy to confuse. Their differences can be summarized as follows:

- `combine` and `cross` both produce an *outer product* or *cross product*, whereas `join` produces an *inner product*.

- `combine` filters pairs with a matching key only if the `by` option is used, whereas `cross` always filters pairs with a matching key.

- `combine` with the `by` option merges and flattens each pair, whereas `cross` does not. Compare the examples for `combine` and `cross` to see this difference.

::: section
## concat[​](#concat "Direct link to concat"){.hash-link aria-label="Direct link to concat" translate="no"} {#concat .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: channel*

The `concat` operator emits the items from two or more source channels into a single output channel. Each source channel is emitted in the order in which it was specified.

In other words, given *N* channels, the items from the *i+1*-th channel are emitted only after all of the items from the *i*-th channel have been emitted.

For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
a = channel.of( 'a', 'b', 'c' )
b = channel.of( 1, 2, 3 )
c = channel.of( 'p', 'q' )

c.concat( b, a ).view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
p
q
1
2
3
a
b
c
```

tip

As a best practice, use [`mix`](#mix) instead of `concat`. The `mix` operator does not wait for each source channel to emit all values before processing the next one.

::: section
## count[​](#count "Direct link to count"){.hash-link aria-label="Direct link to count" translate="no"} {#count .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: dataflow value*

The `count` operator computes the total number of items in a source channel and emits it:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of(9, 1, 7, 5)
    .count()
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
4
```

An optional filter can be provided to select which items to count. The selection criteria can be a literal value, a [regular expression](/nextflow/script#regular-expressions), a type qualifier (i.e. Java class), or a boolean predicate. For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of(4, 1, 7, 1, 1)
    .count(1)
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
3
```

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of('a', 'c', 'c', 'q', 'b')
    .count( ~/c/ )
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
2
```

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of('a', 'c', 'c', 'q', 'b')
    .count { v -> v <= 'c' }
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
4
```

::: section
## countFasta[​](#countfasta "Direct link to countFasta"){.hash-link aria-label="Direct link to countFasta" translate="no"} {#countfasta .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: dataflow value*

Counts the total number of records in a channel of FASTA files, equivalent to `splitFasta | count`. See [splitFasta](#splitfasta) for the list of available options.

::: section
## countFastq[​](#countfastq "Direct link to countFastq"){.hash-link aria-label="Direct link to countFastq" translate="no"} {#countfastq .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: dataflow value*

Counts the total number of records in a channel of FASTQ files, equivalent to `splitFastq | count`. See [splitFastq](#splitfastq) for the list of available options.

::: section
## countJson[​](#countjson "Direct link to countJson"){.hash-link aria-label="Direct link to countJson" translate="no"} {#countjson .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: dataflow value*

Counts the total number of records in a channel of JSON files, equivalent to `splitJson | count`. See [splitJson](#splitjson) for the list of available options.

::: section
## countLines[​](#countlines "Direct link to countLines"){.hash-link aria-label="Direct link to countLines" translate="no"} {#countlines .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: dataflow value*

Counts the total number of lines in a channel of text files, equivalent to `splitText | count`. See [splitLines](#splittext) for the list of available options.

::: section
## cross[​](#cross "Direct link to cross"){.hash-link aria-label="Direct link to cross" translate="no"} {#cross .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: channel*

The `cross` operator emits every pairwise combination of two channels for which the pair has a matching key.

By default, the key is defined as the first entry in a list or map, or the value itself for any other data type. For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
source = channel.of( [1, 'alpha'], [2, 'beta'] )
target = channel.of( [1, 'x'], [1, 'y'], [1, 'z'], [2, 'p'], [2, 'q'], [2, 't'] )

source.cross(target).view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
[[1, alpha], [1, x]]
[[1, alpha], [1, y]]
[[1, alpha], [1, z]]
[[2, beta], [2, p]]
[[2, beta], [2, q]]
[[2, beta], [2, t]]
```

An optional [closure](/nextflow/script#closures) can be used to define the matching key for each item:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
source = channel.of( [1, 'alpha'], [2, 'beta'] )
target = channel.of( [1, 'a'], [1, 'b'], [2, 'a'], [2, 'b'] )

source .cross(target) { v -> v[1][0] } .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
[[1, alpha], [1, a]]
[[1, alpha], [2, a]]
[[2, beta], [1, b]]
[[2, beta], [2, b]]
```

There are two important caveats when using the `cross` operator:

1.  The operator is not *commutative*, i.e. `a.cross(b)` is not the same as `b.cross(a)`
2.  Each source channel should not emit any items with duplicate keys, i.e. each item should have a unique key.

::: section
## distinct[​](#distinct "Direct link to distinct"){.hash-link aria-label="Direct link to distinct" translate="no"} {#distinct .anchor .anchorTargetStickyNavbar_Vzrq}

warning

This operator depends on the ordering of values in the source channel. It can lead to [non-deterministic behavior](/nextflow/cache-and-resume#non-deterministic-process-inputs) if used improperly.

*Returns: channel*

The `distinct` operator forwards a source channel with *consecutively* repeated items removed, such that each emitted item is different from the preceding one:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 1, 1, 2, 2, 2, 3, 1, 1, 2, 2, 3 )
    .distinct()
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
1
2
3
1
2
3
```

An optional [closure](/nextflow/script#closures) can be used to transform each value before it is evaluated for distinct-ness:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 1, 1, 2, 2, 2, 3, 1, 1, 2, 4, 6 )
    .distinct { v -> v % 2 }
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
1
2
3
2
```

::::: section
## dump[​](#dump "Direct link to dump"){.hash-link aria-label="Direct link to dump" translate="no"} {#dump .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: channel*

The `dump` operator prints each item in a source channel when the pipeline is executed with the `-dump-channels` command-line option, otherwise it does nothing. It is a useful way to inspect and debug channels quickly without having to modify the pipeline script.

The `tag` option can be used to select which channels to dump:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 1, 2, 3 )
    .map { v -> v + 1 }
    .dump(tag: 'plus1')

channel.of( 1, 2, 3 )
    .map { v -> v ** 2 }
    .dump(tag: 'exp2')
```

Then, you can run your pipeline with `-dump-channels plus1` or `-dump-channels exp2` to dump the content of either channel. Multiple tag names can be specified as a comma-separated list.

Available options:

::: section
##### `pretty`[​](#pretty "Direct link to pretty"){.hash-link aria-label="Direct link to pretty" translate="no"} {#pretty .anchor .anchorTargetStickyNavbar_Vzrq}

When `true`, format the output as pretty-printed JSON (default: `false`).

::: section
##### `tag`[​](#tag "Direct link to tag"){.hash-link aria-label="Direct link to tag" translate="no"} {#tag .anchor .anchorTargetStickyNavbar_Vzrq}

Associate the channel with a tag that can be specified with the `-dump-channels` option to select which channels to dump.

::: section
## filter[​](#filter "Direct link to filter"){.hash-link aria-label="Direct link to filter" translate="no"} {#filter .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: channel*

The `filter` operator emits the items from a source channel that satisfy a condition, discarding all other items. The filter condition can be a literal value, a [regular expression](/nextflow/script#regular-expressions), a type qualifier (i.e. Java class), or a boolean predicate.

The following example filters a channel with a regular expression that only matches strings beginning with `a`:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 'a', 'b', 'aa', 'bc', 3, 4.5 )
    .filter( ~/^a.*/ )
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
a
aa
```

The following example filters a channel with the `Number` type qualifier so that only numbers are emitted:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 'a', 'b', 'aa', 'bc', 3, 4.5 )
    .filter( Number )
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
3
4.5
```

The following example filters a channel using a boolean predicate, which is a [closure](/nextflow/script#closures) that returns a boolean value. In this case, the predicate is used to select only odd numbers:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 1, 2, 3, 4, 5 )
    .filter { v -> v % 2 == 1 }
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
1
3
5
```

::: section
## first[​](#first "Direct link to first"){.hash-link aria-label="Direct link to first" translate="no"} {#first .anchor .anchorTargetStickyNavbar_Vzrq}

warning

This operator depends on the ordering of values in the source channel. It can lead to [non-deterministic behavior](/nextflow/cache-and-resume#non-deterministic-process-inputs) if used improperly.

*Returns: dataflow value*

The `first` operator emits the first item in a source channel, or the first item that matches a condition. The condition can be a [regular expression](/nextflow/script#regular-expressions), a type qualifier (i.e. Java class), or a boolean predicate. For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
// no condition is specified, emits the very first item: 1
channel.of( 1, 2, 3 )
    .first()
    .view()

// emits the first item matching the regular expression: 'aa'
channel.of( 'a', 'aa', 'aaa' )
    .first( ~/aa.*/ )
    .view()

// emits the first String value: 'a'
channel.of( 1, 2, 'a', 'b', 3 )
    .first( String )
    .view()

// emits the first item for which the predicate evaluates to true: 4
channel.of( 1, 2, 3, 4, 5 )
    .first { v -> v > 3 }
    .view()
```

::: section
## flatMap[​](#flatmap "Direct link to flatMap"){.hash-link aria-label="Direct link to flatMap" translate="no"} {#flatmap .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: channel*

The `flatMap` operator applies a *mapping function* to each item from a source channel.

When the mapping function returns a list, each element in the list is emitted separately:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 1, 2, 3 )
    .flatMap { n -> [ n, n*2, n*3 ] }
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
1
2
3
2
4
6
3
6
9
```

When the mapping function returns a map, each key-value pair in the map is emitted separately:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 1, 2, 3 )
    .flatMap { n -> [ number: n, square: n*n, cube: n*n*n ] }
    .view { entry -> "${entry.key}: ${entry.value}" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
number: 1
square: 1
cube: 1
number: 2
square: 4
cube: 8
number: 3
square: 9
cube: 27
```

::: section
## flatten[​](#flatten "Direct link to flatten"){.hash-link aria-label="Direct link to flatten" translate="no"} {#flatten .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: channel*

The `flatten` operator flattens each item from a source channel that is a list or other collection, such that each element in each collection is emitted separately:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( [1, [2, 3]], 4, [5, [6]] )
    .flatten()
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
1
2
3
4
5
6
```

As shown in the above example, deeply nested collections are also flattened.

tip

As a best practice, use [`flatMap`](#flatmap) instead of `flatten`. The `flatMap` operator only flattens one level and has a well-defined return type.

::::::: section
## groupTuple[​](#grouptuple "Direct link to groupTuple"){.hash-link aria-label="Direct link to groupTuple" translate="no"} {#grouptuple .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: queue channel*

The `groupTuple` operator collects lists (i.e. *tuples*) from a source channel into groups based on a grouping key. A new tuple is emitted for each distinct key.

To be more precise, the operator transforms a sequence of tuples like *(K, V1, V2, ..)* into a sequence of tuples like *(K, list(V), list(W), ..)*.

For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( [1, 'A'], [1, 'B'], [2, 'C'], [3, 'B'], [1, 'C'], [2, 'A'], [3, 'D'] )
    .groupTuple()
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
[1, [A, B, C]]
[2, [C, A]]
[3, [B, D]]
```

By default, the first element of each tuple is used as the grouping key. The `by` option can be used to specify a different index, or list of indices. For example, to group by the second element of each tuple:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( [1, 'A'], [1, 'B'], [2, 'C'], [3, 'B'], [1, 'C'], [2, 'A'], [3, 'D'] )
    .groupTuple(by: 1)
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
[[1, 2], A]
[[1, 3], B]
[[2, 1], C]
[[3], D]
```

By default, if you don\'t specify a size, the `groupTuple` operator will not emit any groups until *all* inputs have been received. If possible, you should always try to specify the number of expected elements in each group using the `size` option, so that each group can be emitted as soon as it\'s ready. In cases where the size of each group varies based on the grouping key, you can use the built-in `groupKey()` function, which allows you to define a different expected size for each group:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of(
        ['chr1', ['/path/to/region1_chr1.vcf', '/path/to/region2_chr1.vcf']],
        ['chr2', ['/path/to/region1_chr2.vcf', '/path/to/region2_chr2.vcf', '/path/to/region3_chr2.vcf']],
    )
    .flatMap { chr, vcfs ->
        vcfs.collect { vcf ->
            tuple(groupKey(chr, vcfs.size()), vcf)              // preserve group size with key
        }
    }
    .view { v -> "scattered: ${v}" }
    .groupTuple()
    .map { key, vcfs -> tuple(key.getGroupTarget(), vcfs) }     // unwrap group key
    .view { v -> "gathered: ${v}" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
scattered: [chr1, /path/to/region1_chr1.vcf]
scattered: [chr1, /path/to/region2_chr1.vcf]
scattered: [chr2, /path/to/region1_chr2.vcf]
scattered: [chr2, /path/to/region2_chr2.vcf]
scattered: [chr2, /path/to/region3_chr2.vcf]
gathered: [chr1, [/path/to/region1_chr1.vcf, /path/to/region2_chr1.vcf]]
gathered: [chr2, [/path/to/region1_chr2.vcf, /path/to/region2_chr2.vcf, /path/to/region3_chr2.vcf]]
```

Available options:

::: section
##### `by`[​](#by "Direct link to by"){.hash-link aria-label="Direct link to by" translate="no"} {#by .anchor .anchorTargetStickyNavbar_Vzrq}

The zero-based index of the element to use as the grouping key. Can also be a list of indices, e.g. `by: [0,2]` (default: `[0]`).

::: section
##### `remainder`[​](#remainder "Direct link to remainder"){.hash-link aria-label="Direct link to remainder" translate="no"} {#remainder .anchor .anchorTargetStickyNavbar_Vzrq}

When `true`, incomplete tuples (i.e. groups with less than `size` items) are emitted as partial groups, otherwise they are discarded (default: `false`). This option can only be used with `size`.

::: section
##### `size`[​](#size "Direct link to size"){.hash-link aria-label="Direct link to size" translate="no"} {#size .anchor .anchorTargetStickyNavbar_Vzrq}

The required number of items for each group. When a group reaches the required size, it is emitted.

::: section
##### `sort`[​](#sort-2 "Direct link to sort-2"){.hash-link aria-label="Direct link to sort-2" translate="no"} {#sort-2 .anchor .anchorTargetStickyNavbar_Vzrq}

Defines the sorting criteria for the grouped items. Can be one of the following values:

warning

The `sort` option is discouraged because it can lead to inconsistent sorting when there are multiple groups. Perform sorting separately (e.g., in a subsequent `map` operation) to ensure correct results.
:::::::

::: section
## ifEmpty[​](#ifempty "Direct link to ifEmpty"){.hash-link aria-label="Direct link to ifEmpty" translate="no"} {#ifempty .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: channel*

The `ifEmpty` operator emits a source channel, or a default value if the source channel is *empty* (doesn\'t emit any value):

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of(1, 2, 3).ifEmpty('Hello').view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
1
2
3
```

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.empty().ifEmpty('Hello').view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
Hello
```

The default value can also be a [closure](/nextflow/script#closures), in which case the closure is evaluated and the result is emitted when the source channel is empty.

See also: [empty](/nextflow/reference/stdlib-namespaces/channel#empty) channel factory

::::::: section
## join[​](#join "Direct link to join"){.hash-link aria-label="Direct link to join" translate="no"} {#join .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: channel*

The `join` operator emits the inner product of two source channels using a matching key.

To be more precise, the operator transforms a sequence of tuples like *(K, V1, V2, ..)* and *(K, W1, W2, ..)* into a sequence of tuples like *(K, V1, V2, .., W1, W2, ..)*.

For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
left  = channel.of( ['X', 1], ['Y', 2], ['Z', 3], ['P', 7] )
right = channel.of( ['Z', 6], ['Y', 5], ['X', 4] )

left.join(right).view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
[Z, 3, 6]
[Y, 2, 5]
[X, 1, 4]
```

By default, the first element of each item is used as the key. The `by` option can be used to specify a different index, or list of indices.

By default, unmatched items are discarded. The `remainder` option can be used to emit them at the end:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
left  = channel.of( ['X', 1], ['Y', 2], ['Z', 3], ['P', 7] )
right = channel.of( ['Z', 6], ['Y', 5], ['X', 4] )

left.join(right, remainder: true).view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
[Y, 2, 5]
[Z, 3, 6]
[X, 1, 4]
[P, 7, null]
```

note

The `join` operator is similar to an SQL *inner join*, or an SQL *outer join* when `remainder` is `true`. The only difference is that `join` does not support duplicate keys, whereas an SQL join produces the cross-product of duplicate keys. The `combine` operator with the `by` option is equivalent to an SQL join.

Available options:

::: section
##### `by`[​](#by-1 "Direct link to by-1"){.hash-link aria-label="Direct link to by-1" translate="no"} {#by-1 .anchor .anchorTargetStickyNavbar_Vzrq}

The zero-based index of each item to use as the matching key. Can also be a list of indices, e.g. `by: [0, 2]` (default: `[0]`).

::: section
##### `failOnDuplicate`[​](#failonduplicate "Direct link to failonduplicate"){.hash-link aria-label="Direct link to failonduplicate" translate="no"} {#failonduplicate .anchor .anchorTargetStickyNavbar_Vzrq}

When `true`, an error is reported when the operator receives multiple items from the same channel with the same key (default: `false`). Value is set to `true` if [strict mode](/nextflow/reference/feature-flags) is enabled.

::: section
##### `failOnMismatch`[​](#failonmismatch "Direct link to failonmismatch"){.hash-link aria-label="Direct link to failonmismatch" translate="no"} {#failonmismatch .anchor .anchorTargetStickyNavbar_Vzrq}

When `true`, an error is reported when the operator receives an item from one channel for which there no matching item from the other channel (default: `false`). Value is set to `true` if [strict mode](/nextflow/reference/feature-flags) is enabled. This option cannot be used with `remainder`.

::: section
##### `remainder`[​](#remainder-1 "Direct link to remainder-1"){.hash-link aria-label="Direct link to remainder-1" translate="no"} {#remainder-1 .anchor .anchorTargetStickyNavbar_Vzrq}

When `true`, unmatched items are emitted at the end, otherwise they are discarded (default: `false`).
:::::::

::: section
## last[​](#last "Direct link to last"){.hash-link aria-label="Direct link to last" translate="no"} {#last .anchor .anchorTargetStickyNavbar_Vzrq}

warning

This operator depends on the ordering of values in the source channel. It can lead to [non-deterministic behavior](/nextflow/cache-and-resume#non-deterministic-process-inputs) if used improperly.

*Returns: dataflow value*

The `last` operator emits the last item from a source channel:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 1, 2, 3, 4, 5, 6 )
    .last()
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
6
```

::: section
## map[​](#map "Direct link to map"){.hash-link aria-label="Direct link to map" translate="no"} {#map .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: channel*

The `map` operator applies a *mapping function* to each item from a source channel:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 1, 2, 3, 4, 5 )
    .map { v -> v * v }
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
1
4
9
16
25
```

note

`null` values are not emitted by `map`.

::: section
## max[​](#max "Direct link to max"){.hash-link aria-label="Direct link to max" translate="no"} {#max .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: dataflow value*

The `max` operator emits the item with the greatest value from a source channel:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 8, 6, 2, 5 )
    .max()
    .view { v -> "Max value is $v" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
Max value is 8
```

An optional [closure](/nextflow/script#closures) can be used to control how the items are compared. The closure can be a *mapping function*, which transforms each item before it is compared, or a *comparator function*, which defines how to compare two items more generally.

The following examples show how to find the longest string in a channel:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
// mapping function
channel.of( "hello", "hi", "hey" )
    .max { v -> v.length() }
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
hello
```

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
// comparator function
channel.of( "hello", "hi", "hey" )
    .max { a, b -> a.length() <=> b.length() }
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
hello
```

::: section
## merge[​](#merge "Direct link to merge"){.hash-link aria-label="Direct link to merge" translate="no"} {#merge .anchor .anchorTargetStickyNavbar_Vzrq}

warning

This operator depends on the ordering of values in the source channel. It can lead to [non-deterministic behavior](/nextflow/cache-and-resume#non-deterministic-process-inputs) if used improperly.

Use [combine](#combine) or [join](#join) instead to combine multiple channels in a deterministic way, such as a matching key.

*Returns: channel*

The `merge` operator joins the items from two or more channels into a new channel:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
odds  = channel.of(1, 3, 5, 7, 9)
evens = channel.of(2, 4, 6)

odds
    .merge( evens )
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
[1, 2]
[3, 4]
[5, 6]
```

An optional [closure](/nextflow/script#closures) can be used to control how two items are merged:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
odds  = channel.of(1, 3, 5, 7, 9)
evens = channel.of(2, 4, 6)

odds
    .merge( evens ) { a, b -> tuple(b*b, a) }
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
[16, 3]
[36, 5]
[4, 1]
```

The `merge` operator may return a channel or value depending on the inputs:

- If the first argument is a channel, the `merge` operator returns a channel merging as many values as are available for all inputs. Dataflow values are re-used for each merged value.

- If the first argument is a dataflow value, the `merge` operator returns a dataflow value, merging the first value from each input, regardless of whether there are channel inputs with additional values.

::: section
## min[​](#min "Direct link to min"){.hash-link aria-label="Direct link to min" translate="no"} {#min .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: dataflow value*

The `min` operator emits the item with the lowest value from a source channel:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 8, 6, 2, 5 )
    .min()
    .view { v -> "Min value is $v" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
Min value is 2
```

An optional [closure](/nextflow/script#closures) can be used to control how the items are compared. The closure can be a *mapping function*, which transforms each item before it is compared, or a *comparator function*, which defines how to compare two items more generally.

The following examples show how to find the shortest string in a channel:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
// mapping function
channel.of( "hello", "hi", "hey" )
    .min { v -> v.length() }
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
hi
```

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
// comparator function
channel.of( "hello", "hi", "hey" )
    .min { a, b -> a.length() <=> b.length() }
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
hi
```

::: section
## mix[​](#mix "Direct link to mix"){.hash-link aria-label="Direct link to mix" translate="no"} {#mix .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: channel*

The `mix` operator emits the items from two or more source channels into a single output channel:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
c1 = channel.of( '1', '2', '3' )
c2 = channel.of( 'a', 'b' )
v3 = channel.value( 'z' )

c1.mix(c2).mix(v3).view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
1
2
3
a
b
z
```

The items in the mixed output channel may appear in any order, regardless of which source channel they came from. Thus, the previous example could also output the following:

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
z
1
a
2
b
3
```

::: section
## multiMap[​](#multimap "Direct link to multiMap"){.hash-link aria-label="Direct link to multiMap" translate="no"} {#multimap .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: multiple channels*

The `multiMap` operator applies a set of mapping functions to a source channel, producing a separate output channel for each mapping function.

The multi-map criteria is a [closure](/nextflow/script#closures) that defines, for each output channel, a label followed by a mapping expression.

For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 1, 2, 3, 4 )
    .multiMap { v ->
        inc: v + 1
        squared: v * v
    }
    .set { result }

result.inc.view { v -> "inc $v" }
result.squared.view { v -> "squared $v" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
inc 2
inc 3
inc 4
inc 5
squared 1
squared 4
squared 9
squared 16
```

Multiple labels can share the same mapping expression using the following shorthand:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 1, 2, 3 )
    .multiMap { v -> alpha: beta: v }
    .set { result }

result.alpha.view { v -> "alpha $v" }
result.beta.view { v -> "beta $v" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
alpha 1
alpha 2
alpha 3
beta 1
beta 2
beta 3
```

The above example creates two channels as before, but now they both receive the same items.

You can use the `multiMapCriteria()` method to create a multi-map criteria as a variable that can be passed as an argument to any number of `multiMap` operations, as shown below:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
def criteria = multiMapCriteria { v ->
    small: [v, v < 10]
    large: [v, v > 10]
}

channel.of(1, 2, 30).multiMap(criteria).set { ch1 }
channel.of(10, 20, 1).multiMap(criteria).set { ch2 }

ch1.small.view { v, is_small -> "ch1: $v is small: $is_small" }
ch1.large.view { v, is_large -> "ch1: $v is large: $is_large" }
ch2.small.view { v, is_small -> "ch2: $v is small: $is_small" }
ch2.large.view { v, is_large -> "ch2: $v is large: $is_large" }
```

note

If you use `multiMap` to split a tuple or map into multiple channels, it is recommended that you retain a matching key (e.g. sample ID) with *each* new channel, so that you can re-combine these channels later on if needed. In general, you should not expect to be able to merge channels correctly without a matching key, due to the concurrent nature of Nextflow pipelines.

::: section
## randomSample[​](#randomsample "Direct link to randomSample"){.hash-link aria-label="Direct link to randomSample" translate="no"} {#randomsample .anchor .anchorTargetStickyNavbar_Vzrq}

warning

This operator depends on the ordering of values in the source channel. It can lead to [non-deterministic behavior](/nextflow/cache-and-resume#non-deterministic-process-inputs) if used improperly.

*Returns: channel*

The `randomSample` operator emits a randomly-selected subset of items from a source channel:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 1..100 )
    .randomSample( 10 )
    .view()
```

The above snippet will print 10 randomly-selected numbers between 1 and 100 (without replacement).

An optional second parameter can be used to set the initial *seed* for the random number generator, which ensures that the `randomSample` operator produces the same pseudo-random sequence across runs:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 1..100 )
    .randomSample( 10, 234 )
    .view()
```

The above example will print 10 randomly-selected numbers between 1 and 100 (without replacement). Each subsequent script execution will produce the same sequence.

::: section
## reduce[​](#reduce "Direct link to reduce"){.hash-link aria-label="Direct link to reduce" translate="no"} {#reduce .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: dataflow value*

The `reduce` operator applies an *accumulator function* sequentially to each item in a source channel, and emits the final accumulated value. The accumulator function takes two parameters \-- the accumulated value and the *i*-th emitted item \-- and it should return the accumulated result, which is passed to the next invocation with the *i+1*-th item. This process is repeated for each item in the source channel.

For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 1, 2, 3, 4, 5 )
    .reduce { a, b ->
        println "a: $a b: $b"
        a + b
    }
    .view { result -> "result = $result" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
a: 1 b: 2
a: 3 b: 3
a: 6 b: 4
a: 10 b: 5
result = 15
```

By default, the first item is used as the initial accumulated value. You can optionally specify a different initial value as shown below:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 1, 2, 3, 4, 5 )
    .reduce( 'result:' ) { acc, v ->
        println acc
        acc + ' ' + v
    }
    .view { result -> "final $result" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
result:
result: 1
result: 1 2
result: 1 2 3
result: 1 2 3 4
final result: 1 2 3 4 5
```

::: section
## set[​](#set "Direct link to set"){.hash-link aria-label="Direct link to set" translate="no"} {#set .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: nothing*

The `set` operator assigns a source channel to a variable, whose name is specified as a closure parameter:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of(10, 20, 30).set { my_channel }
```

Using `set` is semantically equivalent to assigning a variable:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
my_channel = channel.of(10, 20, 30)
```

tip

As a best practice, use a standard assignment (`=`) instead of `set`. Standard assignments enable more effective [type checking](/nextflow/tutorials/static-types#preparing-for-static-typing).

:::::::::::: section
## splitCsv[​](#splitcsv "Direct link to splitCsv"){.hash-link aria-label="Direct link to splitCsv" translate="no"} {#splitcsv .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: channel*

The `splitCsv` operator parses and splits [CSV](http://en.wikipedia.org/wiki/Comma-separated_values){target="_blank" rel="noopener noreferrer"} files or text from a source channel into records.

For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( '10,20,30\n70,80,90' )
    .splitCsv()
    .view { row -> "${row[0]} - ${row[1]} - ${row[2]}" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
10 - 20 - 30
70 - 80 - 90
```

The above example shows hows CSV text is parsed and split into individual rows, where each row is simply a list of columns.

When the CSV begins with a header line defining the column names, and the `header` option is `true`, each row is returned as a map instead:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 'alpha,beta,gamma\n10,20,30\n70,80,90' )
    .splitCsv( header: true )
    .view { row -> "${row.alpha} - ${row.beta} - ${row.gamma}" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
10 - 20 - 30
70 - 80 - 90
```

The `header` option can also just be a list of columns:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 'alpha,beta,gamma\n10,20,30\n70,80,90' )
    .splitCsv( header: ['col1', 'col2', 'col3'], skip: 1 )
    .view { row -> "${row.col1} - ${row.col2} - ${row.col3}" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
10 - 20 - 30
70 - 80 - 90
```

Available options:

::: section
##### `by`[​](#by-2 "Direct link to by-2"){.hash-link aria-label="Direct link to by-2" translate="no"} {#by-2 .anchor .anchorTargetStickyNavbar_Vzrq}

When specified, group rows into *chunks* with the given size (default: none).

::: section
##### `charset`[​](#charset "Direct link to charset"){.hash-link aria-label="Direct link to charset" translate="no"} {#charset .anchor .anchorTargetStickyNavbar_Vzrq}

Parse the content with the specified charset, e.g. `UTF-8`. See the list of [standard charsets](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/nio/charset/StandardCharsets.html){target="_blank" rel="noopener noreferrer"} for available options.

::: section
##### `decompress`[​](#decompress "Direct link to decompress"){.hash-link aria-label="Direct link to decompress" translate="no"} {#decompress .anchor .anchorTargetStickyNavbar_Vzrq}

When `true`, decompress the content using the GZIP format before processing it (default: `false`). Files with the `.gz` extension are decompressed automatically.

::: section
##### `header`[​](#header "Direct link to header"){.hash-link aria-label="Direct link to header" translate="no"} {#header .anchor .anchorTargetStickyNavbar_Vzrq}

When `true`, the first line is used as the columns names (default: `false`). Can also be a list of columns names.

::: section
##### `limit`[​](#limit "Direct link to limit"){.hash-link aria-label="Direct link to limit" translate="no"} {#limit .anchor .anchorTargetStickyNavbar_Vzrq}

Limits the number of records to retrieve for each source item (default: no limit).

::: section
##### `quote`[​](#quote "Direct link to quote"){.hash-link aria-label="Direct link to quote" translate="no"} {#quote .anchor .anchorTargetStickyNavbar_Vzrq}

The character used to quote values (default: `''` or `""`).

::: section
##### `sep`[​](#sep "Direct link to sep"){.hash-link aria-label="Direct link to sep" translate="no"} {#sep .anchor .anchorTargetStickyNavbar_Vzrq}

The character used to separate values (default: `,`)

::: section
##### `skip`[​](#skip-1 "Direct link to skip-1"){.hash-link aria-label="Direct link to skip-1" translate="no"} {#skip-1 .anchor .anchorTargetStickyNavbar_Vzrq}

Number of lines to ignore from the beginning when parsing the CSV text (default: `0`).

::: section
##### `strip`[​](#strip "Direct link to strip"){.hash-link aria-label="Direct link to strip" translate="no"} {#strip .anchor .anchorTargetStickyNavbar_Vzrq}

When `true`, remove leading and trailing blanks from values (default: `false`).
::::::::::::

:::::::::::: section
## splitFasta[​](#splitfasta "Direct link to splitFasta"){.hash-link aria-label="Direct link to splitFasta" translate="no"} {#splitfasta .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: channel*

The `splitFasta` operator splits [FASTA](http://en.wikipedia.org/wiki/FASTA_format){target="_blank" rel="noopener noreferrer"} files or text from a source channel into individual sequences.

The `by` option can be used to group sequences into chunks of a given size. The following example shows how to read a FASTA file and split it into chunks of 10 sequences each:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.fromPath('misc/sample.fa')
    .splitFasta( by: 10 )
    .view()
```

warning

Chunks are stored in memory by default. When splitting large files, specify `file: true` to save the chunks into files in order to avoid running out of memory. See the list of options below for details.

The `record` option can be used to split FASTA content into *records* instead of text chunks. Each record is a map that allows you to access the FASTA sequence data with ease. For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.fromPath('misc/sample.fa')
    .splitFasta( record: [id: true, seqString: true] )
    .filter { record -> record.id =~ /^ENST0.*/ }
    .view { record -> record.seqString }
```

The above example loads the `misc/sample.fa` file, splits it into records containing the `id` and `seqString` fields (i.e. the sequence id and the sequence data), filters records by their ID, and finally prints the sequence string of each record.

Available options:

::: section
##### `by`[​](#by-3 "Direct link to by-3"){.hash-link aria-label="Direct link to by-3" translate="no"} {#by-3 .anchor .anchorTargetStickyNavbar_Vzrq}

Defines the number of sequences in each chunk (default: `1`).

::: section
##### `charset`[​](#charset-1 "Direct link to charset-1"){.hash-link aria-label="Direct link to charset-1" translate="no"} {#charset-1 .anchor .anchorTargetStickyNavbar_Vzrq}

Parse the content with the specified charset, e.g. `UTF-8`. See the list of [standard charsets](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/nio/charset/StandardCharsets.html){target="_blank" rel="noopener noreferrer"} for available options.

::: section
##### `compress`[​](#compress "Direct link to compress"){.hash-link aria-label="Direct link to compress" translate="no"} {#compress .anchor .anchorTargetStickyNavbar_Vzrq}

When `true`, resulting file chunks are GZIP compressed (default: `false`). The `.gz` suffix is automatically added to chunk file names.

::: section
##### `decompress`[​](#decompress-1 "Direct link to decompress-1"){.hash-link aria-label="Direct link to decompress-1" translate="no"} {#decompress-1 .anchor .anchorTargetStickyNavbar_Vzrq}

When `true`, decompress the content using the GZIP format before processing it (default: `false`). Files with the `.gz` extension are decompressed automatically.

::: section
##### `elem`[​](#elem "Direct link to elem"){.hash-link aria-label="Direct link to elem" translate="no"} {#elem .anchor .anchorTargetStickyNavbar_Vzrq}

The index of the element to split when the source items are lists or tuples (default: first file object or first element).

::: section
##### `file`[​](#file "Direct link to file"){.hash-link aria-label="Direct link to file" translate="no"} {#file .anchor .anchorTargetStickyNavbar_Vzrq}

When `true`, saves each split to a file. Use a string instead of `true` value to create split files with a specific name (split index number is automatically added). Finally, set this attribute to an existing directory, in order to save the split files into the specified directory.

::: section
##### `limit`[​](#limit-1 "Direct link to limit-1"){.hash-link aria-label="Direct link to limit-1" translate="no"} {#limit-1 .anchor .anchorTargetStickyNavbar_Vzrq}

Limits the number of sequences to retrieve for each source item (default: no limit).

::: section
##### `record`[​](#record "Direct link to record"){.hash-link aria-label="Direct link to record" translate="no"} {#record .anchor .anchorTargetStickyNavbar_Vzrq}

Parse each entry in the FASTA file into a record. The following fields are available:

- `id`: The FASTA sequence identifier, i.e. the word following the `>` symbol up to the first blank or newline character
- `header`: The first line in a FASTA sequence without the `>` character
- `desc`: The text in the FASTA header following the ID value
- `text`: The complete FASTA sequence including the header
- `seqString`: The sequence as a single-line string, i.e. containing no newline characters
- `sequence`: The sequence as a multi-line string, i.e. always ending with a newline character
- `width`: Define the length of a single line when the `sequence` field is used, after which the sequence continues on a new line.

::: section
##### `size`[​](#size-1 "Direct link to size-1"){.hash-link aria-label="Direct link to size-1" translate="no"} {#size-1 .anchor .anchorTargetStickyNavbar_Vzrq}

Defines the size of the expected chunks as a memory unit, e.g. `1.MB`.

See also: [countFasta](#countfasta)
::::::::::::

:::::::::::: section
## splitFastq[​](#splitfastq "Direct link to splitFastq"){.hash-link aria-label="Direct link to splitFastq" translate="no"} {#splitfastq .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: channel*

The `splitFastq` operator splits [FASTQ](http://en.wikipedia.org/wiki/FASTQ_format){target="_blank" rel="noopener noreferrer"} files or text from a source channel into individual sequences.

The `by` option can be used to group sequences into chunks of a given size. The following example shows how to read a FASTQ file and split it into chunks of 10 sequences each:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
Channel
channel.fromPath('misc/sample.fastq')
    .splitFastq( by: 10 )
    .view()
```

warning

Chunks are stored in memory by default. When splitting large files, specify `file: true` to save the chunks into files in order to avoid running out of memory. See the list of options below for details.

The `record` option can be used to split FASTQ content into *records* instead of text chunks. Each record is a map that allows you to access the FASTQ sequence data with ease. For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.fromPath('misc/sample.fastq')
    .splitFastq( record: true )
    .view { record -> record.readHeader }
```

The `pe` option can be used to split paired-end FASTQ files. The source channel must emit tuples containing the file pairs. For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.fromFilePairs('/my/data/SRR*_{1,2}.fastq', flat: true)
    .splitFastq(by: 100_000, pe: true, file: true)
    .view()
```

note

`channel.fromFilePairs()` requires the `flat: true` option in order to emit the file pairs as separate elements in the produced tuples.

note

This operator assumes that the order of the paired-end reads correspond with each other and that both files contain the same number of reads.

Available options:

::: section
##### `by`[​](#by-4 "Direct link to by-4"){.hash-link aria-label="Direct link to by-4" translate="no"} {#by-4 .anchor .anchorTargetStickyNavbar_Vzrq}

Defines the number of sequences in each chunk (default: `1`).

::: section
##### `charset`[​](#charset-2 "Direct link to charset-2"){.hash-link aria-label="Direct link to charset-2" translate="no"} {#charset-2 .anchor .anchorTargetStickyNavbar_Vzrq}

Parse the content with the specified charset, e.g. `UTF-8`. See the list of [standard charsets](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/nio/charset/StandardCharsets.html){target="_blank" rel="noopener noreferrer"} for available options.

::: section
##### `compress`[​](#compress-1 "Direct link to compress-1"){.hash-link aria-label="Direct link to compress-1" translate="no"} {#compress-1 .anchor .anchorTargetStickyNavbar_Vzrq}

When `true`, resulting file chunks are GZIP compressed (default: `false`). The `.gz` suffix is automatically added to chunk file names.

::: section
##### `decompress`[​](#decompress-2 "Direct link to decompress-2"){.hash-link aria-label="Direct link to decompress-2" translate="no"} {#decompress-2 .anchor .anchorTargetStickyNavbar_Vzrq}

When `true`, decompress the content using the GZIP format before processing it (default: `false`). Files with the `.gz` extension are decompressed automatically.

::: section
##### `elem`[​](#elem-1 "Direct link to elem-1"){.hash-link aria-label="Direct link to elem-1" translate="no"} {#elem-1 .anchor .anchorTargetStickyNavbar_Vzrq}

The index of the element to split when the source items are lists or tuples (default: first file object or first element).

::: section
##### `file`[​](#file-1 "Direct link to file-1"){.hash-link aria-label="Direct link to file-1" translate="no"} {#file-1 .anchor .anchorTargetStickyNavbar_Vzrq}

When `true`, saves each split to a file. Use a string instead of `true` value to create split files with a specific name (split index number is automatically added). Finally, set this attribute to an existing directory, in order to save the split files into the specified directory.

::: section
##### `limit`[​](#limit-2 "Direct link to limit-2"){.hash-link aria-label="Direct link to limit-2" translate="no"} {#limit-2 .anchor .anchorTargetStickyNavbar_Vzrq}

Limits the number of sequences to retrieve for each source item (default: no limit).

::: section
##### `pe`[​](#pe "Direct link to pe"){.hash-link aria-label="Direct link to pe" translate="no"} {#pe .anchor .anchorTargetStickyNavbar_Vzrq}

When `true`, splits paired-end read files. Items emitted by the source channel must be tuples with the file pairs.

::: section
##### `record`[​](#record-1 "Direct link to record-1"){.hash-link aria-label="Direct link to record-1" translate="no"} {#record-1 .anchor .anchorTargetStickyNavbar_Vzrq}

Parse each entry in the FASTQ file into a record. The following fields are available:

- `readHeader`: Sequence header (without the `@` prefix)
- `readString`: The raw sequence content
- `qualityHeader`: Base quality header (it may be empty)
- `qualityString`: Quality values for the sequence

See also: [countFastq](#countfastq)
::::::::::::

::::: section
## splitJson[​](#splitjson "Direct link to splitJson"){.hash-link aria-label="Direct link to splitJson" translate="no"} {#splitjson .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: channel*

The `splitJson` operator splits [JSON](https://en.wikipedia.org/wiki/JSON){target="_blank" rel="noopener noreferrer"} files or text from a source channel into individual records.

If the source item is a JSON array, each element of the array will be emitted:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
// Example with a JSON array
channel.of('[1, null, ["A", {}], true]')
    .splitJson()
    .view { v -> "Item: ${v}" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
Item: 1
Item: null
Item: [A, [:]]
Item: true
```

If the source item is a JSON object, each key-value pair will be emitted as a map with the properties `key` and `value`:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
// Example with a JSON object
channel.of('{"A": 1, "B": [1, 2, 3], "C": {"D": null}}')
    .splitJson()
    .view { v -> "Item: ${v}" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
Item: [key:A, value:1]
Item: [key:B, value:[1, 2, 3]]
Item: [key:C, value:[D:null]]
```

The `path` option can be used to query a section of the JSON document to parse and split:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of('{"A": 1, "B": [2, 3, {"C": {"D": null, "E": 4, "F": 5}}]}')
    .splitJson(path: 'B[2].C')
    .view { v -> "Item: ${v}" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
Item: [key:D, value:null]
Item: [key:E, value:4]
Item: [key:F, value:5]
```

Available options:

::: section
##### `limit`[​](#limit-3 "Direct link to limit-3"){.hash-link aria-label="Direct link to limit-3" translate="no"} {#limit-3 .anchor .anchorTargetStickyNavbar_Vzrq}

Limits the number of records to retrieve for each source item (default: no limit).

::: section
##### `path`[​](#path "Direct link to path"){.hash-link aria-label="Direct link to path" translate="no"} {#path .anchor .anchorTargetStickyNavbar_Vzrq}

Defines a query for a section of each source item to parse and split. The expression should be a path similar to [JSONPath](https://goessner.net/articles/JsonPath/){target="_blank" rel="noopener noreferrer"}. The empty string is the document root (default). An integer in brackets is a zero-based index in a JSON array. A string preceded by a dot `.` is a key in a JSON object.

See also: [countJson](#countjson)

::::::::::: section
## splitText[​](#splittext "Direct link to splitText"){.hash-link aria-label="Direct link to splitText" translate="no"} {#splittext .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: channel*

The `splitText` operator splits multi-line text content from a source channel into chunks of *N* lines:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
Channel
    .fromPath('/some/path/*.txt')
    .splitText()
    .view()
```

The above example loads a collection of text files, splits the content of each file into individual lines, and prints each line.

The `by` option can be used to emit chunks of *N* lines:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.fromPath('/some/path/*.txt')
    .splitText( by: 10 )
    .subscribe { chunk ->
        print chunk
        print "--- end of the chunk ---\n"
    }
```

An optional [closure](/nextflow/script#closures) can be used to transform each text chunk produced by the operator. The following example shows how to split text files into chunks of 10 lines and transform them to uppercase letters:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.fromPath('/some/path/*.txt')
    .splitText( by: 10 ) { v -> v.toUpperCase() }
    .view()
```

note

Text chunks returned by the `splitText` operator are always terminated by a `\n` newline character.

Available options:

::: section
##### `by`[​](#by-5 "Direct link to by-5"){.hash-link aria-label="Direct link to by-5" translate="no"} {#by-5 .anchor .anchorTargetStickyNavbar_Vzrq}

Defines the number of lines in each `chunk` (default: `1`).

::: section
##### `charset`[​](#charset-3 "Direct link to charset-3"){.hash-link aria-label="Direct link to charset-3" translate="no"} {#charset-3 .anchor .anchorTargetStickyNavbar_Vzrq}

Parse the content with the specified charset, e.g. `UTF-8`. See the list of [standard charsets](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/nio/charset/StandardCharsets.html){target="_blank" rel="noopener noreferrer"} for available options.

::: section
##### `compress`[​](#compress-2 "Direct link to compress-2"){.hash-link aria-label="Direct link to compress-2" translate="no"} {#compress-2 .anchor .anchorTargetStickyNavbar_Vzrq}

When `true`, resulting file chunks are GZIP compressed (default: `false`). The `.gz` suffix is automatically added to chunk file names.

::: section
##### `decompress`[​](#decompress-3 "Direct link to decompress-3"){.hash-link aria-label="Direct link to decompress-3" translate="no"} {#decompress-3 .anchor .anchorTargetStickyNavbar_Vzrq}

When `true`, decompresses the content using the GZIP format before processing it (default: `false`). Files with the `.gz` extension are decompressed automatically.

::: section
##### `elem`[​](#elem-2 "Direct link to elem-2"){.hash-link aria-label="Direct link to elem-2" translate="no"} {#elem-2 .anchor .anchorTargetStickyNavbar_Vzrq}

The index of the element to split when the source items are lists or tuples (default: first file object or first element).

::: section
##### `file`[​](#file-2 "Direct link to file-2"){.hash-link aria-label="Direct link to file-2" translate="no"} {#file-2 .anchor .anchorTargetStickyNavbar_Vzrq}

When `true`, saves each split to a file. Use a string instead of `true` value to create split files with a specific name (split index number is automatically added). Finally, set this attribute to an existing directory, in order to save the split files into the specified directory.

::: section
##### `keepHeader`[​](#keepheader-1 "Direct link to keepheader-1"){.hash-link aria-label="Direct link to keepheader-1" translate="no"} {#keepheader-1 .anchor .anchorTargetStickyNavbar_Vzrq}

Parses the first line as header and prepends it to each emitted chunk (default: `false`).

::: section
##### `limit`[​](#limit-4 "Direct link to limit-4"){.hash-link aria-label="Direct link to limit-4" translate="no"} {#limit-4 .anchor .anchorTargetStickyNavbar_Vzrq}

Limits the number of lines to retrieve for each source item (default: no limit).

See also: [countLines](#countlines)
:::::::::::

:::::: section
## subscribe[​](#subscribe "Direct link to subscribe"){.hash-link aria-label="Direct link to subscribe" translate="no"} {#subscribe .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: nothing*

The `subscribe` operator invokes a custom function for each item from a source channel:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
source = channel.of( 'alpha', 'beta', 'delta' )

source.subscribe { str ->
    println "Got: ${str}; len: ${str.length()}"
}
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
Got: alpha; len: 5
Got: beta; len: 4
Got: delta; len: 5
```

The `subscribe` operator supports multiple types of event handlers:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 1, 2, 3 ).subscribe(
    onNext: { v -> println v },
    onComplete: { println 'Done' }
)
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
1
2
3
Done
```

note

Unlike most operators, `subscribe` is a *terminal operator* and does not emit any values. It should only be used for *side effects*, such as printing to the console, writing to a file, or making HTTP requests.

Available options:

::: section
##### `onNext`[​](#onnext "Direct link to onnext"){.hash-link aria-label="Direct link to onnext" translate="no"} {#onnext .anchor .anchorTargetStickyNavbar_Vzrq}

Closure that is invoked when an item is emitted. Equivalent to providing a closure as the first argument.

::: section
##### `onComplete`[​](#oncomplete "Direct link to oncomplete"){.hash-link aria-label="Direct link to oncomplete" translate="no"} {#oncomplete .anchor .anchorTargetStickyNavbar_Vzrq}

Closure that is invoked after the last item is emitted by the channel.

::: section
##### `onError`[​](#onerror "Direct link to onerror"){.hash-link aria-label="Direct link to onerror" translate="no"} {#onerror .anchor .anchorTargetStickyNavbar_Vzrq}

Closure that is invoked when an exception is raised while handling the `onNext` event. It will not make further calls to `onNext` or `onComplete`. The `onError` method takes as its parameter the `Throwable` that caused the error.

::: section
## sum[​](#sum "Direct link to sum"){.hash-link aria-label="Direct link to sum" translate="no"} {#sum .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: dataflow value*

The `sum` operator emits the sum of all items in a source channel:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 8, 6, 2, 5 )
    .sum()
    .view { result -> "The sum is $result" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
The sum is 21
```

An optional [closure](/nextflow/script#closures) can be used to transform each item before it is added to the sum:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 4, 1, 7, 5 )
    .sum { v -> v * v }
    .view { result -> "Square: $result" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
Square: 91
```

::: section
## take[​](#take "Direct link to take"){.hash-link aria-label="Direct link to take" translate="no"} {#take .anchor .anchorTargetStickyNavbar_Vzrq}

warning

This operator depends on the ordering of values in the source channel. It can lead to [non-deterministic behavior](/nextflow/cache-and-resume#non-deterministic-process-inputs) if used improperly.

*Returns: channel*

The `take` operator takes the first *N* items from a source channel:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 1..10 )
    .take( 3 )
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
1
2
3
```

tip

Specifying a size of `-1` causes the operator to take all values.

::: section
## tap[​](#tap "Direct link to tap"){.hash-link aria-label="Direct link to tap" translate="no"} {#tap .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: channel*

The `tap` operator assigns a source channel to a variable, and emits the source channel. It is a useful way to extract intermediate output channels from a chain of operators. For example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 'a', 'b', 'c' )
    .tap { log1 }
    .map { v -> v * 2 }
    .tap { log2 }
    .map { v -> v.toUpperCase() }
    .view { result -> "Result: $result" }

log1.view { v -> "Log 1: $v" }
log2.view { v -> "Log 2: $v" }
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
Result: AA
Result: BB
Result: CC
Log 1: a
Log 1: b
Log 1: c
Log 2: aa
Log 2: bb
Log 2: cc
```

tip

As a best practice, use a standard assignment (`=`) instead of `tap`. Standard assignments enable more effective [type checking](/nextflow/tutorials/static-types#preparing-for-static-typing).

::: section
## toList[​](#tolist "Direct link to toList"){.hash-link aria-label="Direct link to toList" translate="no"} {#tolist .anchor .anchorTargetStickyNavbar_Vzrq}

*Returns: dataflow value*

The `toList` operator collects all the items from a source channel into a list and emits the list as a single item:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
channel.of( 1, 2, 3, 4 )
    .toList()
    .view()
```

``` {.prism-code .language-console .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
[1, 2, 3, 4]
```

note

There are two main differences between `toList` and `collect`:

- When there is no input, `toList` emits an empty list whereas `collect` emits nothing.
- By default, `collect` flattens list items by one level.

In other words, `toList` is equivalent to:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
collect(flat: false).ifEmpty([])
```