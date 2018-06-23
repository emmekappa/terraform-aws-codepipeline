[![Build Status](https://travis-ci.org/emmekappa/terraform-aws-codebuild.svg?branch=master)](https://travis-ci.org/emmekappa/terraform-aws-codebuild)

## AWS CodeBuild module for Terraform

Github --> CodeBuild --> Docker build --> Push to AWS ECR

Fetch code from Github and build a docker image through CodeBuild and publishing it to ECR.

The strategy used to lookup image tag are implemented inside `get_image_tag.sh` relaying on [Codebuild environment variables](https://docs.aws.amazon.com/codebuild/latest/userguide/build-env-ref-env-vars.html) like `CODEBUILD_SOURCE_VERSION` and `CODEBUILD_INITIATOR`.

Using code from Cloudposse
* https://github.com/cloudposse/terraform-aws-cicd
* https://github.com/cloudposse/terraform-aws-codebuild

## Features
* Creates CodeBuild project that fetch source from 
* Supporting github webhooks

## Usage
* Copy both `get_image_tag.sh` and `buildspec.yml` into your source root
* Use the module like this

```hcl
data "aws_caller_identity" "default" {}

module "codebuild" {
  source             = "../codebuild"
  namespace          = "${var.namespace}"
  name               = "${var.name}"
  stage              = "${var.stage}"
  build_image        = "${var.build_image}"
  build_compute_type = "${var.build_compute_type}"
  buildspec          = "${var.buildspec}"
  delimiter          = "${var.delimiter}"
  attributes         = "${concat(var.attributes, list("build"))}"
  tags               = "${var.tags}"
  privileged_mode    = "${var.privileged_mode}"
  aws_region         = "${signum(length(var.aws_region)) == 1 ? var.aws_region : data.aws_region.default.name}"
  aws_account_id     = "${signum(length(var.aws_account_id)) == 1 ? var.aws_account_id : data.aws_caller_identity.default.account_id}"
  image_repo_name    = "${var.image_repo_name}"
  github_token       = "${var.github_token}"
  repo_owner         = "${var.repo_owner}"
  repo_name          = "${var.repo_name}"

}
```

