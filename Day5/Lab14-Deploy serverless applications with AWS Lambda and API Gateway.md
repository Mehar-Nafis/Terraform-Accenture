## Deploy serverless applications with AWS Lambda and API Gateway
Setting up infrastructure with Terraform, packaging and deploying a Lambda function, integrating it with API Gateway, and testing the entire flow.

1. Create an S3 Bucket for the Lambda Function:
   * Use Terraform to create an S3 bucket where the Lambda function package will be stored.
   * Apply the configuration to create the S3 bucket using terraform apply.
    
2. Package and Upload Lambda Function Code:
   * Package the Lambda function code and dependencies (in this case, a Node.js function) into a ZIP file using the archive_file data source.
   * Upload the ZIP file to the S3 bucket using an aws_s3_object resource.
    
3. Create the Lambda Function:
   * Define the Lambda function using the aws_lambda_function resource.
   * Specify the S3 bucket and key for the code package, runtime, handler, and IAM role for the function execution.
   * Create a CloudWatch Log Group for storing logs from the Lambda function.
   * Define an IAM role for the Lambda function, including necessary policies for CloudWatch access.
   * Apply the configuration to deploy the Lambda function and associated resources.
   * Invoke the Lambda function using the AWS CLI to confirm it is working as expected.
    
4. Set Up API Gateway:
   * Create an API Gateway with an HTTP protocol using the aws_apigatewayv2_api resource.
   * Configure a deployment stage and enable access logging.
   * Set up an API integration with the Lambda function using aws_apigatewayv2_integration.
   * Create a route in API Gateway that maps to a specific path (/hello) and integrates with the Lambda function.  
   * Grant API Gateway permission to invoke the Lambda function using aws_lambda_permission.
   * Send a request to the API endpoint, which will trigger the Lambda function and return a response.


### Task 1: Create infrastructure
Create a directory and Change to the repository directory.
```
mkdir serverless && cd serverless
```
Add the below files. The `main.tf` defines the AWS provider and creates S3 bucket that will store your Lambda function.
```
vi main.tf
```
```hcl
provider "aws" {
  region = var.aws_region

}

resource "random_pet" "lambda_bucket_name" {
  prefix = "learn-terraform-functions"
  length = 4
}

resource "aws_s3_bucket" "lambda_bucket" {
  bucket = random_pet.lambda_bucket_name.id
}

resource "aws_s3_bucket_ownership_controls" "lambda_bucket" {
  bucket = aws_s3_bucket.lambda_bucket.id
  rule {
    object_ownership = "BucketOwnerPreferred"
  }
}

resource "aws_s3_bucket_acl" "lambda_bucket" {
  depends_on = [aws_s3_bucket_ownership_controls.lambda_bucket]

  bucket = aws_s3_bucket.lambda_bucket.id
  acl    = "private"
}

```
The `output.tf` will print the S3 bucket used to store function code.
  
```
vi output.tf
```
```hcl

# Output value definitions

output "lambda_bucket_name" {
  description = "Name of the S3 bucket used to store function code."

  value = aws_s3_bucket.lambda_bucket.id
}
```
The `variables.tf` defines the variables using the the configurations.
```
vi variables.tf
```
```hcl
variable "aws_region" {
  description = "AWS region for all resources."

  type    = string
  default = "us-east-1"
}
```
Initialize this configuration.
```
terraform init
```
![image](https://github.com/user-attachments/assets/9a3aad52-9260-4d68-99f6-d8cbc64667dd)

Apply the configuration to create your S3 bucket. 
```
terraform apply --auto-approve
```
![image](https://github.com/user-attachments/assets/7fb2c357-fd30-43ef-8494-43e66e801146)

### Task 2: Create and upload Lambda function archive
To deploy an AWS Lambda function, it must be packaged in an archive containing the function source code and any dependencies. The way you build the function code and dependencies will depend on the language and frameworks you choose. Here, we will deploy the NodeJS function. 

Create a directory. The below function takes an incoming event object from Lambda and logs it to the console. Then it returns an object which API Gateway will use to generate an HTTP response.
```
mkdir hello-world && cd hello-world
```
```
vi hello.js
```
```
module.exports.handler = async (event) => {
  console.log('Event: ', event);
  let responseMessage = 'Hello, World!';

  return {
    statusCode: 200,
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      message: responseMessage,
    }),
  }
}
```
Add the below configuration to s3_object.tf to package and copy this function to your S3 bucket.
```
vi s3_object.tf
```
```
data "archive_file" "lambda_hello_world" {
  type = "zip"

  source_dir  = "${path.module}/hello-world"
  output_path = "${path.module}/hello-world.zip"
}

resource "aws_s3_object" "lambda_hello_world" {
  bucket = aws_s3_bucket.lambda_bucket.id

  key    = "hello-world.zip"
  source = data.archive_file.lambda_hello_world.output_path

  etag = filemd5(data.archive_file.lambda_hello_world.output_path)
}
```
To update the locked dependency selections 
```
terraform init -upgrade
```
Create the bucket object now.
```
terraform apply --auto-approve
```
![image](https://github.com/user-attachments/assets/1bc8494a-aed8-4cc4-9ff3-100865e5b736)

Once Terraform deploys your function to S3, use the AWS CLI to inspect the contents of the S3 bucket.

![image](https://github.com/user-attachments/assets/08d31350-3d46-4c37-8f9e-9f759de4619d)


## Task 3: Create the Lambda function

Create the Lambda function. 
```
vi lambda.tf
```
```
resource "aws_lambda_function" "hello_world" {
  function_name = "HelloWorld"

  s3_bucket = aws_s3_bucket.lambda_bucket.id
  s3_key    = aws_s3_object.lambda_hello_world.key

  runtime = "nodejs20.x"
  handler = "hello.handler"

  source_code_hash = data.archive_file.lambda_hello_world.output_base64sha256

  role = aws_iam_role.lambda_exec.arn
}

resource "aws_cloudwatch_log_group" "hello_world" {
  name = "/aws/lambda/${aws_lambda_function.hello_world.function_name}"

  retention_in_days = 30
}

resource "aws_iam_role" "lambda_exec" {
  name = "serverless_lambda"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Action = "sts:AssumeRole"
      Effect = "Allow"
      Sid    = ""
      Principal = {
        Service = "lambda.amazonaws.com"
      }
      }
    ]
  })
}

resource "aws_iam_role_policy_attachment" "lambda_policy" {
  role       = aws_iam_role.lambda_exec.name
  policy_arn = "arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole"
}
```
Add the following to outputs_lambda.tf to create an output value for your Lambda function's name.
```
vi outputs_lambda.tf
```
```
output "function_name" {
  description = "Name of the Lambda function."

  value = aws_lambda_function.hello_world.function_name
}
```
Apply this configuration to create your Lambda function and associated resources. 
```
terraform apply --auto-approve
```
![image](https://github.com/user-attachments/assets/e9eb6dfe-c80a-48eb-800b-57189984183d)

Once Terraform creates the function, invoke it using the AWS CLI.
```
aws lambda invoke --region=us-east-1 --function-name=$(terraform output -raw function_name) response.json
```
Check the contents of response.json to confirm that the function is working as expected.
![image](https://github.com/user-attachments/assets/778b9d9c-587e-4c83-8de8-69eabac71377)

### Task 4: Create an HTTP API with API Gateway
API Gateway is an AWS managed service that allows you to create and manage HTTP or WebSocket APIs. It supports integration with AWS Lambda functions, allowing you to implement an HTTP API using Lambda functions to handle and respond to HTTP requests.

Add the following to api.tf to configure an API Gateway.
```
vi api.tf
```
```
resource "aws_apigatewayv2_api" "lambda" {
  name          = "serverless_lambda_gw"
  protocol_type = "HTTP"
}

resource "aws_apigatewayv2_stage" "lambda" {
  api_id = aws_apigatewayv2_api.lambda.id

  name        = "serverless_lambda_stage"
  auto_deploy = true

  access_log_settings {
    destination_arn = aws_cloudwatch_log_group.api_gw.arn

    format = jsonencode({
      requestId               = "$context.requestId"
      sourceIp                = "$context.identity.sourceIp"
      requestTime             = "$context.requestTime"
      protocol                = "$context.protocol"
      httpMethod              = "$context.httpMethod"
      resourcePath            = "$context.resourcePath"
      routeKey                = "$context.routeKey"
      status                  = "$context.status"
      responseLength          = "$context.responseLength"
      integrationErrorMessage = "$context.integrationErrorMessage"
      }
    )
  }
}

resource "aws_apigatewayv2_integration" "hello_world" {
  api_id = aws_apigatewayv2_api.lambda.id

  integration_uri    = aws_lambda_function.hello_world.invoke_arn
  integration_type   = "AWS_PROXY"
  integration_method = "POST"
}

resource "aws_apigatewayv2_route" "hello_world" {
  api_id = aws_apigatewayv2_api.lambda.id

  route_key = "GET /hello"
  target    = "integrations/${aws_apigatewayv2_integration.hello_world.id}"
}

resource "aws_cloudwatch_log_group" "api_gw" {
  name = "/aws/api_gw/${aws_apigatewayv2_api.lambda.name}"

  retention_in_days = 30
}

resource "aws_lambda_permission" "api_gw" {
  statement_id  = "AllowExecutionFromAPIGateway"
  action        = "lambda:InvokeFunction"
  function_name = aws_lambda_function.hello_world.function_name
  principal     = "apigateway.amazonaws.com"

  source_arn = "${aws_apigatewayv2_api.lambda.execution_arn}/*/*"
}
```

Add an output value for this URL to outputs.tf.
```
vi outputs_api.tf
```
```
output "base_url" {
  description = "Base URL for API Gateway stage."

  value = aws_apigatewayv2_stage.lambda.invoke_url
}
```
Apply your configuration to create the API Gateway and other resources. 
```
terraform apply --auto-approve
```
![image](https://github.com/user-attachments/assets/f77a5a62-37b5-4019-ab13-b10e1d4b8703)

Now, send a request to API Gateway to invoke the Lambda function. The endpoint consists of the base_url output value and the /hello path, which do you defined as the route_key.
```
curl "$(terraform output -raw base_url)/hello"
```
![image](https://github.com/user-attachments/assets/aa28cf1f-2d26-4cb3-9db6-5374bc0749e7)


### Task 5: Clean up your resources
```
terraform destroy --auto-approve
```
