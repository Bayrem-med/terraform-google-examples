# Custom Machine Types

[![button](http://gstatic.com/cloudssh/images/open-btn.png)](https://console.cloud.google.com/cloudshell/open?git_repo=https://github.com/GoogleCloudPlatform/terraform-google-examples&page=editor&tutorial=example-custom-machine-types/README.md)

This example creates an instance with a custom machine type, a bastion host and a NAT gateway.

**Figure 1.** *diagram of Google Cloud resources*

![architecture diagram](./diagram.png)

## Install Terraform

Install Terraform on Linux if it is not already installed (visit [terraform.io](https://terraform.io) for other distributions):

```
curl -sL https://goo.gl/UYp3WG | bash
source ${HOME}/.bashrc
```

## Setup Environment

```
cd example-custom-machine-types/
```

Set the project, replace `YOUR_PROJECT` with your project ID:

```
gcloud config set project YOUR_PROJECT
```

```
test -z DEVSHELL_GCLOUD_CONFIG && gcloud auth application-default login
export GOOGLE_PROJECT=$(gcloud config get-value project)
```

## Configure remote backend

Configure Terraform [remote backend](https://www.terraform.io/docs/backends/types/gcs.html) for the state file.

```
BUCKET=${GOOGLE_PROJECT}-terraform
gsutil mb gs://${BUCKET}

PREFIX=tf-es-custom-machine/state
```

```
cat > backend.tf <<EOF
terraform {
  backend "gcs" {
    bucket     = "${BUCKET}"
    prefix     = "${PREFIX}"
  }
}
EOF
```

## Run Terraform

```
terraform init
terraform plan
terraform apply
```

## Testing

SSH into the bastion host with port forwarding to Cerebro and Kibana:

```
eval $(ssh-agent)
ssh-add ~/.ssh/google_compute_engine
eval $(terraform output bastion)
```

SSH into the custom machine:

```
ssh tf-custom-1
```

Verify external IP is the IP of the NAT gateway:

```
curl http://ipinfo.io/ip
```

## Cleanup

Exit the ssh sessions:

```
exit
```

Remove all resources created by terraform:

```
terraform destroy
```
