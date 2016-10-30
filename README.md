
# Static site generator plugin: Identity transformation (copy in to out)

This is a static site generator plugin for the [AWS Git-backed static
website stack][stack]. This plugin performs the identity
function. That is, it copies its input to its output without any
transformation.

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

This identity function plugin is the least interesting of the static
site generator plugins as it performs no modifications to the source
content, but simply copies it straight through to the site content.

This identity function plugin is still useful in a couple ways:

1. If your Git repository content is exactly what you want to show on
   your static website, then use this identity plugin to effect no
   change.

2. If you want to create a new static website generator plugin for
   this static website stack, you might consider starting with this
   source code and enhancing it to peform the transformation you
   desire. Then specify that new AWS Lambda function as the generator
   plugin when creating a new stack.

[stack]: https://github.com/alestic/aws-git-backed-static-website
