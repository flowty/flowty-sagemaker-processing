## Flowty Image

### Overview

This example creates a custom image for ScriptProcessor from `sagemaker.processing`

### Creating the ECR repository
We need to create an ECR to store our Docker image, use the AWS CLI
```
REGION=<aws-region>
REPOSITORY=<repository-name>

aws --region ${REGION} ecr create-repository \
    --repository-name ${REPOSITORY}
```

### Build and push the custom Docker Image
Using the AWS CLI, we need to log in to the newly created ECR to be able to push our Docker image
```
# Modify these as required. The Docker registry endpoint can be tuned based on your current region from https://docs.aws.amazon.com/general/latest/gr/ecr.html#ecr-docker-endpoints

ACCOUNT_ID=<account-id>
IMAGE_NAME=<image-name>

aws --region ${REGION} ecr get-login-password | docker login 
    --username AWS 
    --password-stdin ${ACCOUNT_ID}.dkr.ecr.${REGION}.amazonaws.com/${REPOSITORY}
```

### Build the image
In the directory containing the Dockerfile, we build and push the docker image using the following CMD commands  
```
docker build . -t ${IMAGE_NAME} -t ${ACCOUNT_ID}.dkr.ecr.${REGION}.amazonaws.com/${REPOSITORY}:${IMAGE_NAME}
```
### Push the image
```
docker push ${ACCOUNT_ID}.dkr.ecr.${REGION}.amazonaws.com/${REPOSITORY}:${IMAGE_NAME}
```

### Using it with Sagemaker ScriptProcessor

In the Sagemaker-processing folder: Open the sagemaker-processing notebook

Set the ecr_uri and role_arn (with permission to create and push images to AWS ECR & permission to read and write to S3 buckets )

```
#IAM Arn role in your account to be used with the SageMaker ScriptProcessor

ecr_uri = ${ACCOUNT_ID}.dkr.ecr.${REGION}.amazonaws.com/${REPOSITORY}:${IMAGE_NAME}

role_arn = <ROLE_ARN>
```

In the next cell we need to set the alias, and instance_name, and specify the output_bucket

Run all cells
