+++
categories = ["security", "aws"]
date = 2020-12-04T00:00:00Z
link = ""
tags = ["AWS", "SecurityHub"]
title = "How To Remediate Security Hub Finding IMDSv2"

+++
When using AWS SecurityHub you may come across the following:

"\[EC2.8\] EC2 instances should use IMDSv2" which is categorised as a high severity finding.

**What is this!?** 

This is a SecuityHub control check that is verifiying if your EC2 instance metadata is configured with Instance Metadata Service Version 2.
<!--more-->

More info here: [https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-standards-fsbp-controls.html#fsbp-ec2-8](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-standards-fsbp-controls.html#fsbp-ec2-8 "https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-standards-fsbp-controls.html#fsbp-ec2-8")

**Tell Me More...**

Every EC2 instance contains metadata located at http://169.254.169.254 which contains details about the instance including the temporary IAM credentials used for the instance profile IAM role.  There are two versions of this known as `IMDSv1` and `IMDSv2.`V2 is a more secure version which requires tokens in order to access the metadata.

**Why is it high severity?**

V1 is secure but there are potential security vulnerabilities using:

* Open website application firewalls
* Open reverse proxies
* Service-side request forgery (SSRF) vulnerabilities
* Open Layer 3 firewalls and network address translation (NAT)

V2 adds to the security with defence in depth making sure every request is authorised.

**How Do We Fix it!?**

You can go ClickOps and update the metadata in the console but we don't condone ClickOps :-)

I like Terraform so you can update your instances using infrastructure as code:[https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/instance#metadata-options](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/instance#metadata-options "https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/instance#metadata-options")

I have incorporated using IMDSv2 into my Terraform module which can be found here:[https://github.com/ifunky/terraform-aws-ec2-instance](https://github.com/ifunky/terraform-aws-ec2-instance "https://github.com/ifunky/terraform-aws-ec2-instance")

    module "ec2_myserver" {
      source = "git::https://github.com/ifunky/terraform-aws-ec2-instance?ref=master"
    
      ami             = data.aws_ami.linux.id
      iam_role        = "iam_role_name"    
      key_pair        = var.my_key
      instance_type   = "t3a.small"
      vpc_id          = "a-e343434334"
      security_groups = ["sg_windows"]
      subnet          = "i-573443ww"
      name            = "My_Server"
      namespace       = "ifunky"
      stage           = "dev"
    
      metadata_options {
        enabled                 = true
        require_session_tokens  = true # Use metadata service V2
        http_hop_limit          = 1
      }
      ...