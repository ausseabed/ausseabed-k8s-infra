# Survey Planning Tool

This folder includes the Kubernetes configuration for the AusSeabed Survey Plannning Tool (SPT). Since development the tool itself has been renamed to the Survey Coordination Tool, references within code (eg; namespaces, etc) still refer to `spt` or `survey-planning-tool`.


## Application source code

Source code for SPT can be found at the following link. This includes Docker and CI/CD related code.

[https://github.com/ausseabed/survey-request-and-planning-tool](https://github.com/ausseabed/survey-request-and-planning-tool)


## Environments

Different sets of config files are included for different environments, these can be found under the `./envs` folder.

- `prod`
  - The production deployment as is available at [https://planning.ausseabed.gov.au/](https://planning.ausseabed.gov.au/)
  - Running on the GA Marine AWS prod account
  - Redeployments are manual
  - Docker images are tagged to specific builds (config must be updated)
- `staging-frontiersi`
  - Staging environment available at [https://www.qa4mbes.staging.frontiersi.io/](https://www.qa4mbes.staging.frontiersi.io/)
  - Redeployments are performed automatically by CircleCI (CI/CD) when a new commit is pushed to the `develop` branch of the SPT repository (see above)
  - Docker images are tagged with `latest-beta`, config files do not need to be updated to redeploy new version.


## Docker

All Docker images used are pulled from the AusSeabed Docker Hub organisation.

[https://hub.docker.com/orgs/ausseabed/repositories](https://hub.docker.com/orgs/ausseabed/repositories)


## Deployment

### Secrets

Example secret files (`secrets.examples.yaml`) can be found within the associated env folder. Each of the parameters included in these example files must be provided as a base64 encoded string.

Base64 encoding can be performed on the command line as follows (in this example "bXkgc2VjcmV0" needs to be included `secrets.yaml`);

    echo -n 'my secret' | base64
    > bXkgc2VjcmV0


### Prod
*Note: The following instructions require the aws cli, and kubectl*

A dependency of this SPT deployment is having access to the AusSeabed AWS Cognito authentication service. The domain name for this is built into the docker image with some auth parameters included in the secrets file.

Change to directory including prod infrastructure config

    cd ./envs/prod

Set AWS EKS profile details. This assumes the user has already configured the `ga-marine-prod` profile and that it has the necessary AWS permissions.

    aws --profile=ga-marine-prod eks update-kubeconfig --name=ausseabed-prod-cluster --region=ap-southeast-2

Create Kubernetes namespace for SPT

    kubectl create namespace spt

Create and apply secrets file

    cp secrets.example.yaml secrets.yaml
    vi secrets.yaml
    kubectl apply -f secrets.yaml -n spt

Create deployments

    kubectl create -f postgis-deployment.yaml
    kubectl create -f mapserver-deployment.yaml
    kubectl create -f api-deployment.yaml
    kubectl create -f www-deployment.yaml

Create ingress

    kubectl create -f ingress.yaml
