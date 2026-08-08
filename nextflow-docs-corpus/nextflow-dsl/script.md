# Scripts

Nextflow is a workflow language that runs on the Java virtual machine (JVM). Nextflow\'s syntax is very similar to [Groovy](https://groovy-lang.org/){target="_blank" rel="noopener noreferrer"}, a scripting language for the JVM. However, Nextflow is specialized for writing computational pipelines in a declarative manner.

This page is a practical introduction to the Nextflow language. See [Syntax](/nextflow/reference/syntax) for the language grammar, [Semantics](/nextflow/reference/semantics) for the meaning and behavior of each language construct, and [Standard library](/nextflow/reference/stdlib-types) for the built-in types and functions available to every script.

warning

Nextflow uses UTF-8 as the default character encoding for source files. Make sure to use UTF-8 encoding when editing Nextflow scripts with your preferred text editor.

warning

Nextflow scripts have a maximum size of 64 KiB. To avoid this limit for large pipelines, consider moving pipeline components into separate files and including them in the main script.

::: section
## Hello world[​](#hello-world "Direct link to Hello world"){.hash-link aria-label="Direct link to Hello world" translate="no"} {#hello-world .anchor .anchorTargetStickyNavbar_Vzrq}

You can use the `println` function to print to the console:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
println 'Hello, World!'
```

::: section
## Variables[​](#variables "Direct link to Variables"){.hash-link aria-label="Direct link to Variables" translate="no"} {#variables .anchor .anchorTargetStickyNavbar_Vzrq}

Variables are declared using the `def` keyword:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
def num = 1
def str = "Hi"
```

A variable can hold any type of value, and it can be reassigned:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
def x = 1
x = 'hello'
```

::: section
## Strings[​](#strings "Direct link to Strings"){.hash-link aria-label="Direct link to Strings" translate="no"} {#strings .anchor .anchorTargetStickyNavbar_Vzrq}

Strings can be defined using single or double quotes (`'` or `"` characters):

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
println "he said 'cheese' once"
println 'he said "cheese!" again'
```

Strings can be concatenated with `+`:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
def a = "world"
println "hello " + a
```

The important difference between single- and double-quoted strings is that double-quoted strings support *interpolation*: the value of a variable can be inserted by prefixing its name with `$`, and the value of any expression can be inserted with `${expression}`:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
def foxtype = 'quick'
def foxcolor = ['b', 'r', 'o', 'w', 'n']
println "The $foxtype ${foxcolor.join()} fox"
// prints: The quick brown fox

println '$foxtype is not interpolated'
// prints: $foxtype is not interpolated
```

A block of text that spans multiple lines can be defined by delimiting it with triple single or double quotes:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
def text = """
    hello there James
    how are you today?
    """
```

See [Strings](/nextflow/reference/semantics#strings) for more about string behavior, including multi-line strings and escaping.

::: section
## Lists[​](#lists "Direct link to Lists"){.hash-link aria-label="Direct link to Lists" translate="no"} {#lists .anchor .anchorTargetStickyNavbar_Vzrq}

Lists are defined using square brackets:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
def myList = [1776, -1, 33, 99, 0, 928734928763]
```

You can access an item in a list with square-bracket notation (indexes start at 0), and get the length of a list with the `size` method:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
println myList[0]
println myList.size()
```

See [List\<E\>](/nextflow/reference/stdlib-types/list) for the set of available list operations.

::: section
## Maps[​](#maps "Direct link to Maps"){.hash-link aria-label="Direct link to Maps" translate="no"} {#maps .anchor .anchorTargetStickyNavbar_Vzrq}

Maps are used to store *key-value pairs*. They are unordered collections of named values, which can each be of a different type:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
def scores = ["Brett": 100, "Pete": "Did not finish", "Andrew": 86.87934]
```

You can access the values in a map by key in two ways:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
println scores["Pete"]
println scores.Pete
```

To add or modify an entry, assign to a key:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
scores["Pete"] = 3
scores["Cedric"] = 120
```

tip

You can also use the `+` operator to add two maps together, which produces a new map rather than modifying either input. This is a safer way to modify maps when passing them through channels, because any references to the original map won\'t be affected. See [Maps](/nextflow/reference/semantics#maps) for details.

See [Map\<K,V\>](/nextflow/reference/stdlib-types/map) for the set of available map operations.

::: section
## Records and tuples[​](#records-and-tuples "Direct link to Records and tuples"){.hash-link aria-label="Direct link to Records and tuples" translate="no"} {#records-and-tuples .anchor .anchorTargetStickyNavbar_Vzrq}

*Records* store a set of related fields, where each field can have its own type. They are created with the `record` function and accessed by field name:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
def person = record(name: 'Alice', age: 42, is_alive: true)
println person.name
```

*Tuples* store a fixed sequence of heterogeneous values. They are created with the `tuple` function and accessed by index, and can be *destructured* in an assignment:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
def coords = tuple(1, 2)
def (x, y) = coords
println "x=$x, y=$y"
```

Records and tuples are both immutable \-- once created, they cannot be modified. See [Records](/nextflow/reference/semantics#records) and [Tuples](/nextflow/reference/semantics#tuples) for more information.

::: section
## Operators[​](#operators "Direct link to Operators"){.hash-link aria-label="Direct link to Operators" translate="no"} {#operators .anchor .anchorTargetStickyNavbar_Vzrq}

Operators are symbols that perform specific functions on one or more values. This section highlights some of the most commonly used operators. See [Operators](/nextflow/reference/semantics#operators) for the full set.

note

Operators in this context are different from *channel operators*, which are the methods used to work with channels. See [Dataflow](/nextflow/workflow#dataflow) for more information.

The `==` and `!=` operators test whether two values are equal (or not equal):

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
assert 2 + 2 == 4
assert [2, 2] != [4]
```

tip

The `assert` keyword tests a condition and raises an error if the condition is false. Every assert on this page succeeds when executed.

Comparison operators compare two values:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
assert 3 < 3.14             // numbers are compared as numbers
assert 'hello' < 'world'    // strings are compared alphabetically
```

Logical operators perform Boolean logic:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
assert (true && false) == false   // logical AND
assert (true || false) == true    // logical OR
assert (!true) == false           // logical NOT
```

The `in` operator tests *membership*, i.e., whether a collection contains a value:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
assert 2 in [1, 2, 3]
```

Arithmetic operators do math:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
assert 2 + 2 == 4
assert 2 * 3 == 6
assert 2 ** 8 == 256    // exponent
assert 7 % 2 == 1       // modulo (division remainder)
```

Some arithmetic operators can be used with other types of values. For example, `+` can also concatenate lists, maps, and strings:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
assert [1, 2, 3] + [4] == [1, 2, 3, 4]
```

::: section
## Conditional execution[​](#conditional-execution "Direct link to Conditional execution"){.hash-link aria-label="Direct link to Conditional execution" translate="no"} {#conditional-execution .anchor .anchorTargetStickyNavbar_Vzrq}

If-else statements can be used to execute different code under different conditions:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
def x = Math.random()
if( x < 0.5 ) {
    println 'You lost.'
}
else {
    println 'You won!'
}
```

In some cases, a conditional can be expressed more concisely as a conditional expression (also known as a *ternary expression*):

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
def message = Math.random() < 0.5
    ? 'You lost.'
    : 'You won!'
println message
```

A shortened version of the conditional expression, known as the *Elvis operator*, returns the first value if it is *truthy*, or falls back to a second value otherwise:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
def scores = ['A': 1, 'B': 2]
assert (scores['C'] ?: 0) == 0
```

::: section
## Regular expressions[​](#regular-expressions "Direct link to Regular expressions"){.hash-link aria-label="Direct link to Regular expressions" translate="no"} {#regular-expressions .anchor .anchorTargetStickyNavbar_Vzrq}

Regular expressions can be used to match and extract patterns from strings.

Use `=~` to check whether a pattern occurs anywhere in a string, and `==~` to check whether a string matches a pattern exactly:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
assert 'hello world' =~ /hello/
assert 'hello' ==~ /hello/
```

Use the `replaceFirst` and `replaceAll` methods to replace matching occurrences in a string:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
assert 'colour'.replaceFirst(/ou/, 'o') == 'color'
assert 'cheesecheese'.replaceAll(/cheese/, 'nice') == 'nicenice'
```

You can use the matcher object returned by `=~` to extract capture groups from a string:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
def version = '2.7.3-beta'
def m = version =~ /(\d+)\.(\d+)\.(\d+)-?(.+)/

assert m[0] == ['2.7.3-beta', '2', '7', '3', 'beta']
assert m[0][1] == '2'
assert m[0][2] == '7'
assert m[0][3] == '3'
assert m[0][4] == 'beta'
```

See [Regex operators](/nextflow/reference/semantics#regex-operators) for more information.

::: section
## Closures[​](#closures "Direct link to Closures"){.hash-link aria-label="Direct link to Closures" translate="no"} {#closures .anchor .anchorTargetStickyNavbar_Vzrq}

A closure is a function that can be used like a regular value. Typically, closures are passed as arguments to *higher-order functions* to express computations in a declarative manner.

For example, the following closure takes one parameter named `v` and returns the *square* of `v`:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
{ v -> v * v }
```

This closure can be used with the `collect` method of a list, which uses it to transform each value in the list and produce a new list:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
assert [1, 2, 3, 4].collect { v -> v * v } == [1, 4, 9, 16]
```

Another example is the `each` method of a map, which takes a closure with two parameters corresponding to the key and value of each map entry:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
["Yue": "Wu", "Mark": "Williams", "Sudha": "Kumari"].each { key, value ->
    println "$key $value"
}
```

Prints:

``` {.prism-code .language-text .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
Yue Wu
Mark Williams
Sudha Kumari
```

See [Closures](/nextflow/reference/semantics#closures) and [Iteration](/nextflow/reference/semantics#iteration) for more about closures and the higher-order functions used to process collections.

::: section
## Script definitions[​](#script-definitions "Direct link to Script definitions"){.hash-link aria-label="Direct link to Script definitions" translate="no"} {#script-definitions .anchor .anchorTargetStickyNavbar_Vzrq}

The previous sections covered the basic building blocks of Nextflow code: variables, strings, collections, and closures.

In practice, Nextflow scripts are composed of *workflows*, *processes*, *functions*, and *types* (collectively known as *definitions*), and can *include* definitions from other scripts.

To turn a code snippet into a workflow script, wrap it in a `workflow` block:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
workflow {
    println 'Hello!'
}
```

This block is called the *entry workflow*. It is the entrypoint when the script is executed. Whenever a script contains only simple statements like `println 'Hello!'`, Nextflow treats it as an entry workflow.

You can break up code into functions, for example:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
def sayHello() {
    println 'Hello!'
}

def add(a, b) {
    a + b
}

workflow {
    sayHello()
    println "2 + 2 = ${add(2, 2)}!"
}
```

You can organize these definitions further by moving them to a separate script and including them in the main script:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
// functions.nf
def sayHello() {
    println 'Hello!'
}

def add(a, b) {
    a + b
}
```

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
// main.nf
include { sayHello; add } from './functions.nf'

workflow {
    sayHello()
    println "2 + 2 = ${add(2, 2)}!"
}
```

See [Processes](/nextflow/process), [Workflows](/nextflow/workflow), and [Scripts](/nextflow/reference/semantics#scripts) for more information about how to use these features in your Nextflow scripts.

:::: {.col .col--3}
::: {.text-[.7rem] .mb-1 .uppercase .text-primary .font-semibold}
On this Page

- [Hello world](#hello-world){.table-of-contents__link .toc-highlight}
- [Variables](#variables){.table-of-contents__link .toc-highlight}
- [Strings](#strings){.table-of-contents__link .toc-highlight}
- [Lists](#lists){.table-of-contents__link .toc-highlight}
- [Maps](#maps){.table-of-contents__link .toc-highlight}
- [Records and tuples](#records-and-tuples){.table-of-contents__link .toc-highlight}
- [Operators](#operators){.table-of-contents__link .toc-highlight}
- [Conditional execution](#conditional-execution){.table-of-contents__link .toc-highlight}
- [Regular expressions](#regular-expressions){.table-of-contents__link .toc-highlight}
- [Closures](#closures){.table-of-contents__link .toc-highlight}
- [Script definitions](#script-definitions){.table-of-contents__link .toc-highlight}
::::