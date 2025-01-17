# Adding Jobs in AWS Glue<a name="add-job"></a>

An AWS Glue job encapsulates a script that connects to your source data, processes it, and then writes it out to your data target\. Typically, a job runs extract, transform, and load \(ETL\) scripts\. Jobs can also run general\-purpose Python scripts \(Python shell jobs\.\) AWS Glue *triggers* can start jobs based on a schedule or event, or on demand\. You can monitor job runs to understand runtime metrics such as completion status, duration, and start time\.

You can use scripts that AWS Glue generates or you can provide your own\. Given a source schema and target location or schema, the AWS Glue code generator can automatically create an Apache Spark API \(PySpark\) script\. You can use this script as a starting point and edit it to meet your goals\.

AWS Glue can write output files in several data formats, including JSON, CSV, ORC \(Optimized Row Columnar\), Apache Parquet, and Apache Avro\. For some data formats, common compression formats can be written\. 

There are three types of jobs in AWS Glue: *Spark*, *Streaming ETL*, and *Python shell*\.
+ A Spark job is run in an Apache Spark environment managed by AWS Glue\. It processes data in batches\.
+ A streaming ETL job is similar to a Spark job, except that it performs ETL on data streams\. It uses the Apache Spark Structured Streaming framework\. Some Spark job features are not available to streaming ETL jobs\.
+ A Python shell job runs Python scripts as a shell and supports a Python version that depends on the AWS Glue version you are using\. You can use these jobs to schedule and run tasks that don't require an Apache Spark environment\.

## Defining Job Properties for Spark Jobs<a name="create-job"></a>

When you define your job on the AWS Glue console, you provide values for properties to control the AWS Glue runtime environment\. 

The following list describes the properties of a Spark job\. For the properties of a Python shell job, see [Defining Job Properties for Python Shell Jobs](add-job-python.md#create-job-python-properties)\. For properties of a streaming ETL job, see [Defining Job Properties for a Streaming ETL Job](add-job-streaming.md#create-job-streaming-properties)\.

The properties are listed in the order in which they appear on the **Add job** wizard on AWS Glue console\.

**Name**  
Provide a UTF\-8 string with a maximum length of 255 characters\. 

**IAM role**  
Specify the IAM role that is used for authorization to resources used to run the job and access data stores\. For more information about permissions for running jobs in AWS Glue, see [Managing Access Permissions for AWS Glue Resources](access-control-overview.md)\.

**Type**  
Specify the type of job environment to run:  
+ Choose **Spark** to run an Apache Spark ETL script with the job command `glueetl`\.
+ Choose **Spark Streaming** to run a Apache Spark streaming ETL script with the job command `gluestreaming`\. For more information, see [Adding Streaming ETL Jobs in AWS Glue](add-job-streaming.md)\.
+ Choose **Python shell** to run a Python script with the job command `pythonshell`\. For more information, see [Adding Python Shell Jobs in AWS Glue](add-job-python.md)\.

**AWS Glue version**  
AWS Glue version determines the versions of Apache Spark and Python that are available to the job, as specified in the following table\.      
<a name="table-glue-versions"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/glue/latest/dg/add-job.html)
Jobs that were created without specifying a AWS Glue version default to AWS Glue 0\.9\.

**This job runs \(generated or custom script\)**  
The code in the ETL script defines your job's logic\. The script can be coded in Python or Scala\. You can choose whether the script that the job runs is generated by AWS Glue or provided by you\. You provide the script name and location in Amazon Simple Storage Service \(Amazon S3\)\. Confirm that there isn't a file with the same name as the script directory in the path\. To learn more about writing scripts, see [Editing Scripts in AWS Glue](edit-script.md)\.

**Scala class name**  
If the script is coded in Scala, you must provide a class name\. The default class name for AWS Glue generated scripts is **GlueApp**\.

**Temporary directory**  
Provide the location of a working directory in Amazon S3 where temporary intermediate results are written when AWS Glue runs the script\. Confirm that there isn't a file with the same name as the temporary directory in the path\. This directory is used when AWS Glue reads and writes to Amazon Redshift and by certain AWS Glue transforms\.

**Note**  
Source and target are not listed under the console **Details** tab for a job\. Review the script to see source and target details\.

**Advanced Properties**    
**Job bookmark**  
Specify how AWS Glue processes state information when the job runs\. You can have it remember previously processed data, update state information, or ignore state information\. For more information, see [Tracking Processed Data Using Job Bookmarks](monitor-continuations.md)\.

**Monitoring options**    
**Job metrics**  
Turn on or turn off the creation of Amazon CloudWatch metrics when this job runs\. To see profiling data, you must enable this option\. For more information about how to turn on and visualize metrics, see [Job Monitoring and Debugging](monitor-profile-glue-job-cloudwatch-metrics.md)\.   
**Continuous logging**  
Turn on continuous logging to Amazon CloudWatch\. If this option is not enabled, logs are available only after the job completes\. For more information, see [Continuous Logging for AWS Glue Jobs](monitor-continuous-logging.md)\.  
**Spark UI**  
Turn on the use of Spark UI for monitoring this job\. For more information, see [Enabling the Apache Spark Web UI for AWS Glue Jobs](monitor-spark-ui-jobs.md)\. 

**Tags**  
Tag your job with a **Tag key** and an optional **Tag value**\. After tag keys are created, they are read\-only\. Use tags on some resources to help you organize and identify them\. For more information, see [AWS Tags in AWS Glue](monitor-tags.md)\. 

**Security configuration, script libraries, and job parameters **    
**Security configuration**  
Choose a security configuration from the list\. A security configuration specifies how the data at the Amazon S3 target is encrypted: no encryption, server\-side encryption with AWS KMS\-managed keys \(SSE\-KMS\), or Amazon S3\-managed encryption keys \(SSE\-S3\)\.  
**Server\-side encryption**  
If you select this option, when the ETL job writes to Amazon S3, the data is encrypted at rest using SSE\-S3 encryption\. Both your Amazon S3 data target and any data that is written to an Amazon S3 temporary directory is encrypted\. This option is passed as a job parameter\. For more information, see [Protecting Data Using Server\-Side Encryption with Amazon S3\-Managed Encryption Keys \(SSE\-S3\)](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingServerSideEncryption.html) in the *Amazon Simple Storage Service Developer Guide*\.  
This option is ignored if a security configuration is specified\.  
**Python library path, Dependent jars path, and Referenced files path**  
Specify these options if your script requires them\. You can define the comma\-separated Amazon S3 paths for these options when you define the job\. You can override these paths when you run the job\. For more information, see [Providing Your Own Custom Scripts](console-custom-created.md)\.  
**Worker type**  
The following worker types are available:  
+ **Standard** – When you choose this type, you also provide a value for **Maximum capacity**\. Maximum capacity is the number of AWS Glue data processing units \(DPUs\) that can be allocated when this job runs\. A DPU is a relative measure of processing power that consists of 4 vCPUs of compute capacity and 16 GB of memory\. The **Standard** worker type has a 50 GB disk and 2 executors\. Each executor launches with 4 Spark cores, or a total of 8 Spark cores\.
+ **G\.1X** – When you choose this type, you also provide a value for **Number of workers**\. Each worker maps to 1 DPU and 1 executor, which launches with 8 Spark cores and 12228 MiB \(10 GiB\) of memory\. We recommend this worker type for memory\-intensive jobs\. This is the default **Worker type** for AWS Glue Version 2\.0 jobs\.
+ **G\.2X** – When you choose this type, you also provide a value for **Number of workers**\. Each worker maps to 2 DPUs and 1 executor, which launches with 16 Spark cores and 24576 MiB of memory\. We recommend this worker type for memory\-intensive jobs and jobs that run ML transforms\.
You are charged an hourly rate based on the number of DPUs used to run your ETL jobs\. For more information, see the [AWS Glue pricing page](https://aws.amazon.com/glue/pricing/)\.  
For AWS Glue version 1\.0 or earlier jobs, when you configure a job using the console and specify a **Worker type** of **Standard**, the **Maximum capacity** is set and the **Number of workers** becomes the value of **Maximum capacity** \- 1\. If you use the AWS Command Line Interface \(AWS CLI\) or AWS SDK, you can specify the **Max capacity** parameter, or you can specify both **Worker type** and the **Number of workers**\.  
For AWS Glue version 2\.0 jobs, you cannot instead specify a **Maximum capacity**\. Instead, you should specify a **Worker type** and the **Number of workers**\. For more information, see [Jobs](https://docs.aws.amazon.com/en_us/glue/latest/dg/aws-glue-api-jobs-job.html)\.  
**Number of workers**  
For G\.1X and G\.2X worker types, you must specify the number of workers that are allocated when the job runs\.   
The maximum number of workers you can define is 299 for `G.1X`, and 149 for `G.2X`\.  
**Maximum capacity**  
For AWS Glue version 1\.0 or earlier jobs, using the standard worker type, you must specify the maximum number of AWS Glue data processing units \(DPUs\) that can be allocated when this job runs\. You are charged an hourly rate based on the number of DPUs used to run your ETL jobs\. For more information, see the [AWS Glue pricing page](https://aws.amazon.com/glue/pricing/)\.    
Choose an integer from 2 to 100\. The default is 10\. This job type cannot have a fractional DPU allocation\.  
For AWS Glue version 2\.0 jobs, you cannot instead specify a **Maximum capacity**\. Instead, you should specify a **Worker type** and the **Number of workers**\. For more information, see [Jobs](https://docs.aws.amazon.com/en_us/glue/latest/dg/aws-glue-api-jobs-job.html)\.  
**Max concurrency**  
Sets the maximum number of concurrent runs that are allowed for this job\. The default is 1\. An error is returned when this threshold is reached\. The maximum value you can specify is controlled by a service limit\. For example, if a previous run of a job is still running when a new instance is started, you might want to return an error to prevent two instances of the same job from running concurrently\.   
**Job timeout**  
Sets the maximum execution time in minutes\. The default is 2880 minutes \(48 hours\) for batch jobs\. When the job execution time exceeds this limit, the job run state changes to `TIMEOUT`\.   
For streaming jobs that run indefinitely, leave the value blank, which is the default value for streaming jobs\.  
**Delay notification threshold**  
Sets the threshold \(in minutes\) before a delay notification is sent\. You can set this threshold to send notifications when a `RUNNING`, `STARTING`, or `STOPPING` job run takes more than an expected number of minutes\.  
**Number of retries**  
Specify the number of times, from 0 to 10, that AWS Glue should automatically restart the job if it fails\. Jobs that reach the timeout limit are not restarted\.  
**Job parameters**  
A set of key\-value pairs that are passed as named parameters to the script\. These are default values that are used when the script is run, but you can override them in triggers or when you run the job\. You must prefix the key name with `--`; for example: `--myKey`\. You pass job parameters as a map when using the AWS Command Line Interface\.  
For examples, see Python parameters in [Passing and Accessing Python Parameters in AWS Glue](aws-glue-programming-python-calling.md#aws-glue-programming-python-calling-parameters)\.  
**Non\-overrideable Job parameters**  
A set of special job parameters that cannot be overridden in triggers or when you run the job\. These key\-value pairs are passed as named parameters to the script\. These values are used when the script is run, and you cannot override them in triggers or when you run the job\. You must prefix the key name with `--`; for example: `--myKey`\. `getResolvedOptions()` returns both job parameters and non\-overrideable job parameters in a single map\. For more information, see [Accessing Parameters Using `getResolvedOptions`](aws-glue-api-crawler-pyspark-extensions-get-resolved-options.md)\.

**Catalog options**    
**Use AWS Glue data catalog as the Hive metastore**  
Select to use the AWS Glue Data Catalog as the Hive metastore\. The IAM role used for the job must have the `glue:CreateDatabase` permission\. A database called “default” is created in the Data Catalog if it does not exist\.

**Data source**  
Choose a Data Catalog table\.

**Transform type**  
Choose one of the following:    
**Change schema**  
Adds the `ApplyMapping` transform to the generated script\. Allows you to change the schema of the source data and create a new target dataset\.  
**Find matching records**  
Allows you to choose a machine learning transform to use to find matching records within your source data\.

**Target**  
Do one of the following:   
+ To specify an Amazon S3 path or JDBC data store, choose **Create tables in your data target**\.
+ To specify a catalog table, choose **Use tables in the data catalog and update your data target**\.
For Amazon S3 target locations, provide the location of a directory where your output is written\. Confirm that there isn't a file with the same name as the target path directory in the path\. For JDBC targets, AWS Glue creates schema objects as needed if the specified objects do not exist\.

 For more information about adding a job using the AWS Glue console, see [Working with Jobs on the AWS Glue Console](console-jobs.md)\. 