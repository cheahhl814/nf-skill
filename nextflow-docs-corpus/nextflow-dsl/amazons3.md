# Amazon S3

Nextflow includes support for AWS S3 storage. Files stored in an S3 bucket can be accessed transparently in your pipeline script like any other file in the local file system.

::: section
## S3 path[​](#s3-path "Direct link to S3 path"){.hash-link aria-label="Direct link to S3 path" translate="no"} {#s3-path .anchor .anchorTargetStickyNavbar_Vzrq}

In order to access an S3 file, you only need to prefix the file path with the `s3` schema and the `bucket` name where it is stored.

For example, if you need to access the file `/data/sequences.fa` stored in a bucket named `my-bucket`, that file can be accessed using the following fully qualified path:

``` {.prism-code .language-text .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
s3://my-bucket/data/sequences.fa
```

The usual file operations can be applied to a path handle with the above notation. For example, the content of an S3 file can be printed as follows:

``` {.prism-code .language-nextflow .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
println file('s3://my-bucket/data/sequences.fa').text
```

See [Working with files](/nextflow/working-with-files) and the [Path](/nextflow/reference/stdlib-types/path) reference to learn more about available file operations.

::::: section
## Security credentials[​](#security-credentials "Direct link to Security credentials"){.hash-link aria-label="Direct link to Security credentials" translate="no"} {#security-credentials .anchor .anchorTargetStickyNavbar_Vzrq}

AWS access credentials can be provided in two ways:

1.  Using AWS access and secret keys in your pipeline configuration.
2.  Using IAM roles to grant access to S3 storage on AWS EC2 instances.

::: section
### AWS access and secret keys[​](#aws-access-and-secret-keys "Direct link to AWS access and secret keys"){.hash-link aria-label="Direct link to AWS access and secret keys" translate="no"} {#aws-access-and-secret-keys .anchor .anchorTargetStickyNavbar_Vzrq}

The AWS access and secret keys can be specified by using the `aws` section in the `nextflow.config` configuration file as shown below:

``` {.prism-code .language-groovy .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
aws {
    accessKey = '<aws-access-key>'
    secretKey = '<aws-secret-key>'
    region = '<aws-region-identifier>'
}
```

If the access credentials are not found in the above file, Nextflow looks for AWS credentials in the following order:

1.  The `nextflow.config` file in the pipeline execution directory
2.  The environment variables `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`
3.  The environment variables `AWS_ACCESS_KEY` and `AWS_SECRET_KEY`
4.  The profile in the AWS credentials file located at `~/.aws/credentials`
    - Uses the `default` profile or the environment variable `AWS_PROFILE` if set
5.  The profile in the AWS client configuration file located at `~/.aws/config`
    - Uses the `default` profile or the environment variable `AWS_PROFILE` if set
6.  The temporary AWS credentials provided by an IAM instance role. See [IAM Roles](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html){target="_blank" rel="noopener noreferrer"} documentation for details.

More information regarding [AWS Security Credentials](http://docs.aws.amazon.com/general/latest/gr/aws-security-credentials.html){target="_blank" rel="noopener noreferrer"} are available in the AWS documentation.

::: section
### IAM roles with AWS EC2 instances[​](#iam-roles-with-aws-ec2-instances "Direct link to IAM roles with AWS EC2 instances"){.hash-link aria-label="Direct link to IAM roles with AWS EC2 instances" translate="no"} {#iam-roles-with-aws-ec2-instances .anchor .anchorTargetStickyNavbar_Vzrq}

When running your pipeline in an EC2 instance, IAM roles can be used to grant access to AWS resources.

In this scenario, you only need to launch the EC2 instance with an IAM role which includes the `AmazonS3FullAccess` policy. Nextflow will detect and automatically acquire the permission to access S3 storage, without any further configuration.

Learn more about [Using IAM Roles to Delegate Permissions to Applications that Run on AWS EC2](http://docs.aws.amazon.com/IAM/latest/UserGuide/roles-usingrole-ec2instance.html){target="_blank" rel="noopener noreferrer"} in the AWS documentation.

::: section
## China regions[​](#china-regions "Direct link to China regions"){.hash-link aria-label="Direct link to China regions" translate="no"} {#china-regions .anchor .anchorTargetStickyNavbar_Vzrq}

To use an AWS China region, make sure to specify the corresponding AWS API S3 endpoint in the Nextflow configuration file as shown below:

``` {.prism-code .language-groovy .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
aws { 
    client {
        endpoint = "https://s3.cn-north-1.amazonaws.com.cn"        
    }
}
```

Read more about AWS API endpoints in the [AWS documentation](https://docs.aws.amazon.com/general/latest/gr/s3.html){target="_blank" rel="noopener noreferrer"}

::: section
## S3-compatible storage[​](#s3-compatible-storage "Direct link to S3-compatible storage"){.hash-link aria-label="Direct link to S3-compatible storage" translate="no"} {#s3-compatible-storage .anchor .anchorTargetStickyNavbar_Vzrq}

To use S3-compatible object storage such as [Ceph](https://ceph.io){target="_blank" rel="noopener noreferrer"} or [Minio](https://min.io){target="_blank" rel="noopener noreferrer"} specify the endpoint of your storage provider and enable the [S3 path style access](https://docs.aws.amazon.com/AmazonS3/latest/userguide/VirtualHosting.html#path-style-access){target="_blank" rel="noopener noreferrer"} in your Nextflow configuration as shown below:

``` {.prism-code .language-groovy .codeBlock_QsEB .thin-scrollbar tabindex="0" style="background-color:hsl(230, 1%, 98%);color:hsl(230, 8%, 24%)"}
aws {
    accessKey = '<aws-access-key>'
    secretKey = '<aws-secret-key>'
    client {
        endpoint = '<storage-endpoint-url>'
        s3PathStyleAccess = true
    }
}
```

::: section
## Advanced configuration[​](#advanced-configuration "Direct link to Advanced configuration"){.hash-link aria-label="Direct link to Advanced configuration" translate="no"} {#advanced-configuration .anchor .anchorTargetStickyNavbar_Vzrq}

See [AWS configuration](/nextflow/reference/config/aws) for more information about advanced S3 client configuration options.

:::: {.col .col--3}
::: {.text-[.7rem] .mb-1 .uppercase .text-primary .font-semibold}
On this Page

- [S3 path](#s3-path){.table-of-contents__link .toc-highlight}
- [Security credentials](#security-credentials){.table-of-contents__link .toc-highlight}
  - [AWS access and secret keys](#aws-access-and-secret-keys){.table-of-contents__link .toc-highlight}
  - [IAM roles with AWS EC2 instances](#iam-roles-with-aws-ec2-instances){.table-of-contents__link .toc-highlight}
- [China regions](#china-regions){.table-of-contents__link .toc-highlight}
- [S3-compatible storage](#s3-compatible-storage){.table-of-contents__link .toc-highlight}
- [Advanced configuration](#advanced-configuration){.table-of-contents__link .toc-highlight}
::::