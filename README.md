# AWS + Github OIDC setup

This repo contains the configuration for the AWS role and identity provider used to allow Github Actions.

## AWS chicken and OIDC egg

Since OIDC was not set up, the stack was not deployed with Github Actions. Instead:

```sh
AWS_PROFILE=cloudbourn AWS_REGION=eu-west-1 aws cloudformation deploy --stack-name github-oidc --template-file cloudformation.yml --capabilities CAPABILITY_IAM
```
