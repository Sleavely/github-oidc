# AWS + Github OIDC setup

[ ![CI status](https://github.com/Cloudbourn/github-oidc/actions/workflows/deploy.yml/badge.svg) ](https://github.com/Cloudbourn/github-oidc/actions/workflows/deploy.yml "View workflow")

This repo contains the configuration to allow Github Actions free reign in the cloud. 🌬☁

The generated role is only allowed to be assumed by repositories under your user or organization.

## Setup

1. First, fork the repository.

2. Change the default value from `Cloudbourn` to your Github username or organisation in [cloudformation.yml](./cloudformation.yml)

3. You cannot use the Github Actions to deploy this stack for the first time since OIDC is not set up yet. Instead, perform the initial deployment from your local machine:

  ```sh
  AWS_REGION=eu-west-1 aws cloudformation deploy --stack-name github-oidc --template-file cloudformation.yml --capabilities CAPABILITY_IAM
  ```

  Retrieving the generated role name:

  ```sh
  AWS_REGION=eu-west-1 aws cloudformation describe-stack-resources --stack-name github-oidc --query "StackResources[?LogicalResourceId=='Role'].PhysicalResourceId" --output text
  ```

4. Now modify the `role-to-assume` ARN in [deploy.yml](./.github/workflows/deploy.yml) with your role name. Don't forget to update the AWS Account ID.

5. If you forked the repository you need to explicitly go to Actions and enable workflows.

6. Commit and push your changes

## Usage in Github Actions

Here's a sample workflow using the OIDC role.

```yaml
name: Deploy

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    name: Deploy
    runs-on: ubuntu-latest

    # Required to use OIDC
    permissions:
      id-token: write
      contents: read

    steps:
      - uses: actions/checkout@v2

      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          # Change this role ARN like you did during setup
          role-to-assume: arn:aws:iam::484537476580:role/github-oidc-Role-11HTWA6AOKQLC
          aws-region: eu-west-1

      - name: Build
        ...

      - name: Publish front-end
        run: aws s3 sync . s3://cloudbourn-website
```
