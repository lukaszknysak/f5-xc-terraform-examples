# F5 Distributed Cloud Hybrid Security Architecture Deployments

## Overview

Example hybrid security deployments utilizing F5 Distributed Cloud WAAP in conjunction with the F5 product portfolio.

## Getting Started

## Prerequisites

* [F5 Distributed Cloud Account (F5XC)](https://console.ves.volterra.io/signup/usage_plan)
  * [Create an F5XC API certificate](https://docs.cloud.f5.com/docs/how-to/user-mgmt/credentials)
* [NGINX Plus with App Protect license](https://www.nginx.com/free-trial-request/)
* [AWS Account](https://aws.amazon.com) - Due to the assets being created, free tier will not work.
  * The F5 BIG-IP AMI being used from the AWS Marketplace must be applied to your account
* [Terraform Cloud Account](https://developer.hashicorp.com/terraform/tutorials/cloud-get-started)
* [GitHub Account](https://github.com)

## Assets

* **xc:** F5 Distributed Cloud WAAP
* **nap:** NGINX Kubernetes Ingress Controller with NGINX App Protect WAF
* **bigip:** F5 BIG-IP Advanced WAF
* **infra:** AWS Infrastructure (VPC, IGW, etc.)
* **eks:** AWS Elastic Kubernetes Service
* **arcadia:** Arcadia Finance test web application and API
* **juiceshop:** OWASP Juice Shop test web application

## Tools

* **Cloud Provider:** AWS
* **IAC:** Terraform
* **IAC State:** Terraform Cloud
* **CI/CD:** GitHub Actions

## Terraform Cloud

* **Workspaces:** Create a CLI or API workspace for each asset in the workflow chosen

  | **Workflow** | **Assets/Workspaces**          |
  | ----------- | ------------------------------- |
  | xc-bigip    | infra, bigip, juiceshop, xc     |
  | xc-nap      | infra, eks, nap, arcadia, xc    |
  | xc-nap-api  | infra, eks, nap, arcadia, xc    |
  | xc-nap-bot  | infra, eks, nap, arcadia, xc    |

* **Workspace Shareing:** Under the settings for each Workspace, set the **Remote state sharing** to share with each Workspace created.
  
* **Variable Set:** Create a Variable Set with the following values.
  * AWS_ACCESS_KEY_ID: Your AWS Access Key ID - Environment Variable
  * AWS_SECRET_ACCESS_KEY: Your AWS Secret Access Key - Environment Variable
  * AWS_SESSION_TOKEN: Your AWS Session Token - Environment Variable
  * NGINX_JWT: Your NGINX Java Web Token assocaited with your NGINX license. Set this to nignx-repo.jwt - Environment Variable
  * VOLT_API_P12_FILE: Your F5XC API certificate. Set this to api.p12 - Environment Variable
  * VES_P12_PASSWORD: Set this to the password you supplied when creating your F5 XC API key - Environment Variable
  * ssh_key: Your ssh key for access to created BIG-IP and compute assets - Terraform Variable
  * admin_src_addr: The source address of your administrative workstation - Terraform Variable

## GitHub

* **Fork and Clone Repo**

* **Actions Secrets:** Create the following GitHub Actions secrets in your forked repo
  *  NGINX_JWT: The base64 encoded NGINX Java Web Token assocaited with your NGINX license
  *  P12: The base64 encoded F5XC API certificate
  *  TF_API_TOKEN: Your Terraform Cloud API token
  *  TF_CLOUD_ORGANIZATION: Your Terraform Cloud Organization name
  *  TF_CLOUD_WORKSPACE_Workspace Name: Create for each workspace in your workflow
      * EX: TF_CLOUD_WORKSPACE_BIGIP would be created with the value bigip

## Deployment

**Step 1:** Check out a branch for the workflow you wish to deploy using the following naming convention
  * xc-bigip deployment branch: deploy-xc-bigip
  * xc-nap deployment branch: deploy-xc-nap

**Step 2:** Edit infra/terraform.tfvars and add the following data
  * project_prefix  = "Your project identifier - this will be applied as a prefix to all assets"
  * resource_owner = "You"
  * aws_region     = "AWS Region"
  * azs            = ["AZ1", "AZ2"] - Your Region Availability Zones

**Step 3:** Edit bigip/terraform.tfvars and add the following data
  * f5_ami_search_name = "F5 BIGIP-16.1.3* PAYG-Adv WAF Plus 25Mbps*" - You must be subscribed to the AMI in the [AWS Marketplace](https://aws.amazon.com/marketplace)
  * aws_secretmanager_auth = false
  * create_awaf_config = true
  * awaf_config_payload = "awaf-config.json"


**Step 3:** Edit xc/terraform.tfvars and add the following data
  * api_url         = "Your F5XC tenant"
  * xc_namespace    = "The XC namespace you are deploying to"
  * app_domain      = "the FQDN of your app (cert will be autogenerated)"
  * xc_waf_blocking = "Set to true to enable blocking"

**Step 4:** Commit and push your changes to your forked repo
  * Build will run and can be monitored in the GitHub Actions tab and TF Cloud console

**Step 5:** Once the pipeline completes, head on over to the app FQDN!

## Development

Outline any requirements to setup a development environment if someone would like to contribute.  You may also link to another file for this information.

## Support

For support, please open a GitHub issue.  Note, the code in this repository is community supported and is not supported by F5 Networks.  

## Community Code of Conduct

Please refer to the [F5 DevCentral Community Code of Conduct](code_of_conduct.md).

## License

[Apache License 2.0](LICENSE)

## Copyright

Copyright 2014-2020 F5 Networks Inc.

### F5 Networks Contributor License Agreement

Before you start contributing to any project sponsored by F5 Networks, Inc. (F5) on GitHub, you will need to sign a Contributor License Agreement (CLA).

If you are signing as an individual, we recommend that you talk to your employer (if applicable) before signing the CLA since some employment agreements may have restrictions on your contributions to other projects.
Otherwise by submitting a CLA you represent that you are legally entitled to grant the licenses recited therein.

If your employer has rights to intellectual property that you create, such as your contributions, you represent that you have received permission to make contributions on behalf of that employer, that your employer has waived such rights for your contributions, or that your employer has executed a separate CLA with F5.

If you are signing on behalf of a company, you represent that you are legally entitled to grant the license recited therein.
You represent further that each employee of the entity that submits contributions is authorized to submit such contributions on behalf of the entity pursuant to the CLA.
