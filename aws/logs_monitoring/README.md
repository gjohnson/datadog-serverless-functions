# Datadog-lambda


AWS lambda function to ship ELB, S3, CloudTrail, VPC, CloudFront and CloudWatch logs to Datadog

# Features

- Use AWS Lambda to re-route triggered S3 events to Datadog
- Cloudwatch, ELB, S3, CloudTrail, VPC and CloudFont logs can be forwarded
- SSL Security
- JSON events providing details about S3 documents forwarded
- Structured meta-information can be attached to the events
- Multiline Log Support (S3 Only)

# Quick Start

The provided Python script must be deployed into your AWS Lambda service. We will explain how in this step-by-step tutorial.

## 1. Create a new Lambda function

- Navigate to the Lambda console: https://console.aws.amazon.com/lambda/home and create a new function.
- Select `Author from scratch` and give the function a unique name.
- For `Role`, select `Create new role from template(s)` and give the role a unique name.
- Under Policy templates, search for and select `s3 object read-only permissions`.

## 2. Provide the code

- Copy paste the code of the Lambda function
- Set the runtime to `Python 2.7`
- Set the handler to `lambda_function.lambda_handler`


### Parameters

At the top of the script you'll find a section called `#Parameters`, that's where you want to edit your code.

```
#Parameters
ddApiKey = "<your_api_key>"
# metadata: Additional metadata to send with the logs
metadata = {
    "ddsourcecategory": "aws"
}
```

- **API key**:

There are 3 possibilities to set your Datadog's API key (available in your Datadog platform):

1. **KMS Encrypted key (recommended)**: Use the `DD_KMS_API_KEY` environment variable to use a KMS encrypted key. Make sure that the Lambda excution role is listed in the KMS Key user in https://console.aws.amazon.com/iam/home#encryptionKeys.
2. **Environment Variable**: Use the `DD_API_KEY` environment variable of the Lambda function
3. **Manual**: Replace `<your_api_key>` in the code:

- **(Optional) Metadata**:

You can optionally change the structured metadata. The metadata is merged to all the log events sent by the Lambda script.
Example: to add the environment value to your logs:

```
metadata = {
    "ddsourcecategory": "aws",
    "env": "prod",
}
```

- **(Optional) Custom Tags**

You have two options to add custom tags to your logs:

- Manually by editing the lambda code [there](https://github.com/DataDog/datadog-serverless-functions/blob/master/aws/logs_monitoring/lambda_function.py#L372-#L377).
- Automatically with the `DD_TAGS` environment variable (tags must be a comma-separated list of strings).

## 3. (optional) Send logs to EU or to a proxy

### Send logs to EU

Set the environment variable `DD_SITE` to `datadoghq.eu` and logs are automatically forwarded to your EU platform.

### Send logs through a proxy

Two environment variables can be used to forward logs through a proxy:

- `DD_URL`: Define the proxy endpoint to forward the logs to
- `DD_PORT`: Define the proxy port to forward the logs to

## 4. Configuration

- Set the memory to the highest possible value.
- Set also the timeout limit. We recommends 120 seconds to deal with big files.
- Hit the `Save and Test` button.

## 5. Testing it

If the test "succeeded", you are all set! The test log will not show up in the platform.

For S3 logs, there may be some latency between the time a first S3 log file is posted and the Lambda function wakes up.

## 6. (optional) Multiline Log support for s3

If there are multiline logs in s3, set `DD_MULTILINE_LOG_REGEX_PATTERN` environment variable to the specified regex pattern to detect for a new log line.

- Example: for multiline logs beginning with pattern `11/10/2014`: `DD_MULTILINE_LOG_REGEX_PATTERN="\d{2}\/\d{2}\/\d{4}"`
