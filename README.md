# AusSeabed Survey Request and Planning Tool - Elastic Kubernetes Service(EKS) Cluster Deployment 

This repo contains the config files and process to create a EKS cluster with the eksctl tool and configure access to it.

# Licensing Terms

AusSeabed Data Hub – Survey Request and Planning is licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at [http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0)

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.

Copyright (c) 2019- AusSeabed Development Team

# About the AusSeabed Development Team

The AusSeabed Development Team is the set of all contributors to the AusSeabed Program. This includes all of the AusSeabed Subprojects, which are the different repositories under the AusSeabed GitHub organization.

## Our Copyright Policy

AusSeabed Data Hub – Survey Request and Planning tool uses a shared copyright model. Each contributor maintains copyright over their contributions. But, it is important to note that these contributions are typically only changes to the repositories. Thus, the ASB Request and Planning tool source code, in its entirety is not the copyright of any single person or institution. Instead, it is the collective copyright of the entire AusSeabed Development Team. If individual contributors want to maintain a record of what changes/contributions they have specific copyright on, they should indicate their copyright in the commit message of the change, when they commit the change to one of the AusSeabed repositories.

With this in mind, the following banner should be used in any source code file to indicate the copyright and license terms:

    Distributed under the terms of the Apache License, Version 2.0.

# Dependencies

1. AWS Account with programmic access and with permissions to create AWS resources. If specific permissions need to be specified view this thread: https://github.com/weaveworks/eksctl/issues/204.
2. awscli, see: https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html
3. eksctl, see: https://eksctl.io/introduction/installation/
4. kubectl, see: https://docs.aws.amazon.com/eks/latest/userguide/configure-kubectl.html
5. aws-iam-authenticator, see: https://docs.aws.amazon.com/eks/latest/userguide/install-aws-iam-authenticator.html

## Setup

Clone the repository
```
    git clone https://github.com/ausseabed/ausseabed-k8s-infra.git
    cd ausseabed-k8s-infra
```

Configure awscli with your access keys:
```
    aws configure
```

The environments/prod-cluster.yaml file contains eksctl setup parameters. See eksctl documentation for more information: https://eksctl.io/.

Create the cluster:
```
    eksctl create cluster -f environments/prod-cluster.yaml
```

Check the cluster:
```
    kubectl get pods --all-namespaces
```

You should see running pods. If so you are ready to deploy your applications.

## Adding addional users access to the cluster

To add users other than yourself to access kubectl you need to add them the kubernetes iamidentitymapping.

Get your account id:
```
    ACCOUNT_ID=$(aws sts get-caller-identity --output text --query 'Account')
```   

Then check IAM and note down the IAM username you wish to allow to access your eks cluster.

Give access with the following command replacing `test1` in the arn and username with their username:
```
    eksctl create iamidentitymapping --cluster  ausseabed-prod-cluster --arn arn:aws:iam::$ACCOUNT_ID:user/*test1* 
    --group system:masters --username *test1*
```

## Addional users instructions to access the cluster

Configure awscli with your credentials:
```
    aws configure
```

Update your kubectl config:
```
    aws eks update-kubeconfig --name=ausseabed-prod-cluster --region=ap-southeast-2
```

Run the following command:
```
    kubectl get pods --all-namespaces
```

You should see running pods. If so you are ready to deploy your applications.

### Deleting cluster

To delete the cluster run:

```
    eksctl delete cluster -f environments/prod-cluster.yaml
```








