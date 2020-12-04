+++
categories = ["security", "aws"]
date = 2020-12-04T00:00:00Z
link = ""
tags = []
title = "How To Remediate Security Hub Finding IMDSv2"

+++
Using [SecurityHub](https://aws.amazon.com/security-hub/?aws-security-hub-blogs.sort-by=item.additionalFields.createdDate&aws-security-hub-blogs.sort-order=desc) you may come across "\[EC2.8\] EC2 instances should use IMDSv2" which is categorised as a high severity finding.

More info here: [https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-standards-fsbp-controls.html#fsbp-ec2-8](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-standards-fsbp-controls.html#fsbp-ec2-8 "https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-standards-fsbp-controls.html#fsbp-ec2-8")

**What is this!?**

Every EC2 instance contains metadata located at http://169.254.169.254 which contains details about the instance including the temporary IAM credentials used for the instance profile IAM role.  There are two versions of this known as `IMDSv1` and `IMDSv2.`V2 is a more secure version which requires tokens in order to access the metadata.

**Why is it high severity?**

V1 is secure but there are potential security vulnerabilities using:

* Open website application firewalls
* Open reverse proxies
* Service-side request forgery (SSRF) vulnerabilities
* Open Layer 3 firewalls and network address translation (NAT)

V2 adds to the security with defence in depth making sure every request is authorised.