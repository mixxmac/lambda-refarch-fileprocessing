# AWS Lambda Reference Architecture: Real-time File Processing

The [Real-time File Processing](https://s3.amazonaws.com/awslambda-reference-architectures/file-processing/lambda-refarch-fileprocessing.pdf) reference architecture is an general-purpose event-driven parallel data processing architecture that utilizes [AWS Lambda](https://aws.amazon.com/lambda). This architecture is ideal for workloads that need more than one data derivative of an object. This simple architecture is described in the [Fanout S3 Event Notifications to
Multiple
Endpoints](https://aws.amazon.com/blogs/compute/fanout-s3-event-notifications-to-multiple-endpoints/) blog post and can be created with two AWS CloudFormation templates.

[Template
One](https://s3.amazonaws.com/awslambda-reference-architectures/file-processing/lambda_file_processing.template)
does the following:

-   Creates an Amazon Simple Storage Service (Amazon S3) bucket named event-manifold-bucket.

-   Creates an Amazon Simple Notification Service (Amazon SNS) topic named event-manifold-topic.

-   Creates an Amazon SNS topic policy which permits the S3 bucket to call the
    Publish action on the topic.

-   Creates a Lambda function named data-processor-1.

-   Creates a Lambda function named data-processor-2.

-   Creates an AWS Identity and Access Management (IAM) role and policy for data-processor-1 and data-processor-2 to assume when invoked. Permissions allow the
    functions to write output to Amazon CloudWatch Logs and get objects from
    the S3 bucket.

-   Creates an Add Permission Lambda function to execute the
    add-permission action on data-processor-1 and data-processor-2.
    The function permits Amazon SNS to call the InvokeFunction action on
    data-processor-1 and data-processor-2.

-   Creates an IAM role and policy for the Add Permission Lambda
    function to assume when invoked. Permissions allow the function to
    write output to CloudWatch Logs and execute add-permission on
    Lambda functions.

-   Creates two custom resources, each of which invoke the Add
    Permission Lambda function for data-processor-1 and data-processor-2.

[Template
Two](https://s3.amazonaws.com/awslambda-reference-architectures/file-processing/lambda_file_processing_update.template)
does the following:

-   Configures the S3 bucket to send notifications to the
    event-manifold-topic when objects are created.

## Instructions

Step 1 – Create an AWS CloudFormation Stack with Template One.

Step 2 – Update Template One with Template Two (Update Stack).

Step 3 – Navigate to the CloudWatch Logs tab.

Step 4 – Upload a file to the event-manifold-bucket, for example by using the AWS
Command Line Interface:

**aws s3 cp &lt;some file&gt; s3://event-manifold-bucket**

Step 5 – View the CloudWatch Log events for the data-processor-1 and
data-processor-2 Lambda functions for evidence that both functions
received the Amazon SNS message of the Amazon S3 event.

## Worth Noting

The Add Permissions Lambda function will send output to CloudWatch Logs
during Template One, showing the exchange between AWS CloudFormation and a
Lambda custom resource.

The Data Processor 1 and 2 Lambda functions will show the Amazon S3 test event
notification sent to the topic after Template Two.

## License

This reference architecture sample is licensed under Apache 2.0.
