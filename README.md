# EKS Deploy Cloudformation Template

This template deploys an Amazon EKS cluster in a existing VPC and subnets. It also deploys an application using an external manifest using AWS Lambda.

## Pre Reqs

- [Create or use a AWS VPC](https://github.com/awslabs/aws-cloudformation-templates/blob/master/aws/services/VPC/VPC_With_Managed_NAT_And_Private_Subnet.yaml) with private an public subnets. ([Don't forget to TAG your subnets for EKS subnet discovery](https://aws.amazon.com/premiumsupport/knowledge-center/eks-vpc-subnet-discovery/))
- Activate AWSQS::EKS::Cluster Cloudformation resource provider.
- Create lambda layer to use with the lambda function.
- Deploy Cloudformation template.

## Activate AWSQS::EKS::Cluster

Open the Cloudformation console and upload the [cloudformation/extensions/01_activate_extension.yaml](cloudformation/extensions/01_activate_extension.yaml) into the console to deploy the AWSQS::EKS::Cluster resource provider.

## Creating lambda layer

In this repo we have a folder called `lambda-python` where you can find all the requirements to create the lambda-layer that later we gonna use on EKS Cloudformation template.

[Follow the steps of that guide about how to create and publish your layer.](https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html#configuration-layers-upload)

## Deploying Cloudformation Template

Let's deploy our EKS Cloudformation template, open Cloudformation Console and upload the [cloudformation/eks_resources/01_eks_sample_stack.yaml](cloudformation/eks_resources/01_eks_sample_stack.yaml)

Enter the following variables when requested:

- AWSIAMUser (User for granting access to Amazon EKS Cluster)
- ARNLambdaLayer (Layer arn created earlier)
- ClusterName
- SubnetIds (subnet-xxx,subnet-xxx,subnet-xxx)


# One Click Deployment with Nested Stacks

We can also deploy this solution as one click deployment, for that we are gonna need to create a Cloudformation package to use Nested Stacks.

Nested stacks are stacks created as part of other stacks. You create a nested stack within another stack by using the `AWS::CloudFormation::Stack` resource.

## Pre Reqs

- S3 bucket to upload templates (This ca be either private or public)
- aws cli

## Packaging

Packaging from root template.

```bash
cd cloudformation/
aws cloudformation package --template-file root_stack.yaml --output-template packaged.yaml --s3-bucket S3_BUCKET_NAME
```

After a quick `aws cloudformation package` on the root template, you'll get output to packaged.yaml that reflects a new "packaged" template with all necessary assets uploaded to your deployment s3 bucket.

## Deploy into the console

Open Cloudformation console and deploy the `packaged.yaml` template created earlier. That template is a nested stack with references to the two other stacks that we created.
