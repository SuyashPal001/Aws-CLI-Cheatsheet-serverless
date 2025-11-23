
# AWS Serverless CLI Cheat Sheet

Complete reference for AWS CLI commands focused on serverless architecture and common AWS services.

---

## Lambda

List all functions

```bash
aws lambda list-functions
```

List functions with specific details

```bash
aws lambda list-functions \
  --query 'Functions[*].[FunctionName,Runtime,LastModified]' \
  --output table
```

Get function configuration

```bash
aws lambda get-function-configuration --function-name <function-name>
```

Get function details including code location

```bash
aws lambda get-function --function-name <function-name>
```

Invoke function synchronously

```bash
aws lambda invoke \
  --function-name <function-name> \
  --payload '{"key":"value"}' \
  response.json
```

Invoke function with logs

```bash
aws lambda invoke \
  --function-name <function-name> \
  --log-type Tail \
  --payload '{"key":"value"}' \
  response.json
```

Update function code from zip file

```bash
aws lambda update-function-code \
  --function-name <function-name> \
  --zip-file fileb://function.zip
```

Update function code from S3

```bash
aws lambda update-function-code \
  --function-name <function-name> \
  --s3-bucket <bucket-name> \
  --s3-key <path/to/function.zip>
```

Update function configuration

```bash
aws lambda update-function-configuration \
  --function-name <function-name> \
  --timeout 30 \
  --memory-size 512
```

Update environment variables

```bash
aws lambda update-function-configuration \
  --function-name <function-name> \
  --environment Variables={KEY1=value1,KEY2=value2}
```

Delete function

```bash
aws lambda delete-function --function-name <function-name>
```

List Lambda layers

```bash
aws lambda list-layers
```

Publish new layer version

```bash
aws lambda publish-layer-version \
  --layer-name <layer-name> \
  --zip-file fileb://layer.zip \
  --compatible-runtimes python3.9 nodejs18.x
```

---

## API Gateway

List all REST APIs

```bash
aws apigateway get-rest-apis
```

List REST APIs with names and IDs

```bash
aws apigateway get-rest-apis \
  --query 'items[*].[name,id]' \
  --output table
```

Get specific REST API details

```bash
aws apigateway get-rest-api --rest-api-id <api-id>
```

List all resources for an API

```bash
aws apigateway get-resources --rest-api-id <api-id>
```

List deployment stages

```bash
aws apigateway get-stages --rest-api-id <api-id>
```

Get stage details

```bash
aws apigateway get-stage \
  --rest-api-id <api-id> \
  --stage-name <stage-name>
```

Create deployment

```bash
aws apigateway create-deployment \
  --rest-api-id <api-id> \
  --stage-name <stage-name>
```

Delete REST API

```bash
aws apigateway delete-rest-api --rest-api-id <api-id>
```

Get API keys

```bash
aws apigateway get-api-keys
```

Get API key with value

```bash
aws apigateway get-api-key \
  --api-key <key-id> \
  --include-value
```

### API Gateway v2 (HTTP APIs)

List HTTP APIs

```bash
aws apigatewayv2 get-apis
```

Get HTTP API details

```bash
aws apigatewayv2 get-api --api-id <api-id>
```

List routes

```bash
aws apigatewayv2 get-routes --api-id <api-id>
```

List stages

```bash
aws apigatewayv2 get-stages --api-id <api-id>
```

---

## SQS

List all queues

```bash
aws sqs list-queues
```

List queues with prefix

```bash
aws sqs list-queues --queue-name-prefix <prefix>
```

Get queue URL

```bash
aws sqs get-queue-url --queue-name <queue-name>
```

Get queue attributes

```bash
aws sqs get-queue-attributes \
  --queue-url <queue-url> \
  --attribute-names All
```

Send message

```bash
aws sqs send-message \
  --queue-url <queue-url> \
  --message-body "Your message here"
```

Send message with attributes

```bash
aws sqs send-message \
  --queue-url <queue-url> \
  --message-body "Message" \
  --message-attributes '{"Key":{"StringValue":"Value","DataType":"String"}}'
```

Receive messages

```bash
aws sqs receive-message --queue-url <queue-url>
```

Receive multiple messages with long polling

```bash
aws sqs receive-message \
  --queue-url <queue-url> \
  --max-number-of-messages 10 \
  --wait-time-seconds 20
```

Delete message

```bash
aws sqs delete-message \
  --queue-url <queue-url> \
  --receipt-handle <receipt-handle>
```

Purge queue

```bash
aws sqs purge-queue --queue-url <queue-url>
```

Create standard queue

```bash
aws sqs create-queue --queue-name <queue-name>
```

Create FIFO queue

```bash
aws sqs create-queue \
  --queue-name <queue-name>.fifo \
  --attributes FifoQueue=true,ContentBasedDeduplication=true
```

Delete queue

```bash
aws sqs delete-queue --queue-url <queue-url>
```

Configure dead letter queue

```bash
aws sqs set-queue-attributes \
  --queue-url <queue-url> \
  --attributes RedrivePolicy='{"deadLetterTargetArn":"<dlq-arn>","maxReceiveCount":"3"}'
```

---

## SNS

List all topics

```bash
aws sns list-topics
```

List topic ARNs

```bash
aws sns list-topics \
  --query 'Topics[*].TopicArn' \
  --output table
```

Create standard topic

```bash
aws sns create-topic --name <topic-name>
```

Create FIFO topic

```bash
aws sns create-topic \
  --name <topic-name>.fifo \
  --attributes FifoTopic=true,ContentBasedDeduplication=true
```

Get topic attributes

```bash
aws sns get-topic-attributes --topic-arn <topic-arn>
```

Subscribe to topic (Email)

```bash
aws sns subscribe \
  --topic-arn <topic-arn> \
  --protocol email \
  --notification-endpoint <email@example.com>
```

Subscribe to topic (SQS)

```bash
aws sns subscribe \
  --topic-arn <topic-arn> \
  --protocol sqs \
  --notification-endpoint <queue-arn>
```

Subscribe to topic (Lambda)

```bash
aws sns subscribe \
  --topic-arn <topic-arn> \
  --protocol lambda \
  --notification-endpoint <lambda-arn>
```

List all subscriptions

```bash
aws sns list-subscriptions
```

List subscriptions by topic

```bash
aws sns list-subscriptions-by-topic --topic-arn <topic-arn>
```

Publish message

```bash
aws sns publish \
  --topic-arn <topic-arn> \
  --message "Your message here"
```

Publish message with subject

```bash
aws sns publish \
  --topic-arn <topic-arn> \
  --message "Message body" \
  --subject "Subject line"
```

Publish message from file

```bash
aws sns publish \
  --topic-arn <topic-arn> \
  --message file://message.txt
```

Publish with message structure (different formats per protocol)

```bash
aws sns publish \
  --topic-arn <topic-arn> \
  --message '{"default":"Default message","sms":"SMS message","email":"Email message"}' \
  --message-structure json
```

Unsubscribe

```bash
aws sns unsubscribe --subscription-arn <subscription-arn>
```

Delete topic

```bash
aws sns delete-topic --topic-arn <topic-arn>
```

Set topic display name

```bash
aws sns set-topic-attributes \
  --topic-arn <topic-arn> \
  --attribute-name DisplayName \
  --attribute-value "Display Name"
```

---

## SES

Verify email address

```bash
aws ses verify-email-identity --email-address <email@example.com>
```

List verified identities

```bash
aws ses list-identities
```

List verified email addresses

```bash
aws ses list-verified-email-addresses
```

Send email

```bash
aws ses send-email \
  --from <sender@example.com> \
  --to <recipient@example.com> \
  --subject "Subject" \
  --text "Email body"
```

Send email to multiple recipients

```bash
aws ses send-email \
  --from <sender@example.com> \
  --destination ToAddresses=<email1@example.com>,<email2@example.com> \
  --message Subject={Data="Subject"},Body={Text={Data="Body"}}
```

Send raw email

```bash
aws ses send-raw-email --raw-message Data=file://message.txt
```

Get send statistics

```bash
aws ses get-send-statistics
```

Delete verified email

```bash
aws ses delete-identity --identity <email@example.com>
```

---

## DynamoDB

List all tables

```bash
aws dynamodb list-tables
```

List table names

```bash
aws dynamodb list-tables \
  --query 'TableNames' \
  --output table
```

Describe table

```bash
aws dynamodb describe-table --table-name <table-name>
```

Describe table with specific attributes

```bash
aws dynamodb describe-table \
  --table-name <table-name> \
  --query 'Table.[TableName,ItemCount,TableSizeBytes]'
```

Create table

```bash
aws dynamodb create-table \
  --table-name <table-name> \
  --attribute-definitions AttributeName=id,AttributeType=S \
  --key-schema AttributeName=id,KeyType=HASH \
  --billing-mode PAY_PER_REQUEST
```

Put item

```bash
aws dynamodb put-item \
  --table-name <table-name> \
  --item '{"id":{"S":"123"},"name":{"S":"John"},"age":{"N":"30"}}'
```

Get item

```bash
aws dynamodb get-item \
  --table-name <table-name> \
  --key '{"id":{"S":"123"}}'
```

Query table

```bash
aws dynamodb query \
  --table-name <table-name> \
  --key-condition-expression "id = :v1" \
  --expression-attribute-values '{":v1":{"S":"123"}}'
```

Scan table

```bash
aws dynamodb scan --table-name <table-name>
```

Scan with filter

```bash
aws dynamodb scan \
  --table-name <table-name> \
  --filter-expression "age > :val" \
  --expression-attribute-values '{":val":{"N":"25"}}'
```

Update item

```bash
aws dynamodb update-item \
  --table-name <table-name> \
  --key '{"id":{"S":"123"}}' \
  --update-expression "SET #n = :val" \
  --expression-attribute-names '{"#n":"name"}' \
  --expression-attribute-values '{":val":{"S":"Jane"}}'
```

Delete item

```bash
aws dynamodb delete-item \
  --table-name <table-name> \
  --key '{"id":{"S":"123"}}'
```

Delete table

```bash
aws dynamodb delete-table --table-name <table-name>
```

Batch get items

```bash
aws dynamodb batch-get-item --request-items file://request.json
```

Batch write items

```bash
aws dynamodb batch-write-item --request-items file://request.json
```

---

## EventBridge (CloudWatch Events)

List all rules

```bash
aws events list-rules
```

List rules with prefix

```bash
aws events list-rules --name-prefix <prefix>
```

Describe rule

```bash
aws events describe-rule --name <rule-name>
```

Create schedule rule

```bash
aws events put-rule \
  --name <rule-name> \
  --schedule-expression "rate(5 minutes)"
```

Create event pattern rule

```bash
aws events put-rule \
  --name <rule-name> \
  --event-pattern '{"source":["aws.ec2"],"detail-type":["EC2 Instance State-change Notification"]}'
```

Add target to rule

```bash
aws events put-targets \
  --rule <rule-name> \
  --targets Id=1,Arn=<lambda-arn>
```

List targets for rule

```bash
aws events list-targets-by-rule --rule <rule-name>
```

Remove target from rule

```bash
aws events remove-targets \
  --rule <rule-name> \
  --ids 1
```

Delete rule

```bash
aws events delete-rule --name <rule-name>
```

List event buses

```bash
aws events list-event-buses
```

Put custom events

```bash
aws events put-events \
  --entries '[{"Source":"my.application","DetailType":"myEvent","Detail":"{\"key\":\"value\"}"}]'
```

---

## Step Functions

List state machines

```bash
aws stepfunctions list-state-machines
```

Describe state machine

```bash
aws stepfunctions describe-state-machine \
  --state-machine-arn <state-machine-arn>
```

Start execution

```bash
aws stepfunctions start-execution \
  --state-machine-arn <state-machine-arn> \
  --input '{"key":"value"}'
```

Start execution with name

```bash
aws stepfunctions start-execution \
  --state-machine-arn <state-machine-arn> \
  --name <execution-name> \
  --input file://input.json
```

List executions

```bash
aws stepfunctions list-executions \
  --state-machine-arn <state-machine-arn>
```

List running executions

```bash
aws stepfunctions list-executions \
  --state-machine-arn <state-machine-arn> \
  --status-filter RUNNING
```

Describe execution

```bash
aws stepfunctions describe-execution \
  --execution-arn <execution-arn>
```

Get execution history

```bash
aws stepfunctions get-execution-history \
  --execution-arn <execution-arn>
```

Stop execution

```bash
aws stepfunctions stop-execution \
  --execution-arn <execution-arn>
```

Create state machine

```bash
aws stepfunctions create-state-machine \
  --name <state-machine-name> \
  --definition file://definition.json \
  --role-arn <role-arn>
```

Update state machine

```bash
aws stepfunctions update-state-machine \
  --state-machine-arn <state-machine-arn> \
  --definition file://definition.json
```

Delete state machine

```bash
aws stepfunctions delete-state-machine \
  --state-machine-arn <state-machine-arn>
```

---

## CloudWatch Logs

List log groups

```bash
aws logs describe-log-groups
```

List log groups with prefix

```bash
aws logs describe-log-groups \
  --log-group-name-prefix /aws/lambda/
```

List log streams

```bash
aws logs describe-log-streams \
  --log-group-name <log-group-name>
```

Tail logs in real-time

```bash
aws logs tail <log-group-name> --follow
```

Tail logs from last hour

```bash
aws logs tail <log-group-name> --since 1h --follow
```

Filter log events

```bash
aws logs filter-log-events \
  --log-group-name <log-group-name> \
  --filter-pattern "ERROR"
```

Filter logs by time

```bash
aws logs filter-log-events \
  --log-group-name <log-group-name> \
  --start-time $(date -d '1 hour ago' +%s)000
```

Create log group

```bash
aws logs create-log-group --log-group-name <log-group-name>
```

Delete log group

```bash
aws logs delete-log-group --log-group-name <log-group-name>
```

Put log events

```bash
aws logs put-log-events \
  --log-group-name <log-group-name> \
  --log-stream-name <log-stream-name> \
  --log-events timestamp=1234567890000,message="Log message"
```

---

## S3

List all buckets

```bash
aws s3 ls
```

List bucket contents

```bash
aws s3 ls s3://<bucket-name>/
```

List bucket contents recursively

```bash
aws s3 ls s3://<bucket-name>/ --recursive
```

Copy file to S3

```bash
aws s3 cp <file> s3://<bucket-name>/
```

Copy directory to S3

```bash
aws s3 cp <directory> s3://<bucket-name>/<prefix>/ --recursive
```

Copy file from S3

```bash
aws s3 cp s3://<bucket-name>/<file> .
```

Sync local directory with S3

```bash
aws s3 sync <local-directory> s3://<bucket-name>/<prefix>/
```

Sync S3 with local directory

```bash
aws s3 sync s3://<bucket-name>/<prefix>/ <local-directory>
```

Delete file from S3

```bash
aws s3 rm s3://<bucket-name>/<file>
```

Delete directory from S3

```bash
aws s3 rm s3://<bucket-name>/<prefix>/ --recursive
```

Create bucket

```bash
aws s3 mb s3://<bucket-name>
```

Create bucket in specific region

```bash
aws s3api create-bucket \
  --bucket <bucket-name> \
  --region <region> \
  --create-bucket-configuration LocationConstraint=<region>
```

Get bucket location

```bash
aws s3api get-bucket-location --bucket <bucket-name>
```

Get bucket policy

```bash
aws s3api get-bucket-policy --bucket <bucket-name>
```

Put bucket policy

```bash
aws s3api put-bucket-policy \
  --bucket <bucket-name> \
  --policy file://policy.json
```

List bucket versioning status

```bash
aws s3api get-bucket-versioning --bucket <bucket-name>
```

---

## CloudFormation

List stacks

```bash
aws cloudformation list-stacks
```

List stacks with specific status

```bash
aws cloudformation list-stacks \
  --stack-status-filter CREATE_COMPLETE UPDATE_COMPLETE
```

Describe stack

```bash
aws cloudformation describe-stacks --stack-name <stack-name>
```

Describe stack resources

```bash
aws cloudformation describe-stack-resources \
  --stack-name <stack-name>
```

Create stack

```bash
aws cloudformation create-stack \
  --stack-name <stack-name> \
  --template-body file://template.yaml
```

Create stack with parameters

```bash
aws cloudformation create-stack \
  --stack-name <stack-name> \
  --template-body file://template.yaml \
  --parameters ParameterKey=Key1,ParameterValue=Value1
```

Update stack

```bash
aws cloudformation update-stack \
  --stack-name <stack-name> \
  --template-body file://template.yaml
```

Delete stack

```bash
aws cloudformation delete-stack --stack-name <stack-name>
```

Get stack events

```bash
aws cloudformation describe-stack-events \
  --stack-name <stack-name>
```

Validate template

```bash
aws cloudformation validate-template \
  --template-body file://template.yaml
```

Validate template from S3

```bash
aws cloudformation validate-template \
  --template-url https://s3.amazonaws.com/<bucket>/<key>
```

---

## IAM

List users

```bash
aws iam list-users
```

List roles

```bash
aws iam list-roles
```

List roles containing specific name

```bash
aws iam list-roles \
  --query 'Roles[?contains(RoleName, `lambda`)].RoleName'
```

Get role

```bash
aws iam get-role --role-name <role-name>
```

Create role

```bash
aws iam create-role \
  --role-name <role-name> \
  --assume-role-policy-document file://trust-policy.json
```

Attach policy to role

```bash
aws iam attach-role-policy \
  --role-name <role-name> \
  --policy-arn <policy-arn>
```

List attached role policies

```bash
aws iam list-attached-role-policies --role-name <role-name>
```

List policies

```bash
aws iam list-policies
```

List local (custom) policies

```bash
aws iam list-policies --scope Local
```

Get current user

```bash
aws iam get-user
```

List access keys

```bash
aws iam list-access-keys
```

---

## EC2

List instances

```bash
aws ec2 describe-instances
```

List instances with specific details

```bash
aws ec2 describe-instances \
  --query 'Reservations[*].Instances[*].[InstanceId,State.Name,InstanceType]' \
  --output table
```

Start instance

```bash
aws ec2 start-instances --instance-ids <instance-id>
```

Stop instance

```bash
aws ec2 stop-instances --instance-ids <instance-id>
```

Reboot instance

```bash
aws ec2 reboot-instances --instance-ids <instance-id>
```

Terminate instance

```bash
aws ec2 terminate-instances --instance-ids <instance-id>
```

List regions

```bash
aws ec2 describe-regions --output table
```

---

## General AWS CLI

Configure AWS CLI

```bash
aws configure
```

List configuration

```bash
aws configure list
```

Get configured region

```bash
aws configure get region
```

Get caller identity

```bash
aws sts get-caller-identity
```

Get account ID

```bash
aws sts get-caller-identity --query Account --output text
```

Set output format

```bash
aws configure set output json
aws configure set output table
aws configure set output text
```

Use named profile

```bash
aws <command> --profile <profile-name>
```

Get help for any command

```bash
aws <service> help
aws <service> <command> help
```

---

## Query and Output Formatting

Table output

```bash
aws <command> --output table
```

JSON output (default)

```bash
aws <command> --output json
```

Text output

```bash
aws <command> --output text
```

Query specific fields

```bash
aws <command> --query 'key'
aws <command> --query 'array[*].field'
```

Filter with contains

```bash
aws <command> --query 'array[?contains(field, `value`)]'
```

---

## Useful Serverless Combinations

Get Lambda function ARN

```bash
aws lambda get-function \
  --function-name <function-name> \
  --query 'Configuration.FunctionArn' \
  --output text
```

Get SQS queue with DLQ configuration

```bash
aws sqs get-queue-attributes \
  --queue-url <queue-url> \
  --attribute-names RedrivePolicy
```

View recent Lambda errors

```bash
aws logs filter-log-events \
  --log-group-name /aws/lambda/<function-name> \
  --filter-pattern "ERROR" \
  --start-time $(date -u -d '1 hour ago' +%s)000
```

Test SNS to Lambda integration

```bash
aws sns publish \
  --topic-arn <topic-arn> \
  --message "test message"
```

List all Lambda functions with runtime versions

```bash
aws lambda list-functions \
  --query 'Functions[*].[FunctionName,Runtime,LastModified]' \
  --output table
```

---

## Tips

- Use `--dry-run` flag when available to test commands without executing
- Use `--debug` flag for detailed debug output
- Use `--no-cli-pager` to disable the pager for long outputs
- Set `AWS_PAGER=""` environment variable to disable pager globally
- Use `--region` flag to specify region for a single command
- Use JMESPath for complex queries with `--query`
- Most commands support `--filters` for more efficient API calls

---

## Contributing

Feel free to submit pull requests with additional commands or improvements.

## License

MIT License
