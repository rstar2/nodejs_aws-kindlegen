# Deploy to AWS

## Deploy the 'Layer' first

```bash
cd layer && sls deploy
```

## Deploy the 'Lambda' after that

```bash
cd lambda && sls deploy
```

## Deploy the static client app

```bash
cd hosting && npm run deploy
```

It will sync local directories and S3 prefixes.

```bash
sls remove --nos3sync
```

can be used when removing the stack without removing S3 objects from the target S3

## Why use qemu-i386-static in the Layer?

The 'kindlegen' binary is 32 bin BUT the AWS lambda cannot run it normally.
Even though the EC2 or the Docker "awslinux" image can.
So use qemu-i386-static

See https://forums.aws.amazon.com/thread.jspa?threadID=166825

## Remove the CloudFormations

This time the removal process ust be the other way around, e.g first remove the Lambda and then the Layer as the Lambda uses a reference to the Layer.
Note for the Lambda stack to be deleted the S3 bucket has to be empty.

## Technologies

1. The client app is with Vue2 powered by Vite cli/bundling
   Then it's hosted on a AWS S3 public bucket.
   Then there's AWS CloudFront CDN in front (no custom domain so far)

2. The real conversion is a AWS Lambda function with a AWS Layer that contains the binary 'kindlegen'. The convertion is started once a file is uploaded into an upload AWS S3 bucket. 

3. There a 2 other AWS Lambda HTTP API functions (protected with authorizer) that produce pre-signed URLs for uploading and downloadin from the AWS S3 upload bucket
   The authorization is by Auth0 - a new Auth0 application is created. The client SPA uses auth0-spa-js to generate a JWT idToken which later the authorizer verifies using the Auth0's application's public certificate.
