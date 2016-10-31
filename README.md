
# Static site generator plugin: Subdirectory (copy part of tree)

This is a static site generator plugin for the [AWS Git-backed static
website stack][stack]. This plugin copies an entire subdirectory of
the Git source into the target static website.

This plugin takes the form of an AWS Lambda function that is deployed
in a ZIP file in an S3 bucket. When the static website stack is
created with CloudFormation, the site generator AWS Lambda function
parameters are pointed to the ZIP file in the S3 bucket, causing the
AWS Lambda function to be created and run in the new stack.

When the stack is running and the CodeCommit Git repository contents
are updated, CodePipeline automatically invokes this AWS Lambda
function, providing it a ZIP file of the CodeCommit Git branch
contents. This function turns that site source into the static web
site contents, and passes back a ZIP file. The stack then syncs that
content to the S3 bucket that serves the static website.

This subdirectory function plugin is almost the same as the
["identity" plugin][identity], except that it only copies part of the
Git branch repository content. All other content is left out of the
target website.

This subdirectory function plugin is useful if your Git repository
contains a lot of different things including site source,
documentation, etc., and one subdirectory contains the resulting
static website that you want to publish.

When passing parameters to the AWS Git-backed static website
CloudFormation template, specify:

- **GeneratorLambdaFunctionS3Bucket** - The S3 bucket containing this
  AWS Lambda function ZIP file. E.g., "run.alestic.com"

- **GeneratorLambdaFunctionS3Key** - The S3 key containing this AWS
  Lambda function ZIP file.  E.g.,
  "lambda/aws-lambda-codepipeline-site-generator-subdirectory.zip"

- **GeneratorLambdaFunctionRuntime** - "python2.7"

- **GeneratorLambdaFunctionHandler** - "index.handler"

- **GeneratorLambdaFunctionUserParameters** - Specify the path to the
  subdirectory in the Git repository that contains the static website
  content. This should not start with a leading "/" and it should not
  end with a trailing "/".

  For example, if your Git repository has a top level subdirectory
  named "htdocs" then pass that string as the user parameters
  value. If it is under a directory like "site/generated/public-html",
  then specify that as the value.

**WARNING!** If you do not specify the user parameters value
correctly, you may accidentally publish content in your Git repository
that you did not intend to make public.

Here is a sample stack creation command using aws-cli:

    domain=example.com
    email=yourrealemail@anotherdomain.com

    template_url=https://s3.amazonaws.com/run.alestic.com/cloudformation/aws-git-backed-static-website-cloudformation.yml
    stackname=${domain/./-}-$(date +%Y%m%d-%H%M%S)
    region=us-east-1

    aws cloudformation create-stack \
      --region "$region" \
      --stack-name "$stackname" \
      --capabilities CAPABILITY_IAM \
      --template-url "$template_url" \
      --tags "Key=Name,Value=$stackname" \
      --parameters \
        "ParameterKey=DomainName,ParameterValue=$domain" \
        "ParameterKey=NotificationEmail,ParameterValue=$email" \
        "ParameterKey=GeneratorLambdaFunctionS3Bucket,ParameterValue=run.alestic.com" \
        "ParameterKey=GeneratorLambdaFunctionS3Key,ParameterValue=lambda/aws-lambda-codepipeline-site-generator-subdirectory.zip" \
        "ParameterKey=GeneratorLambdaFunctionUserParameters,ParameterValue=htdocs" \
    echo region=$region stackname=$stackname

The important point in the above command is the last three
"Generator*" parameters, which specify the location of the
Subdirectory AWS Lambda static site generator plugin.

See the main [AWS Git-backed static website stack][stack]
documentation for more details on how to work with the stack once it
is launched.

[stack]: https://github.com/alestic/aws-git-backed-static-website
[identity]: https://github.com/alestic/aws-lambda-codepipeline-site-generator-identity
