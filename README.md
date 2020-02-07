About
==========================

I like Heroku as a first destination for new projects. Heroku has a couple addons for log management, but AWS CloudWatch Logs is a super low cost place for log files, and a relatively known quantity operationally.

There's no simple "select this addon" solution for Cloudwatch Logs from Heroku, but Heroku supports log "drains".

This lambda function acts as a Heroku log drain.

With a serverless solution I'm only charged for computing resources I use: important for situations where Heroku's free tier may power down the unused instance.

Using this lambda script
=========================

  1. Create an S3 bucket named `heroku-cloudwatch-sync-app`. If you decide to use a different name then there's a variable for that in the Cloudformation template for that.
  
  2. `make` creates the zip file for deployment. Upload the file `target/herokuCloudwatchSync.zip` to the S3 bucket.

  3. Run `build-scripts/create_cloudformation.sh`

  4. `cp env.sample .env` and fill out the values of the environmental variables with the S3 bucket name and the name of the lambda function that Cloudformation created for you (go into the lambda management console).


  5. `make deploy` will deploy the package to S3 and trigger lambda to use the new code.
  6. Using the AWS lambda management console, find out the URL for the lambda.
  7. The lambda takes two path parameters at the end: these are the Cloudwatch Logs log group and log stream to write events to. Decide on these.
  8. `heroku drains:add https://{lambdaApiEndpoint}/Prod/flush/{logGroup}/{logStream}`

Testing deployment
========================

Visit the `/Prod/flush/test/testing` route and you should not get errors in the CloudWatch logs for the lambda function.


Credit:
==========================

  * [On aws cloudformation package and references in CodeURI](https://github.com/awslabs/serverless-application-model/issues/61#issuecomment-311066225)
  * Basis for Makefile: [jc2k's blog post on using make for Python Lambda functions](https://unrouted.io/2016/07/21/use-make/)
  * Basis for reading Heroku flush info: [Mischa Spiegelmock's Heroku logging to Slack implementation](https://spiegelmock.com/2017/10/26/heroku-logging-to-aws-lambda/)
