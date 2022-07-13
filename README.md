# AWS + Github OIDC setup

[ ![CI status](https://github.com/Cloudbourn/github-oidc/actions/workflows/deploy.yml/badge.svg) ](https://github.com/Cloudbourn/github-oidc/actions/workflows/deploy.yml "View workflow")

This repo contains the configuration to allow Github Actions free reign in the cloud. 🌬☁

The generated role is only allowed to be assumed by repositories in the Cloudbourn organization.

## Github Actions

Here's a sample workflow using the OIDC role. Copy this as you see fit.

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
          role-to-assume: arn:aws:iam::484537476580:role/github-oidc-Role-11HTWA6AOKQLC
          aws-region: eu-west-1

      - name: Build
        ...

      - name: Publish front-end
        run: aws s3 sync . s3://cloudbourn-website
```

## AWS chicken and OIDC egg

Since OIDC was not set up, the stack was not initially deployed with Github Actions. Instead:

```sh
AWS_PROFILE=cloudbourn AWS_REGION=eu-west-1 aws cloudformation deploy --stack-name github-oidc --template-file cloudformation.yml --capabilities CAPABILITY_IAM
```
