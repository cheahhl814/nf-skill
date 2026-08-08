::::::::::::::: {.row astro-cid-bk4jreff=""}
::::::::: {.col-12 .col-xl-6 .order-2 astro-cid-bk4jreff=""}
:::::::: {.markdown-content .m-auto .me-xl-0 astro-transition-scope="astro-vlgjizik-1" astro-cid-bk4jreff=""}
::::::: {}
# Pipelines {#pipelines astro-cid-j55lipr2=""}

nf-core pipelines are end-to-end analysis workflows that combine multiple tools and processes to analyze biological data. These specifications define the standards for developing high-quality, reproducible pipelines that can be shared and maintained by the nf-core community.

The specifications are organized into **requirements** (mandatory standards all nf-core pipelines MUST follow) and **recommendations** (best practices pipelines SHOULD adopt where applicable). While these specifications are mandatory for pipelines contributed to the nf-core repository, they represent proven best practices for developing professional-grade Nextflow pipelines that can benefit any workflow development project.

::: {.title .mb-0 .alert .alert-success .fw-bold .px-3 .py-2 .d-flex .align-items-center .rounded-0 .border-0}
Tip
:::

::: {#admonition-0}
If you're thinking of adding a new pipeline to nf-core or contributing to an existing pipeline, see [adding a new pipeline](../../contributing/contribute-new-pipelines) and [contributing to a pipeline](../../contributing/contribute-existing-pipelines).
:::

::: {.title .mb-0 .alert .alert-warning .fw-bold .px-3 .py-2 .d-flex .align-items-center .rounded-0 .border-0}
Build with the community
:::

::: {#admonition-1}
nf-core is a community of builders, not a registry of pipelines. Propose your pipeline and develop with us, rather than proposing an already-complete pipeline. See recommendation: [Build with the community](recommendations/build_with_community)
:::

## Requirements[](#requirements)

The following specifications define mandatory standards that all nf-core pipelines MUST follow:

- **[Nextflow](requirements/nextflow):** Pipelines MUST be built using Nextflow and adhere to DSL2 syntax standards.
- **[Community owned](requirements/community_owned):** Pipelines are owned collectively by the community with contributions welcomed from all members.
- **[Identity and branding](requirements/identity_branding):** Primary development MUST occur within the nf-core GitHub organisation with appropriate branding.
- **[Workflow specificity](requirements/workflow_specificity):** Each data or analysis type MUST have only one canonical nf-core pipeline to avoid fragmentation.
- **[Workflow size](requirements/workflow_size):** Pipelines MUST have appropriate scope---comprehensive enough to be useful but focused enough to maintain.
- **[Workflow name](requirements/workflow_name):** Pipeline names MUST be lowercase, use hyphens for separation, and avoid punctuation.
- **[Use the template](requirements/use_the_template):** All pipelines MUST be built using the nf-core template and maintain synchronization through automated processes.
- **[Software license](requirements/mit_license):** Pipelines MUST be open source and released under the MIT license.
- **[Bundled documentation](requirements/documentation):** Comprehensive documentation MUST be bundled with the pipeline and hosted on the nf-core website.
- **[Docker support](requirements/docker):** Software dependencies MUST be containerized using Docker with specific version tags.
- **[Continuous integration testing](requirements/ci_testing):** Pipelines MUST implement automated CI tests to verify functionality.
- **[Semantic versioning](requirements/semantic_versioning):** Pipelines MUST follow semantic versioning with stable release tags.
- **[Standardised parameters](requirements/parameters):** Pipelines MUST use standardized parameter names and conventions for consistency across nf-core.
- **[Single command](requirements/single_command):** Pipelines MUST be executable with a single command without requiring manual intervention.
- **[Keywords](requirements/keywords):** Pipelines MUST have comprehensive documentation and appropriate GitHub repository keywords for discoverability.
- **[Pass lint tests](requirements/linting):** Pipelines MUST pass all `nf-core pipelines lint` tests without failures.
- **[Credits and Acknowledgements](requirements/acknowledgements):** Pipelines MUST properly acknowledge and credit prior work, tools, and contributors.
- **[Minimum inputs](requirements/minimum_inputs):** Pipelines MUST be designed to run with minimal required input, using sensible defaults.
- **[Use nf-core git branches](requirements/git_branches):** Pipelines MUST use standardized branch structure including `master`, `dev`, and `TEMPLATE`.

## Recommendations[](#recommendations)

The following specifications define best practices that all nf-core pipelines SHOULD follow where possible or appropriate:

- **[Use Bioconda](recommendations/bioconda):** Software dependencies SHOULD be packaged using Bioconda and distributed through BioContainers for reproducibility.
- **[File formats](recommendations/file_formats):** Pipelines SHOULD use community-accepted modern file formats such as CRAM for better compression and compatibility.
- **[Testing](recommendations/testing):** Pipelines SHOULD use nf-test to verify successful completion with valid outputs using minimal test datasets.
- **[DOIs](recommendations/dois):** Pipeline releases SHOULD be assigned digital object identifiers (DOIs) through Zenodo for citability.
- **[Cloud compatible](recommendations/cloud_compatible):** Pipelines SHOULD be tested on cloud computing environments to ensure portability.
- **[Publication credit](recommendations/publication_credit):** Pipeline publications SHOULD acknowledge the nf-core community and cite the nf-core framework paper.
- **[Build with the community](recommendations/build_with_community):** Pipeline development SHOULD occur collaboratively with the community from the beginning rather than proposing complete pipelines.
- **[Custom Docker images](recommendations/custom_containers):** Custom containers not available through BioContainers SHOULD be mirrored on quay.io for long-term reproducibility.
:::::::
::::::::
:::::::::

::::: {.col-12 .col-xl-3 .sidebar-right .toc .order-3 astro-cid-bk4jreff=""}
:::: {.nav .flex-column .sticky-top-under .align-items-end .pt-1 .svelte-1ah43h7}
::: {.d-none .d-xl-block .w-100}
:::
::::

[ Edit this page](https://github.com/nf-core/website/blob/main/sites/docs/src/content/docs/specifications/pipelines/overview.md){.text-small .text-body-secondary astro-cid-bk4jreff=""}
:::::

:::: {.col-12 .col-xl-3 .sticky-top-under .d-none .d-xl-inline .sidebar-left .order-1 astro-cid-bk4jreff=""}
::: {.nav .sticky-top-under .pt-1 .ms-auto astro-cid-2fjw7fep=""}
- ::: {.h6 .group-label .flex-grow-1 astro-cid-zhnod64e=""}
  [Get started]{.group-header .d-flex .align-items-start weight="1" astro-cid-zhnod64e=""}
  :::

  ::: {.hover-target astro-cid-zhnod64e=""}
  :::

  - [[What is nf-core?]{.section-link astro-cid-zhnod64e=""}](/docs/get_started/nf-core){.px-2 .py-2 aria-current="false" weight="1" astro-cid-zhnod64e=""}

  - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
    [Environment setup]{.group-header .d-flex .align-items-start weight="2" astro-cid-zhnod64e=""}
    :::

    ::: {.hover-target astro-cid-zhnod64e=""}
    :::

    - [[Overview]{.section-link astro-cid-zhnod64e=""}](/docs/get_started/environment_setup/overview){.px-2 .py-2 aria-current="false" weight="1" astro-cid-zhnod64e=""}
    - [[Nextflow]{.section-link astro-cid-zhnod64e=""}](/docs/get_started/environment_setup/nextflow){.px-2 .py-2 aria-current="false" weight="2" astro-cid-zhnod64e=""}
    - [[Software dependencies]{.section-link astro-cid-zhnod64e=""}](/docs/get_started/environment_setup/software-dependencies){.px-2 .py-2 aria-current="false" weight="3" astro-cid-zhnod64e=""}
    - [[ nf-core tools]{.section-link astro-cid-zhnod64e=""}](/docs/get_started/environment_setup/nf-core-tools){.px-2 .py-2 aria-current="false" weight="4" astro-cid-zhnod64e=""}
    - [[VS Code]{.section-link astro-cid-zhnod64e=""}](/docs/get_started/environment_setup/vs-code){.px-2 .py-2 aria-current="false" weight="5" astro-cid-zhnod64e=""}
    - [[Dev Containers]{.section-link astro-cid-zhnod64e=""}](/docs/get_started/environment_setup/dev-containers){.px-2 .py-2 aria-current="false" weight="6" astro-cid-zhnod64e=""}
    - [[Prettier]{.section-link astro-cid-zhnod64e=""}](/docs/get_started/environment_setup/prettier){.px-2 .py-2 aria-current="false" weight="10" astro-cid-zhnod64e=""}
    - [[Troubleshooting]{.section-link astro-cid-zhnod64e=""}](/docs/get_started/environment_setup/troubleshooting){.px-2 .py-2 aria-current="false" weight="11" astro-cid-zhnod64e=""}

  - [[Run your first pipeline]{.section-link astro-cid-zhnod64e=""}](/docs/get_started/run-your-first-pipeline){.px-2 .py-2 aria-current="false" weight="3" astro-cid-zhnod64e=""}

- ::: {.h6 .group-label .flex-grow-1 astro-cid-zhnod64e=""}
  [Running]{.group-header .d-flex .align-items-start weight="2" astro-cid-zhnod64e=""}
  :::

  ::: {.hover-target astro-cid-zhnod64e=""}
  :::

  - [[Overview]{.section-link astro-cid-zhnod64e=""}](/docs/running/overview){.px-2 .py-2 aria-current="false" weight="1" astro-cid-zhnod64e=""}

  - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
    [Advanced topics]{.group-header .d-flex .align-items-start astro-cid-zhnod64e=""}
    :::

    ::: {.hover-target astro-cid-zhnod64e=""}
    :::

    - [[Google Colab]{.section-link astro-cid-zhnod64e=""}](/docs/running/advanced-topics/google-colab){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Managing work directory growth]{.section-link astro-cid-zhnod64e=""}](/docs/running/advanced-topics/managing_work_directory_growth){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
    [Configuration]{.group-header .d-flex .align-items-start astro-cid-zhnod64e=""}
    :::

    ::: {.hover-target astro-cid-zhnod64e=""}
    :::

    - [[Configuration options]{.section-link astro-cid-zhnod64e=""}](/docs/running/configuration/configuration-options){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[System requirements]{.section-link astro-cid-zhnod64e=""}](/docs/running/configuration/nextflow-for-your-system){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Troubleshooting]{.section-link astro-cid-zhnod64e=""}](/docs/running/configuration/troubleshooting){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - [[Reference genomes]{.section-link astro-cid-zhnod64e=""}](/docs/running/reference-genomes){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - [[Running pipelines]{.section-link astro-cid-zhnod64e=""}](/docs/running/run-pipelines){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - [[Running pipelines offline]{.section-link astro-cid-zhnod64e=""}](/docs/running/run-pipelines-offline){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - [[Troubleshooting]{.section-link astro-cid-zhnod64e=""}](/docs/running/troubleshooting){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

- ::: {.h6 .group-label .flex-grow-1 astro-cid-zhnod64e=""}
  [Developing]{.group-header .d-flex .align-items-start weight="3" astro-cid-zhnod64e=""}
  :::

  ::: {.hover-target astro-cid-zhnod64e=""}
  :::

  - [[Overview]{.section-link astro-cid-zhnod64e=""}](/docs/developing/overview){.px-2 .py-2 aria-current="false" weight="1" astro-cid-zhnod64e=""}

  - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
    [Testing]{.group-header .d-flex .align-items-start weight="3" astro-cid-zhnod64e=""}
    :::

    ::: {.hover-target astro-cid-zhnod64e=""}
    :::

    - [[Overview]{.section-link astro-cid-zhnod64e=""}](/docs/developing/testing/overview){.px-2 .py-2 aria-current="false" weight="1" astro-cid-zhnod64e=""}
    - [[Assertions]{.section-link astro-cid-zhnod64e=""}](/docs/developing/testing/assertions){.px-2 .py-2 aria-current="false" weight="2" astro-cid-zhnod64e=""}
    - [[Advanced assertions]{.section-link astro-cid-zhnod64e=""}](/docs/developing/testing/advanced){.px-2 .py-2 aria-current="false" weight="3" astro-cid-zhnod64e=""}
    - [[CI runners]{.section-link astro-cid-zhnod64e=""}](/docs/developing/testing/ci-runners){.px-2 .py-2 aria-current="false" weight="4" astro-cid-zhnod64e=""}

  - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
    [Components]{.group-header .d-flex .align-items-start astro-cid-zhnod64e=""}
    :::

    ::: {.hover-target astro-cid-zhnod64e=""}
    :::

    - [[Automated methods]{.section-link astro-cid-zhnod64e=""}](/docs/developing/components/automated-methods-text){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Creating components]{.section-link astro-cid-zhnod64e=""}](/docs/developing/components/creating-components){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Cross organisational components]{.section-link astro-cid-zhnod64e=""}](/docs/developing/components/cross-org-components){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Deprecating modules]{.section-link astro-cid-zhnod64e=""}](/docs/developing/components/deprecating-components){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[ext arguments]{.section-link astro-cid-zhnod64e=""}](/docs/developing/components/ext-args){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[GPU modules]{.section-link astro-cid-zhnod64e=""}](/docs/developing/components/gpu-modules){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Meta maps]{.section-link astro-cid-zhnod64e=""}](/docs/developing/components/meta-map){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Setting custom remotes]{.section-link astro-cid-zhnod64e=""}](/docs/developing/components/custom-remotes){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
    [Containers]{.group-header .d-flex .align-items-start astro-cid-zhnod64e=""}
    :::

    ::: {.hover-target astro-cid-zhnod64e=""}
    :::

    - [[ARM64 on Bioconda]{.section-link astro-cid-zhnod64e=""}](/docs/developing/containers/arm64-on-bioconda){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Seqera Containers]{.section-link astro-cid-zhnod64e=""}](/docs/developing/containers/seqera-containers){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
    [Documentation]{.group-header .d-flex .align-items-start astro-cid-zhnod64e=""}
    :::

    ::: {.hover-target astro-cid-zhnod64e=""}
    :::

    - [[Harshil alignment]{.section-link astro-cid-zhnod64e=""}](/docs/developing/documentation/harshil-alignment){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Style guide]{.section-link astro-cid-zhnod64e=""}](/docs/developing/documentation/style-guide){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Topic types]{.section-link astro-cid-zhnod64e=""}](/docs/developing/documentation/topic-types){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Website Markdown]{.section-link astro-cid-zhnod64e=""}](/docs/developing/documentation/website-markdown){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - [[External use]{.section-link astro-cid-zhnod64e=""}](/docs/developing/external-use){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
    [Institutional profiles]{.group-header .d-flex .align-items-start astro-cid-zhnod64e=""}
    :::

    ::: {.hover-target astro-cid-zhnod64e=""}
    :::

    - [[Overview]{.section-link astro-cid-zhnod64e=""}](/docs/developing/institutional-profiles/overview){.px-2 .py-2 aria-current="false" weight="1" astro-cid-zhnod64e=""}
    - [[Configuration]{.section-link astro-cid-zhnod64e=""}](/docs/developing/institutional-profiles/configuration){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Documentation]{.section-link astro-cid-zhnod64e=""}](/docs/developing/institutional-profiles/documentation){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[File structure]{.section-link astro-cid-zhnod64e=""}](/docs/developing/institutional-profiles/file-structure){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[ nf-core/configs outside nf-core]{.section-link astro-cid-zhnod64e=""}](/docs/developing/institutional-profiles/outside-nf-core){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Preparing to write]{.section-link astro-cid-zhnod64e=""}](/docs/developing/institutional-profiles/preparing-to-write){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Testing]{.section-link astro-cid-zhnod64e=""}](/docs/developing/institutional-profiles/testing){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Troubleshooting]{.section-link astro-cid-zhnod64e=""}](/docs/developing/institutional-profiles/troubleshooting){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
    [Migration guides]{.group-header .d-flex .align-items-start astro-cid-zhnod64e=""}
    :::

    ::: {.hover-target astro-cid-zhnod64e=""}
    :::

    - [[Migrating to strict syntax]{.section-link astro-cid-zhnod64e=""}](/docs/developing/migration-guides/strict-syntax){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Migrating to topic channels]{.section-link astro-cid-zhnod64e=""}](/docs/developing/migration-guides/update-pipelines){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
    [Pipelines]{.group-header .d-flex .align-items-start astro-cid-zhnod64e=""}
    :::

    ::: {.hover-target astro-cid-zhnod64e=""}
    :::

    - [[Adding modules]{.section-link astro-cid-zhnod64e=""}](/docs/developing/pipelines/adding-modules){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Release procedure]{.section-link astro-cid-zhnod64e=""}](/docs/developing/pipelines/release-procedure){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Renaming branches]{.section-link astro-cid-zhnod64e=""}](/docs/developing/pipelines/master_to_main){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Template files]{.section-link astro-cid-zhnod64e=""}](/docs/developing/pipelines/template-files){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
    [Template syncs]{.group-header .d-flex .align-items-start astro-cid-zhnod64e=""}
    :::

    ::: {.hover-target astro-cid-zhnod64e=""}
    :::

    - [[Overview]{.section-link astro-cid-zhnod64e=""}](/docs/developing/template-syncs/overview){.px-2 .py-2 aria-current="false" weight="1" astro-cid-zhnod64e=""}
    - [[Fixing a broken TEMPLATE branch]{.section-link astro-cid-zhnod64e=""}](/docs/developing/template-syncs/fix-broken-template-branch){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Manually syncing your pipeline]{.section-link astro-cid-zhnod64e=""}](/docs/developing/template-syncs/manual-sync){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Merging automated PRs]{.section-link astro-cid-zhnod64e=""}](/docs/developing/template-syncs/merge-automated-pull-requests){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Setting up a pipeline sync retrospectively]{.section-link astro-cid-zhnod64e=""}](/docs/developing/template-syncs/set-up-pipeline-sync){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
    [Tutorials]{.group-header .d-flex .align-items-start astro-cid-zhnod64e=""}
    :::

    ::: {.hover-target astro-cid-zhnod64e=""}
    :::

    - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
      [Writing nf-core modules]{.group-header .d-flex .align-items-start astro-cid-zhnod64e=""}
      :::

      ::: {.hover-target astro-cid-zhnod64e=""}
      :::

      - [[Chapter 1: Introduction]{.section-link astro-cid-zhnod64e=""}](/docs/developing/tutorials/writing-nf-core-modules/1-introduction){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Chapter 2: Getting started]{.section-link astro-cid-zhnod64e=""}](/docs/developing/tutorials/writing-nf-core-modules/2-getting-started){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Chapter 3: What is a module?]{.section-link astro-cid-zhnod64e=""}](/docs/developing/tutorials/writing-nf-core-modules/3-what-is-a-module){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Chapter 4: Boilerplate files]{.section-link astro-cid-zhnod64e=""}](/docs/developing/tutorials/writing-nf-core-modules/4-boilerplate){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Chapter 5: Writing modules]{.section-link astro-cid-zhnod64e=""}](/docs/developing/tutorials/writing-nf-core-modules/5-writing-modules){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Chapter 6: Testing modules]{.section-link astro-cid-zhnod64e=""}](/docs/developing/tutorials/writing-nf-core-modules/6-testing){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Chapter 7: Development workflow]{.section-link astro-cid-zhnod64e=""}](/docs/developing/tutorials/writing-nf-core-modules/7-development){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Chapter 8: Using modules]{.section-link astro-cid-zhnod64e=""}](/docs/developing/tutorials/writing-nf-core-modules/8-using){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

- ::: {.h6 .group-label .flex-grow-1 astro-cid-zhnod64e=""}
  [Contributing]{.group-header .d-flex .align-items-start weight="4" astro-cid-zhnod64e=""}
  :::

  ::: {.hover-target astro-cid-zhnod64e=""}
  :::

  - [[Overview]{.section-link astro-cid-zhnod64e=""}](/docs/contributing/overview){.px-2 .py-2 aria-current="false" weight="1" astro-cid-zhnod64e=""}

  - [[How to contribute]{.section-link astro-cid-zhnod64e=""}](/docs/contributing/how-to-contribute){.px-2 .py-2 aria-current="false" weight="2" astro-cid-zhnod64e=""}

  - [[Components]{.section-link astro-cid-zhnod64e=""}](/docs/contributing/contribute-components){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - [[Contributor types]{.section-link astro-cid-zhnod64e=""}](/docs/contributing/contributor-types){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - [[Contributor\'s list]{.section-link astro-cid-zhnod64e=""}](/docs/contributing/contributors-list){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - [[Documentation]{.section-link astro-cid-zhnod64e=""}](/docs/contributing/documentation){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - [[Existing pipelines]{.section-link astro-cid-zhnod64e=""}](/docs/contributing/contribute-existing-pipelines){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - [[New pipelines]{.section-link astro-cid-zhnod64e=""}](/docs/contributing/contribute-new-pipelines){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - [[Project proposals]{.section-link astro-cid-zhnod64e=""}](/docs/contributing/project-proposals){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
    [Reviewing pull requests]{.group-header .d-flex .align-items-start astro-cid-zhnod64e=""}
    :::

    ::: {.hover-target astro-cid-zhnod64e=""}
    :::

    - [[nf-core bot]{.section-link astro-cid-zhnod64e=""}](/docs/contributing/reviewing-pull-requests/nf-core-bot){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

    - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
      [Review checklists]{.group-header .d-flex .align-items-start astro-cid-zhnod64e=""}
      :::

      ::: {.hover-target astro-cid-zhnod64e=""}
      :::

      - [[Reviewing components]{.section-link astro-cid-zhnod64e=""}](/docs/contributing/reviewing-pull-requests/review_checklists/component){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Reviewing configs]{.section-link astro-cid-zhnod64e=""}](/docs/contributing/reviewing-pull-requests/review_checklists/configs){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Reviewing nf-core/tools]{.section-link astro-cid-zhnod64e=""}](/docs/contributing/reviewing-pull-requests/review_checklists/nf-core-tools){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Reviewing pipeline releases]{.section-link astro-cid-zhnod64e=""}](/docs/contributing/reviewing-pull-requests/review_checklists/pipeline-release){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

- ::: {.h6 .group-label .flex-grow-1 astro-cid-zhnod64e=""}
  [Specifications]{.group-header .d-flex .align-items-start weight="5" astro-cid-zhnod64e=""}
  :::

  ::: {.hover-target astro-cid-zhnod64e=""}
  :::

  - [[Overview]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/overview){.px-2 .py-2 aria-current="false" weight="1" astro-cid-zhnod64e=""}

  - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
    [Components]{.group-header .d-flex .align-items-start weight="10" astro-cid-zhnod64e=""}
    :::

    ::: {.hover-target astro-cid-zhnod64e=""}
    :::

    - [[Overview]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/components/overview){.px-2 .py-2 aria-current="false" weight="1" astro-cid-zhnod64e=""}

    - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
      [Modules]{.group-header .d-flex .align-items-start weight="4" astro-cid-zhnod64e=""}
      :::

      ::: {.hover-target astro-cid-zhnod64e=""}
      :::

      - [[General]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/components/modules/general){.px-2 .py-2 aria-current="false" weight="1" astro-cid-zhnod64e=""}
      - [[Naming conventions]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/components/modules/naming-conventions){.px-2 .py-2 aria-current="false" weight="2" astro-cid-zhnod64e=""}
      - [[Input/output Options]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/components/modules/input-output-options){.px-2 .py-2 aria-current="false" weight="3" astro-cid-zhnod64e=""}
      - [[Documentation]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/components/modules/documentation){.px-2 .py-2 aria-current="false" weight="4" astro-cid-zhnod64e=""}
      - [[Parameters]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/components/modules/module-parameters){.px-2 .py-2 aria-current="false" weight="5" astro-cid-zhnod64e=""}
      - [[Resource requirements]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/components/modules/resource-requirements){.px-2 .py-2 aria-current="false" weight="6" astro-cid-zhnod64e=""}
      - [[Software]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/components/modules/software-requirements){.px-2 .py-2 aria-current="false" weight="7" astro-cid-zhnod64e=""}
      - [[Testing]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/components/modules/testing){.px-2 .py-2 aria-current="false" weight="8" astro-cid-zhnod64e=""}
      - [[Formatting]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/components/modules/formatting){.px-2 .py-2 aria-current="false" weight="9" astro-cid-zhnod64e=""}

    - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
      [Subworkflows]{.group-header .d-flex .align-items-start weight="5" astro-cid-zhnod64e=""}
      :::

      ::: {.hover-target astro-cid-zhnod64e=""}
      :::

      - [[General]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/components/subworkflows/general){.px-2 .py-2 aria-current="false" weight="1" astro-cid-zhnod64e=""}
      - [[Naming conventions]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/components/subworkflows/naming-conventions){.px-2 .py-2 aria-current="false" weight="2" astro-cid-zhnod64e=""}
      - [[Input output options]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/components/subworkflows/input-output-options){.px-2 .py-2 aria-current="false" weight="3" astro-cid-zhnod64e=""}
      - [[Subworkflow parameters]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/components/subworkflows/subworkflow-parameters){.px-2 .py-2 aria-current="false" weight="4" astro-cid-zhnod64e=""}
      - [[Documentation]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/components/subworkflows/documentation){.px-2 .py-2 aria-current="false" weight="5" astro-cid-zhnod64e=""}
      - [[Testing]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/components/subworkflows/testing){.px-2 .py-2 aria-current="false" weight="6" astro-cid-zhnod64e=""}
      - [[Formatting]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/components/subworkflows/formatting){.px-2 .py-2 aria-current="false" weight="7" astro-cid-zhnod64e=""}

  - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
    [Configs]{.group-header .d-flex .align-items-start weight="10" astro-cid-zhnod64e=""}
    :::

    ::: {.hover-target astro-cid-zhnod64e=""}
    :::

    - [[Documentation]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/configs/documentation){.px-2 .py-2 aria-current="false" weight="1" astro-cid-zhnod64e=""}
    - [[General]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/configs/general){.px-2 .py-2 aria-current="false" weight="1" astro-cid-zhnod64e=""}
    - [[Maintenance and deprecation]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/configs/maintenance-and-deprecation){.px-2 .py-2 aria-current="false" weight="1" astro-cid-zhnod64e=""}
    - [[Overview]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/configs/overview){.px-2 .py-2 aria-current="false" weight="1" astro-cid-zhnod64e=""}
    - [[Formatting]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/configs/formatting){.px-2 .py-2 aria-current="false" weight="9" astro-cid-zhnod64e=""}

  - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
    [Pipelines]{.group-header .d-flex .align-items-start weight="20" astro-cid-zhnod64e=""}
    :::

    ::: {.hover-target astro-cid-zhnod64e=""}
    :::

    - [[Overview]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/overview){.px-2 .py-2 aria-current="page" weight="1" astro-cid-zhnod64e=""}

    - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
      [Recommendations]{.group-header .d-flex .align-items-start astro-cid-zhnod64e=""}
      :::

      ::: {.hover-target astro-cid-zhnod64e=""}
      :::

      - [[Publication credit]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/recommendations/publication_credit){.px-2 .py-2 aria-current="false" weight="123" astro-cid-zhnod64e=""}
      - [[Testing]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/recommendations/testing){.px-2 .py-2 aria-current="false" weight="210" astro-cid-zhnod64e=""}
      - [[Bioconda]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/recommendations/bioconda){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Build with community]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/recommendations/build_with_community){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Cloud compatible]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/recommendations/cloud_compatible){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Custom containers]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/recommendations/custom_containers){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[DOIs]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/recommendations/dois){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[File formats]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/recommendations/file_formats){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

    - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
      [Requirements]{.group-header .d-flex .align-items-start astro-cid-zhnod64e=""}
      :::

      ::: {.hover-target astro-cid-zhnod64e=""}
      :::

      - [[Acknowledgements]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/requirements/acknowledgements){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[CI testing]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/requirements/ci_testing){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Community owned]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/requirements/community_owned){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Docker]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/requirements/docker){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Documentation]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/requirements/documentation){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Git branches]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/requirements/git_branches){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Identity branding]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/requirements/identity_branding){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Keywords]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/requirements/keywords){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Linting]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/requirements/linting){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Minimum inputs]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/requirements/minimum_inputs){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[MIT license]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/requirements/mit_license){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Nextflow]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/requirements/nextflow){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Parameters]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/requirements/parameters){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[RO Crate]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/requirements/ro_crate){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Semantic versioning]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/requirements/semantic_versioning){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Single command]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/requirements/single_command){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Use the template]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/requirements/use_the_template){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Workflow name]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/requirements/workflow_name){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Workflow size]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/requirements/workflow_size){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
      - [[Workflow specificity]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/requirements/workflow_specificity){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

    - [[Very important pipelines]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/pipelines/very_important_pipelines){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
    [Reviews]{.group-header .d-flex .align-items-start weight="30" astro-cid-zhnod64e=""}
    :::

    ::: {.hover-target astro-cid-zhnod64e=""}
    :::

    - [[Overview]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/reviews/overview){.px-2 .py-2 aria-current="false" weight="1" astro-cid-zhnod64e=""}
    - [[Request reviewers]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/reviews/request-reviewers){.px-2 .py-2 aria-current="false" weight="2" astro-cid-zhnod64e=""}
    - [[Rapid merge]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/reviews/rapid-merge){.px-2 .py-2 aria-current="false" weight="3" astro-cid-zhnod64e=""}
    - [[Review scope]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/reviews/review-scope){.px-2 .py-2 aria-current="false" weight="4" astro-cid-zhnod64e=""}
    - [[Commit strategy]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/reviews/commit-strategy){.px-2 .py-2 aria-current="false" weight="5" astro-cid-zhnod64e=""}

  - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
    [Test data]{.group-header .d-flex .align-items-start weight="40" astro-cid-zhnod64e=""}
    :::

    ::: {.hover-target astro-cid-zhnod64e=""}
    :::

    - [[Overview]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/test-data/overview){.px-2 .py-2 aria-current="false" weight="1" astro-cid-zhnod64e=""}
    - [[General]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/test-data/general){.px-2 .py-2 aria-current="false" weight="2" astro-cid-zhnod64e=""}
    - [[Modules]{.section-link astro-cid-zhnod64e=""}](/docs/specifications/test-data/modules){.px-2 .py-2 aria-current="false" weight="3" astro-cid-zhnod64e=""}

- ::: {.h6 .group-label .flex-grow-1 astro-cid-zhnod64e=""}
  [Community]{.group-header .d-flex .align-items-start weight="6" astro-cid-zhnod64e=""}
  :::

  ::: {.hover-target astro-cid-zhnod64e=""}
  :::

  - [[Overview]{.section-link astro-cid-zhnod64e=""}](/docs/community/overview){.px-2 .py-2 aria-current="false" weight="1" astro-cid-zhnod64e=""}

  - [[Advisories]{.section-link astro-cid-zhnod64e=""}](/docs/community/advisories){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
    [Brand]{.group-header .d-flex .align-items-start astro-cid-zhnod64e=""}
    :::

    ::: {.hover-target astro-cid-zhnod64e=""}
    :::

    - [[Overview]{.section-link astro-cid-zhnod64e=""}](/docs/community/brand/overview){.px-2 .py-2 aria-current="false" weight="1" astro-cid-zhnod64e=""}
    - [[Colours]{.section-link astro-cid-zhnod64e=""}](/docs/community/brand/colours){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Fonts]{.section-link astro-cid-zhnod64e=""}](/docs/community/brand/fonts){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Logos]{.section-link astro-cid-zhnod64e=""}](/docs/community/brand/logos){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Presentations]{.section-link astro-cid-zhnod64e=""}](/docs/community/brand/presentations){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Workflow schematics]{.section-link astro-cid-zhnod64e=""}](/docs/community/brand/workflow-schematics){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
    [Governance]{.group-header .d-flex .align-items-start astro-cid-zhnod64e=""}
    :::

    ::: {.hover-target astro-cid-zhnod64e=""}
    :::

    - [[Core team tasks]{.section-link astro-cid-zhnod64e=""}](/docs/community/governance/core-team){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Maintainers tasks]{.section-link astro-cid-zhnod64e=""}](/docs/community/governance/maintainers-team){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}
    - [[Teams]{.section-link astro-cid-zhnod64e=""}](/docs/community/governance/teams){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
    [Regulatory]{.group-header .d-flex .align-items-start astro-cid-zhnod64e=""}
    :::

    ::: {.hover-target astro-cid-zhnod64e=""}
    :::

    - [[Overview]{.section-link astro-cid-zhnod64e=""}](/docs/community/regulatory/overview){.px-2 .py-2 aria-current="false" weight="1" astro-cid-zhnod64e=""}
    - [[Checklist]{.section-link astro-cid-zhnod64e=""}](/docs/community/regulatory/checklist){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - [[Terminology]{.section-link astro-cid-zhnod64e=""}](/docs/community/terminology){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

  - [[Very important pipelines]{.section-link astro-cid-zhnod64e=""}](/docs/community/very-important-pipelines){.px-2 .py-2 aria-current="false" astro-cid-zhnod64e=""}

- ::: {.h6 .group-label .flex-grow-1 astro-cid-zhnod64e=""}
  [ nf-core/tools]{.group-header .d-flex .align-items-start weight="7" astro-cid-zhnod64e=""}
  :::

  ::: {.hover-target astro-cid-zhnod64e=""}
  :::

  - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
    [CLI]{.group-header .d-flex .align-items-start weight="1" astro-cid-zhnod64e=""}
    :::

    ::: {.hover-target astro-cid-zhnod64e=""}
    :::

    - [[Installation]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/installation){.px-2 .py-2 aria-current="false" weight="1" astro-cid-zhnod64e=""}

    - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
      [Pipelines]{.group-header .d-flex .align-items-start weight="10" astro-cid-zhnod64e=""}
      :::

      ::: {.hover-target astro-cid-zhnod64e=""}
      :::

      - [[create]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/pipelines/create){.px-2 .py-2 aria-current="false" weight="10" astro-cid-zhnod64e=""}
      - [[list]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/pipelines/list){.px-2 .py-2 aria-current="false" weight="10" astro-cid-zhnod64e=""}
      - [[launch]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/pipelines/launch){.px-2 .py-2 aria-current="false" weight="20" astro-cid-zhnod64e=""}
      - [[create params file]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/pipelines/create-params-file){.px-2 .py-2 aria-current="false" weight="30" astro-cid-zhnod64e=""}
      - [[download]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/pipelines/download){.px-2 .py-2 aria-current="false" weight="40" astro-cid-zhnod64e=""}
      - [[licences]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/pipelines/licences){.px-2 .py-2 aria-current="false" weight="50" astro-cid-zhnod64e=""}
      - [[rocrate]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/pipelines/rocrate){.px-2 .py-2 aria-current="false" weight="60" astro-cid-zhnod64e=""}
      - [[lint]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/pipelines/lint){.px-2 .py-2 aria-current="false" weight="70" astro-cid-zhnod64e=""}
      - [[schema]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/pipelines/schema){.px-2 .py-2 aria-current="false" weight="80" astro-cid-zhnod64e=""}
      - [[bump version]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/pipelines/bump-version){.px-2 .py-2 aria-current="false" weight="90" astro-cid-zhnod64e=""}
      - [[sync]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/pipelines/sync){.px-2 .py-2 aria-current="false" weight="100" astro-cid-zhnod64e=""}
      - [[create logo]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/pipelines/create-logo){.px-2 .py-2 aria-current="false" weight="110" astro-cid-zhnod64e=""}

    - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
      [Modules]{.group-header .d-flex .align-items-start weight="20" astro-cid-zhnod64e=""}
      :::

      ::: {.hover-target astro-cid-zhnod64e=""}
      :::

      - [[list]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/modules/list){.px-2 .py-2 aria-current="false" weight="10" astro-cid-zhnod64e=""}
      - [[info]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/modules/info){.px-2 .py-2 aria-current="false" weight="20" astro-cid-zhnod64e=""}
      - [[install]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/modules/install){.px-2 .py-2 aria-current="false" weight="30" astro-cid-zhnod64e=""}
      - [[update]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/modules/update){.px-2 .py-2 aria-current="false" weight="40" astro-cid-zhnod64e=""}
      - [[remove]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/modules/remove){.px-2 .py-2 aria-current="false" weight="50" astro-cid-zhnod64e=""}
      - [[patch]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/modules/patch){.px-2 .py-2 aria-current="false" weight="60" astro-cid-zhnod64e=""}
      - [[create]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/modules/create){.px-2 .py-2 aria-current="false" weight="70" astro-cid-zhnod64e=""}
      - [[lint]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/modules/lint){.px-2 .py-2 aria-current="false" weight="80" astro-cid-zhnod64e=""}
      - [[test]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/modules/test){.px-2 .py-2 aria-current="false" weight="90" astro-cid-zhnod64e=""}
      - [[bump versions]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/modules/bump-versions){.px-2 .py-2 aria-current="false" weight="100" astro-cid-zhnod64e=""}

    - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
      [Subworkflows]{.group-header .d-flex .align-items-start weight="30" astro-cid-zhnod64e=""}
      :::

      ::: {.hover-target astro-cid-zhnod64e=""}
      :::

      - [[list]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/subworkflows/list){.px-2 .py-2 aria-current="false" weight="10" astro-cid-zhnod64e=""}
      - [[info]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/subworkflows/info){.px-2 .py-2 aria-current="false" weight="20" astro-cid-zhnod64e=""}
      - [[install]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/subworkflows/install){.px-2 .py-2 aria-current="false" weight="30" astro-cid-zhnod64e=""}
      - [[update]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/subworkflows/update){.px-2 .py-2 aria-current="false" weight="40" astro-cid-zhnod64e=""}
      - [[remove]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/subworkflows/remove){.px-2 .py-2 aria-current="false" weight="50" astro-cid-zhnod64e=""}
      - [[create]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/subworkflows/create){.px-2 .py-2 aria-current="false" weight="60" astro-cid-zhnod64e=""}
      - [[lint]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/subworkflows/lint){.px-2 .py-2 aria-current="false" weight="70" astro-cid-zhnod64e=""}
      - [[test]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/subworkflows/test){.px-2 .py-2 aria-current="false" weight="80" astro-cid-zhnod64e=""}

    - ::: {.group-label .flex-grow-1 astro-cid-zhnod64e=""}
      [Test datasets]{.group-header .d-flex .align-items-start weight="30" astro-cid-zhnod64e=""}
      :::

      ::: {.hover-target astro-cid-zhnod64e=""}
      :::

      - [[list]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/test-datasets/list){.px-2 .py-2 aria-current="false" weight="10" astro-cid-zhnod64e=""}
      - [[list branches]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/test-datasets/list_branches){.px-2 .py-2 aria-current="false" weight="10" astro-cid-zhnod64e=""}
      - [[search]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/test-datasets/search){.px-2 .py-2 aria-current="false" weight="10" astro-cid-zhnod64e=""}

    - [[TUI]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/cli/tui){.px-2 .py-2 aria-current="false" weight="40" astro-cid-zhnod64e=""}

  - [[API]{.section-link astro-cid-zhnod64e=""}](/docs/nf-core-tools/api_reference){.px-2 .py-2 aria-current="false" weight="2" astro-cid-zhnod64e=""}
:::
::::
:::::::::::::::
