# example-lambda-aws
Example lambda with local run/testing and AWS deployment.

## Setup

* Install [SAM](https://aws.amazon.com/serverless/sam/)
* (optional) check [SAM dev documentation](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html)
* Install AWS Toolkit (search extension for your IDE)

## Installing Python 3.9 locally

Python 3.9 is required to build lambda functions using SAM (3.10 and later will not work).

```
sudo apt install software-properties-common -y
sudo add-apt-repository ppa:deadsnakes/ppa -y
sudo apt-get update && sudo apt-get install python3.9 python3.9-distutils
```

## Pricing and Costs

* [AWS API Gateway](https://aws.amazon.com/api-gateway/pricing/): free tier got up to 1m requests per month
* [AWS Lambda](https://aws.amazon.com/lambda/pricing/): no free tier, depends on the amount of requests/cpu time/storage per execution

## Setting Up Hello World Example

* Follow the guide [here](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-getting-started-hello-world.html)

```
# Select hello world app - will create sam-app directory
sam init
```

### Setting Up Local Environment and Testing Locally

Running lambda in a docker container and sending a request to it:

```
cd sam-app/
sam local start-api

# could be exposed on the different port on your machine
curl -XPOST "http://localhost:5478/2015-03-31/functions/function/invocations" -d '{}'
```

Running tests:

```
# Initialize virtualenv beforehand using pipenv/mkvirtualenv or other tool
# mkvirtualenv -p python3.9 awslambda
pip install -r tests/requirements.txt

# Integration test will fail as it runs against the actual lambda in AWS
pytest
```

### Deploying App to AWS

* Ctrl + Shift + P --> Create Credentials Profile

```
cd sam-app
# Using mkvirtualenv
workon awslambda
pip install -r hello_world/requirements.txt
sam build
sam deploy --guided

# Check logs of the app
sam logs -t

# Run tests again (will be green and visible in logs)
export AWS_SAM_STACK_NAME=sam-app
pytest

# Get application traces
sam traces

# Send request to the application via API Gateway (look up stages configuration in the app)
curl https://ayk0uswkf7.execute-api.eu-central-1.amazonaws.com/Prod/hello

# Send request directly to the AWS Lambda function
... I've got flagged by AWS for excessive activity by this point (automatic support case) - got blocked out of AWS >.>
```

### Clean Up

```
sam delete
```
