# 4.4 Clean Up

> WARNING: Remember to clean up all AWS resources to ensure that you do not get a surprise charge 12 months from today.

---

## Background

One of the reasons we deploy our resources using Infrastructure-as-Code (IaC) is because of the ease of representing all resources that are deployed.

If the resource exists in code, it exists in the cloud. Similarly, if the resource ceases to exist within the code, it would cease to exist in the cloud as well (which is exactly what we want at this moment).

#### Removing Your Resources

Access your Cloudformation console on AWS. Remove your stacks by selecting and deleting it in the following order:

1. ecs-container 
2. ecs-cluster
3. vpc-subnet
4. role-policy

#### Removing Web Identity and Role

1. Go to IAM > Roles
2. Look for the role that you have created during the [Web Identity Setup](../ci-cd/30-DevOps-Setup.md)
3. Delete the role
4. Go to IAM > Identity Providers 
5. Select your provider and delete the identity 

#### Definition of Done

1. All resources in AWS are deleted

### Optional - Deleting AWS Account

If you no longer want to keep your AWS Account for whatever reasons (e.g. fear of unauthorised usage charges appearing on your credit card), you may choose to delete your AWS Account by following the instructions here: https://aws.amazon.com/premiumsupport/knowledge-center/close-aws-account/

---