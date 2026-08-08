# Installation

Nextflow can be used on any POSIX-compatible system (Linux, macOS, etc), and on Windows through [WSL](https://en.wikipedia.org/wiki/Windows_Subsystem_for_Linux){target="_blank" rel="noopener noreferrer"}. This page describes how to install Nextflow.

note

New versions of Nextflow are released regularly. See [Updating Nextflow](/nextflow/updating-nextflow) for more information about Nextflow release cadence, how to update Nextflow, and how select your version of Nextflow.

::: section
## Requirements[​](#requirements "Direct link to Requirements"){.hash-link aria-label="Direct link to Requirements" translate="no"} {#requirements .anchor .anchorTargetStickyNavbar_Vzrq}

Nextflow requires Bash 3.2 (or later) and [Java 17 (or later, up to 26)](http://www.oracle.com/technetwork/java/javase/downloads/index.html){target="_blank" rel="noopener noreferrer"} to be installed. To see which version of Java you have, run the following command:

``` {.prism-code .language-bash .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
java -version
```

Deprecated in version 25.04

Support for Java versions prior to 17 was dropped.

If you don\'t have a compatible version of Java installed, it is recommended that you install it through [SDKMAN!](https://sdkman.io/){target="_blank" rel="noopener noreferrer"}, and that you use the latest Long-Term-Support (LTS) version of Temurin. See [Which version of JDK should I use?](https://whichjdk.com/){target="_blank" rel="noopener noreferrer"} for more information about different versions of Java.

To install Java with SDKMAN:

1.  [Install SDKMAN](https://sdkman.io/install){target="_blank" rel="noopener noreferrer"}:

    ``` {.prism-code .language-bash .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
    curl -s https://get.sdkman.io | bash
    ```

2.  Open a new terminal.

3.  Install Java:

    ``` {.prism-code .language-bash .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
    sdk install java 17.0.10-tem
    ```

4.  Confirm that Java is installed correctly:

    ``` {.prism-code .language-bash .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
    java -version
    ```

:::::: section
## Install Nextflow[​](#install-nextflow "Direct link to Install Nextflow"){.hash-link aria-label="Direct link to Install Nextflow" translate="no"} {#install-nextflow .anchor .anchorTargetStickyNavbar_Vzrq}

Nextflow is distributed as an easy to use self-installing package. It is also distributed via Conda and as a standalone distribution.

::: section
### Self-install[​](#self-install "Direct link to Self-install"){.hash-link aria-label="Direct link to Self-install" translate="no"} {#self-install .anchor .anchorTargetStickyNavbar_Vzrq}

To install Nextflow with the self-installing package:

1.  Download Nextflow:

    ``` {.prism-code .language-bash .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
    curl -s https://get.nextflow.io | bash
    ```

    warning
    Nextflow updates its executable during the self-install process. An update can fail if the executable is placed in a directory with restricted permissions.

2.  Make Nextflow executable:

    ``` {.prism-code .language-bash .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
    chmod +x nextflow
    ```

3.  Move Nextflow into an executable path. For example:

    ``` {.prism-code .language-bash .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
    mkdir -p $HOME/.local/bin/
    mv nextflow $HOME/.local/bin/
    ```

    tip
    Ensure the directory `$HOME/.local/bin/` is included in your `PATH` variable. Temporarily add this directory to `PATH` by setting `export PATH="$PATH:$HOME/.local/bin"`. Add the directory to `PATH` permanently by adding the export command to your shell configuration file, such as `~/.bashrc` or `~/.zshrc`. Alternatively, move the `nextflow` executable to a directory already in your `PATH`.

4.  Confirm Nextflow is installed correctly:

    ``` {.prism-code .language-bash .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
    nextflow info
    ```

::: section
### Conda[​](#conda "Direct link to Conda"){.hash-link aria-label="Direct link to Conda" translate="no"} {#conda .anchor .anchorTargetStickyNavbar_Vzrq}

To install Nextflow with Conda:

1.  Create an environment with Nextflow:

    ``` {.prism-code .language-bash .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
    conda create --name nf-env bioconda::nextflow
    ```

2.  Activate the environment:

    ``` {.prism-code .language-bash .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
    source activate nf-env
    ```

3.  Confirm Nextflow is installed correctly:

    ``` {.prism-code .language-bash .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
    nextflow info
    ```

warning

Installing Nextflow via Conda may lead to outdated versions, dependency conflicts, and Java compatibility issues. Using the self-installing package is recommended for a more reliable and up-to-date installation.

::: section
### Standalone distribution[​](#standalone-distribution "Direct link to Standalone distribution"){.hash-link aria-label="Direct link to Standalone distribution" translate="no"} {#standalone-distribution .anchor .anchorTargetStickyNavbar_Vzrq}

The Nextflow standalone distribution (i.e., the `dist` release) is a self-contained `nextflow` executable that can run without needing to download core dependencies at runtime. This distribution is useful for offline environments as well as building and testing Nextflow locally. The standalone distribution will still download core and third-party plugins at runtime.

To use the standalone distribution:

1.  Download the standalone distribution from Assets section of the [GitHub releases page](https://github.com/nextflow-io/nextflow/releases){target="_blank" rel="noopener noreferrer"}.

2.  Grant execution permissions to the downloaded file. For example:

    ``` {.prism-code .language-bash .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
    chmod +x nextflow-24.10.1-dist
    ```

3.  Use it as a drop-in replacement for `nextflow` command. For example:

    ``` {.prism-code .language-bash .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
    ./nextflow-24.10.1-dist run info
    ```

::: section
## Seqera Platform[​](#seqera-platform "Direct link to Seqera Platform"){.hash-link aria-label="Direct link to Seqera Platform" translate="no"} {#seqera-platform .anchor .anchorTargetStickyNavbar_Vzrq}

You can launch workflows directly from [Seqera Platform](https://seqera.io/platform/){target="_blank" rel="noopener noreferrer"} without installing Nextflow locally.

Launching from Seqera provides you with:

- User-friendly launch interfaces.
- Automated cloud infrastructure creation.
- Organizational user management.
- Advanced analytics with resource optimization.

Seqera Cloud Basic is free for small teams. Researchers at qualifying academic institutions can apply for free access to Seqera Cloud Pro. See [Seqera Platform Cloud](https://docs.seqera.io/platform){target="_blank" rel="noopener noreferrer"} to get started.

If you have installed Nextflow locally, you can use the [nextflow auth](/nextflow/reference/cli/auth) command to authenticate with Seqera and automatically configure workflow monitoring.

:::: {.col .col--3}
::: {.text-[.7rem] .mb-1 .uppercase .text-primary .font-semibold}
On this Page

- [Requirements](#requirements){.table-of-contents__link .toc-highlight}
- [Install Nextflow](#install-nextflow){.table-of-contents__link .toc-highlight}
  - [Self-install](#self-install){.table-of-contents__link .toc-highlight}
  - [Conda](#conda){.table-of-contents__link .toc-highlight}
  - [Standalone distribution](#standalone-distribution){.table-of-contents__link .toc-highlight}
- [Seqera Platform](#seqera-platform){.table-of-contents__link .toc-highlight}
::::