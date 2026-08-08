# Sharing pipelines

Nextflow seamlessly integrates with popular Git providers, including [BitBucket](http://bitbucket.org/){target="_blank" rel="noopener noreferrer"}, [GitHub](http://github.com){target="_blank" rel="noopener noreferrer"}, and [GitLab](http://gitlab.com){target="_blank" rel="noopener noreferrer"} for managing Nextflow pipelines as version-controlled Git repositories. This feature allows you to easily use other people\'s Nextflow pipelines and publish your own pipelines.

note

Nextflow is not meant to completely replace the [Git](https://git-scm.com/){target="_blank" rel="noopener noreferrer"} tool. You may still need `git` to create new repositories or commit changes, etc.

::: section
## Git configuration[​](#git-configuration "Direct link to Git configuration"){.hash-link aria-label="Direct link to Git configuration" translate="no"} {#git-configuration .anchor .anchorTargetStickyNavbar_Vzrq}

You can configure your credentials for various Git providers in the Git configuration file, located at `$HOME/.nextflow/scm`. See [Git](/nextflow/git) for more information.

::: section
## Using a local repository[​](#using-a-local-repository "Direct link to Using a local repository"){.hash-link aria-label="Direct link to Using a local repository" translate="no"} {#using-a-local-repository .anchor .anchorTargetStickyNavbar_Vzrq}

Nextflow can work with repositories stored in a local or shared file system. The repository must be created as a [bare repository](https://craftquest.io/articles/what-is-a-bare-git-repository){target="_blank" rel="noopener noreferrer"}.

For example, given a bare repository at `/shared/projects/hello.git`, Nextflow is able to run it using the following syntax:

``` {.prism-code .language-bash .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
nextflow run file:/shared/projects/hello.git
```

See [Git documentation](https://git-scm.com/book/en/v2/Git-on-the-Server-Getting-Git-on-a-Server){target="_blank" rel="noopener noreferrer"} for more details about how to create and manage bare repositories.

::: section
## Publishing your pipeline[​](#publishing-your-pipeline "Direct link to Publishing your pipeline"){.hash-link aria-label="Direct link to Publishing your pipeline" translate="no"} {#publishing-your-pipeline .anchor .anchorTargetStickyNavbar_Vzrq}

In order to publish your Nextflow pipeline to GitHub (or any other supported platform) and allow other people to use it, you only need to create a GitHub repository containing all your project script and data files. If you don\'t know how to do it, follow this simple tutorial that explains how to [create a GitHub repository](https://help.github.com/articles/create-a-repo){target="_blank" rel="noopener noreferrer"}.

Nextflow only requires that the main script in your pipeline project is called `main.nf`. A different name can be used by specifying the `manifest.mainScript` attribute in the `nextflow.config` file that must be included in your project. For example:

``` {.prism-code .language-groovy .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
manifest.mainScript = 'my_very_long_script_name.nf'
```

To learn more about this and other project metadata information, that can be defined in the Nextflow configuration file, read the [Manifest](/nextflow/reference/config/manifest) section on the Nextflow configuration page.

Once you have uploaded your pipeline project to GitHub other people can execute it simply using the project name or the repository URL.

For example, if your GitHub account name is `acme` and you have uploaded a project into a repository named `hello` the repository URL will be `http://github.com/acme/hello` and people will be able to download and run it by using either the command:

``` {.prism-code .language-bash .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
nextflow run acme/hello
```

or

``` {.prism-code .language-bash .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
nextflow run http://github.com/acme/hello
```

See [Command line](/nextflow/cli) to learn how to use the Nextflow command line to run pipelines and manage pipeline projects.

:::::: section
## Managing dependencies[​](#managing-dependencies "Direct link to Managing dependencies"){.hash-link aria-label="Direct link to Managing dependencies" translate="no"} {#managing-dependencies .anchor .anchorTargetStickyNavbar_Vzrq}

Computational pipelines are rarely composed by a single script. In real world applications they depend on many other components, including other scripts and tools, databases, and specialized environments which provide compute and storage.

These external dependencies are the primary challenge when sharing software, because the users need to recreate the environment around a tool in order to use it. This setup process is often painful and error prone, which severely hinders the ability to reproduce computational results on a system other than the one on which it was originally developed.

Nextflow tackles this problem by integrating with existing tools for reproducible software, namely Git for source code and Docker for containers. These tools allow you to keep all the dependencies of your pipeline project in one place and track changes over time with version control.

By making your pipeline project self-contained, meaning all of its dependencies are fully defined in the project itself, you gain two major advantages:

- **Portability**: the pipeline can be run in virtually any environment with a Java VM and a container runtime
- **Reproducibility**: any results produced by the pipeline can be easily reproduced, even across different environments

One way to account for dependencies is to break them down into three categories: code, data, and environment. Here we will describe how to include each of these dependencies in your Nextflow pipeline:

::::: section
### Code[​](#code "Direct link to Code"){.hash-link aria-label="Direct link to Code" translate="no"} {#code .anchor .anchorTargetStickyNavbar_Vzrq}

Aside from pipeline scripts, you may have additional scripts and tools used by individual tasks.

::: section
#### Standard dependencies[​](#standard-dependencies "Direct link to Standard dependencies"){.hash-link aria-label="Direct link to Standard dependencies" translate="no"} {#standard-dependencies .anchor .anchorTargetStickyNavbar_Vzrq}

Many standard tools can be accessed as Docker containers or Conda/Spack packages. In this case, you only need to specify the container image URL (e.g. from [DockerHub](https://hub.docker.com){target="_blank" rel="noopener noreferrer"}) or package name with the `container` or `conda` directive, respectively.

Make sure to enable the desired method in your Nextflow configuration:

``` {.prism-code .language-groovy .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
// containers
docker.enabled = true

// conda packages
conda.enabled = true
```

This way, when you launch your pipeline, Nextflow will automatically download the necessary dependencies to run your tasks based on this configuration.

Read the [Containers](/nextflow/container) page to learn more about how to use containers with Nextflow, and the [Conda environments](/nextflow/conda) page for Conda packages.

tip

For maximal reproducibility, make sure to define a specific version for each tool. Otherwise, your pipeline might use different versions across subsequent runs, which can introduce subtle differences to your results.

::: section
#### The `bin` directory[​](#the-bin-directory "Direct link to the-bin-directory"){.hash-link aria-label="Direct link to the-bin-directory" translate="no"} {#the-bin-directory .anchor .anchorTargetStickyNavbar_Vzrq}

As for custom scripts, you can include executable scripts in the `bin` directory of your pipeline repository. When configured correctly, these scripts can be executed like a regular command from any process script (i.e. without modifying the `PATH` environment variable or using an absolute path), and changing the script will cause the task to be re-executed on a resumed run (i.e. just like changing the process script itself).

To configure a custom script:

1.  Save the script in the `bin` directory (relative to the pipeline repository root).
2.  Specify a portable shebang (see note below for details).
3.  Make the script executable. For example: `chmod a+x bin/my_script.py`

tip

To maximize the portability of your bundled script, use `env` to dynamically resolve the location of the interpreter instead of hard-coding it in the shebang line.

For example, shebang definitions `#!/usr/bin/python` and `#!/usr/local/bin/python` both hard-code specific paths to the Python interpreter. Instead, the following approach is more portable:

``` {.prism-code .language-bash .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
#!/usr/bin/env python
```