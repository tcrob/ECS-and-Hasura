# ReadMe

# Description

This repo will install Hasura and Aurora on AWS.  It is using serverless postgres (Aurora) as the database for Hasura.  Hasura is hosted on AWS Fargate, which is a part of Amazon Elastic Container Services.  The steps to get started are below.  Use this to automatically create an entire Hasura GraphQL serverless environment.

# Requirements

1. AWS CLI installed
2. Docker installed
3. Proper IAM permissions on AWS IAM account
4. Domain registered on AWS Route 53

# First Steps:

1. Create an ECR image from the Hasura docker image
2. Create a wildcard SSL certificate in the AWS Cert manager

# SSL Certificate Steps:
1. Go to the AWS certificate page within AWS
2. Request a certificate
3. Request a public certificate
4. Type out the domain name you want to be requested.
   1. Use a wildcard.  *.DOMAIN.com
5. Hit next until you get to the validation
6. Hit the arrow next to the domain and hit the button that says 'Create record in Route 53'
7. Wait 15-30 minutes

# ECR Image Steps:
1. Get the authentication command from AWS using the CLI:
   1. ```aws ecr get-login-password —region <REGION> | docker login —username AWS —password-stdin <ACCOUNT-ID>.dkr.ecr.<REGION>.amazonaws.com```
2. Get the docker image pulled to your local machine
   1. ```docker pull hasura/graphql-engine```
3. Create a repository on AWS ECR (on the AWS website), call it whatever.
4. Copy the URI value (ARN) of the ECR you just created.
5. Run a command to get the docker image.  Copy the 'IMAGE ID' of the Hasura image
   1. ```docker images```
6. Tag the docker image with the URI + docker image
   1. ```docker tag <hasura-image> <ecr-uri>```
7. Push the image to the ECR
   1. ```docker push <ecr-uri>```

# Second Steps:

1.  Update the parameters.json file with the necessary info from AWS.
2.  The database master user name cannot be 'admin'
3.  In the DNSRecordSet field put whatever you want the URL to be.  
    1.  dev-database.DOMAIN.com, for example
4.  Run the "Creation Script" (below)



# Creation Script
```
aws cloudformation create-stack --stack-name NAME-HERE-GraphQL-Stack --template-body file://./Stack.yaml --parameters file://./parameters.json --capabilities CAPABILITY_NAMED_IAM
```
