# 3.0 Devops Setup

In this segment, we will configure the applications that were created in the frontend and backend workshops and set them up for Continuous Integration and Continuous Deployment to the Cloud.

The following tools will be used and covered in this segment:

- AWS Cloudformation
- Gitlab

## Introduction

In this exercise, we will be setting up an Amazon Web Services (AWS) Account. The setup of your own AWS account is an absolute requirement for the workshop component of the DevOps segment.

---

## Setting Up Your AWS Account

### Pre-Requisites

1. An email that has never been used to create an AWS account (you may want to create a new email account)
2. A personal credit card

```
WARNING: Resources deployed on the cloud do cost money, you are
responsible for any cost associated to these resources.

All resources deployed in this workshop is free. While we recommend
some creativity here and there, don't stray too far off course or you
might enter the paid zone.

It is your responsibility to protect your account.
```

### Instructions

1. Create your AWS account here: https://aws.amazon.com/free/
   1. Setup as a Personal account
   2. Use the Basic Support plan
2. Sign in as root user: https://console.aws.amazon.com
3. Setup MFA for root user (Optional)
   1. IAM > Add MFA
4. Setup Web Identity Federation with Gitlab (Enable Gitlab to use AWS services)
   1. Go to Identity and Access Management Console
   2. On the Sidebar select Identity Providers  -> Add Provider
   3. Select OpenID Connect and add the following, Remember to click on **Get Thumbprint**.
      - Provider URL: `https://gitlab.com`
      - Audience: `https://gitlab.com`
   4. On the Sidebar select Role -> Create Role
   5. Select **Web Identity** and Select your **provider** and **audience** as follows:
      - Identity Provider: `gitlab.com`
      - Audience: `https://gitlab.com`
      - Skip policy assignment and press Next.
      - Give your role a name and create your role.
5. Policy assignment
   1. Go to Policies -> Create Policy
   2. Click on the JSON tab and paste the following
      ```
      {
         "Version": "2012-10-17",
         "Statement": [
            {
                  "Sid": "VisualEditor0",
                  "Effect": "Allow",
                  "Action": [
                     "ecr:DescribeImageReplicationStatus",
                     "ecr:ListTagsForResource",
                     "ecr:UploadLayerPart",
                     "ecr:ListImages",
                     "ecr:BatchGetRepositoryScanningConfiguration",
                     "ecr:GetRegistryScanningConfiguration",
                     "ecr:CompleteLayerUpload",
                     "ecr:TagResource",
                     "ecr:DescribeRepositories",
                     "ecr:BatchCheckLayerAvailability",
                     "ecr:GetLifecyclePolicy",
                     "ecr:GetRegistryPolicy",
                     "ecr:DescribeImageScanFindings",
                     "ecr:GetLifecyclePolicyPreview",
                     "ecr:CreateRepository",
                     "ecr:DescribeRegistry",
                     "ecr:GetDownloadUrlForLayer",
                     "ecr:DescribePullThroughCacheRules",
                     "ecr:GetAuthorizationToken",
                     "ecr:PutImage",
                     "ecr:UntagResource",
                     "ecr:BatchGetImage",
                     "ecr:DescribeImages",
                     "ecr:InitiateLayerUpload",
                     "ecr:GetRepositoryPolicy",
                     "ecs:UpdateService"
                  ],
                  "Resource": "*"
            }
         ]
      }
      ```
   3. The policy you have created will grant Gitlab push access to your AWS account Container Registry
   4. Now lets assign this policy to the Role you have created in Step 4.
      1. Go to Roles in your Identity Management Console and search for the name of your created role
      2. You should see a Permissions Tab 
      3. Under the Permissions tab select **Add Permissions** on the top right panel and select attach policies
      4. Look for the policy you created just now and attach it to your role
4. Finally on the same Role panel, find the Trust relationships tab and Edit the tust policy with:
   1. Replace the default trust policy with the following:
      ```
      {
         "Version": "2012-10-17",
         "Statement": [
            {
                  "Effect": "Allow",
                  "Principal": {
                     "Federated": "arn:aws:iam::477843748912:oidc-provider/gitlab.com"
                  },
                  "Action": "sts:AssumeRoleWithWebIdentity",
                  "Condition": {
                     "StringEquals": {
                        "gitlab.com:sub": [
                              "project_path:<Your-Gitlab-Group-name>/backend:ref_type:branch:ref:main",
                              "project_path:<Your-Gitlab-Group-name>/frontend:ref_type:branch:ref:main",
                              "project_path:<Your-Gitlab-Group-name>/templates:ref_type:branch:ref:main"
                        ]
                     }
                  }
            }
         ]
      }
      ```
   2. Click on **Update policy** and your are all set! You have given your gitlab repositories permission to push images to AWS!
5. All done! Lets move on to the next tutorial to see how we can develop a CICD pipeline with our applications 

---

Approach a peer or a workshop instructor for help if you have trouble with this exercise

---