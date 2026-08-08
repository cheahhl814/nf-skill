# Executors

In Nextflow, the *executor* is the component that runs the tasks submitted by a pipeline.

The executor provides an abstraction between the pipeline definition (*what it computes*) and the underlying execution system (*how it runs*). You can write a pipeline once and run it on your local machine, a high-performance computing (HPC) cluster, or in the cloud by changing the executor in the Nextflow configuration.

Use [`process.executor`](/nextflow/reference/process#executor) to set the executor, e.g. `process.executor = 'slurm'`. Nextflow uses the `local` executor by default.

::: section
## Local[​](#local "Direct link to Local"){.hash-link aria-label="Direct link to Local" translate="no"} {#local .anchor .anchorTargetStickyNavbar_Vzrq}

Page

Name

Description

[Local](/nextflow/executor/local)

`local`

Runs tasks as operating system processes on the local machine.

::: section
## Cloud[​](#cloud "Direct link to Cloud"){.hash-link aria-label="Direct link to Cloud" translate="no"} {#cloud .anchor .anchorTargetStickyNavbar_Vzrq}

Page

Name

Description

[AWS Batch](/nextflow/executor/aws-batch)

`awsbatch`

Submits tasks to AWS Batch.

[Azure Batch](/nextflow/executor/azure-batch)

`azurebatch`

Submits tasks to Azure Batch.

[Google Cloud Batch](/nextflow/executor/google-batch)

`google-batch`

Submits tasks to Google Cloud Batch.

[Kubernetes](/nextflow/executor/kubernetes)

`k8s`

Submits tasks to a Kubernetes cluster.

[Seqera](/nextflow/executor/seqera)

`seqera`

Submits tasks to Seqera Intelligent Compute.

::: section
## HPC schedulers[​](#hpc-schedulers "Direct link to HPC schedulers"){.hash-link aria-label="Direct link to HPC schedulers" translate="no"} {#hpc-schedulers .anchor .anchorTargetStickyNavbar_Vzrq}

Page

Name

Description

[Bridge](/nextflow/executor/bridge)

`bridge`

Submits tasks through the Bridge abstraction layer.

[Flux](/nextflow/executor/flux)

`flux`

Submits tasks to the Flux Framework.

[HTCondor](/nextflow/executor/htcondor)

`condor`

Submits tasks to the HTCondor resource manager.

[HyperQueue](/nextflow/executor/hyperqueue)

`hq`

Submits tasks to the HyperQueue job scheduler.

[LSF](/nextflow/executor/lsf)

`lsf`

Submits tasks to a Platform LSF cluster.

[Moab](/nextflow/executor/moab)

`moab`

Submits tasks to the Moab resource manager.

[NQSII](/nextflow/executor/nqsii)

`nqsii`

Submits tasks to the NQSII resource manager.

[OAR](/nextflow/executor/oar)

`oar`

Submits tasks to the OAR resource manager.

[PBS/Torque](/nextflow/executor/pbs)

`pbs`

Submits tasks to a PBS/Torque scheduler.

[PBS Pro](/nextflow/executor/pbspro)

`pbspro`

Submits tasks to the PBS Pro resource manager.

[SGE](/nextflow/executor/sge)

`sge`

Submits tasks to an SGE cluster or compatible platform.

[SLURM](/nextflow/executor/slurm)

`slurm`

Submits tasks to the SLURM resource manager.

[TCS](/nextflow/executor/tcs)

`tcs`

Submits tasks to a Fujitsu TCS cluster.

:::: {.col .col--3}
::: {.text-[.7rem] .mb-1 .uppercase .text-primary .font-semibold}
On this Page

- [Local](#local){.table-of-contents__link .toc-highlight}
- [Cloud](#cloud){.table-of-contents__link .toc-highlight}
- [HPC schedulers](#hpc-schedulers){.table-of-contents__link .toc-highlight}
::::