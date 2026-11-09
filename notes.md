# Project Notes
Development portfolio hosted on AWS

## App Source Code
- FastAPI for Backend, inside of a docker container hosted on AWS Lambda
- Angular for Frontend, static files hosted on S3

## IaC Provisioning
- Frontend Storage: An Amazon S3 bucket configured for hosting static assets.
- Frontend Distribution: An Amazon CloudFront CDN pointing to that S3 bucket, along with a custom error response redirecting 404s back to index.html (for Angular client-side routing).
- Container Registry: An Amazon ECR repository with a strict Lifecycle Policy attached to delete old images automatically.
- Compute Backend: An AWS Lambda function configured as a "Container Image" package type, linked to a Lambda Function URL or an HTTP API Gateway for public routing.
- CI/CD Access: The AWS IAM Role enabling GitHub Actions to use secure OIDC token authentication instead of static keys.
- Database Tables: using On-Demand (Pay-Per-Request) billing to maintain a permanent $0.00 baseline cost when idle
- Lambda Environment Configuration: Inject the generated DynamoDB table names as environment variables into the Lambda function configuration so the backend can locate them seamlessly.

## Database
- DynamoDB (because its the least expensive)
- tables 
    - projects - for links to my other projects
    - users - list of users
    - password hashes - historical and current password hashes for users. used to check password security and enforce password policies.
    


## CI/CD
- Github for code storage and CI/CD (monorepo)

### Pipeline Outline:

#### Task A: Frontend Pipeline

1. If code is pushed to main and changes detected in frontend directory:
2. build the static files
3. update files in S3
4. Invalidate the old Amazon CloudFront cache 

#### Task B: Backend Pipeline
1. If code is pushed to main and changes detected in backend directory:
2. build the container
3. Update container in ECR
4. Update the Lambda Function to use the new container

#### Task C: Infrastructure Pipeline
1. When code is pushed to main and changes are detected in the infra/ directory
2. Automatically run a validation checks 
3. cdk deploy to provision changes


