 Understanding Mondoo Policy Variants: One Policy for Your Entire SDLC

Security and compliance checks shouldn't be an afterthought that only happens in production. Modern DevSecOps practices require shifting security left, testing infrastructure configurations throughout the development lifecycle. But maintaining separate policies for development, staging, and production environments is error-prone and creates unnecessary maintenance overhead.

Enter Mondoo policy variants - a powerful feature that allows you to write a single policy that works across your entire software development lifecycle, from infrastructure as code through runtime environments.

## The Problem: Different Environments, Same Security Requirements 

Let's consider a common scenario: You need to ensure S3 buckets have versioning enabled. This check needs to work against:

* Terraform code during development
* Terraform plans in CI/CD pipelines
* Terraform state files after deployment
* Live AWS environments in production

Without variants, you'd need separate policies for each stage, leading to:

* Duplicate maintenance effort
* Risk of policy drift between environments
* Inconsistent security enforcement

## The Solution: Mondoo Policy Variants

Variants allow you to define a single security requirement while adapting the implementation for different platforms and stages. Here's a simple example:

```yaml
queries:
  - uid: s3-versioning-enabled
    title: 'Ensure S3 buckets have versioning enabled'
    impact: 70
    docs:
      desc: |
        S3 bucket versioning helps protect against accidental or malicious deletion
        of objects and provides a means to recover previous versions.
    variants:
      - uid: s3-versioning-enabled-api
        filters: asset.platform == "aws"
        mql: |
          aws.s3.buckets.all(
            versioning.Status == "Enabled"
          )

      - uid: s3-versioning-enabled-terraform-hcl
        filters: asset.platform == "terraform-hcl" && terraform.resources.contains( nameLabel == "aws_s3_bucket" )
        mql: |
          terraform.resources.where( nameLabel == "aws_s3_bucket" ).all(
            related.one( nameLabel == "aws_s3_bucket_versioning" ) &&
            related.where( nameLabel == "aws_s3_bucket_versioning" ).all(
              blocks.where( type == "versioning_configuration" ).any( arguments.status == "Enabled" )
            )
          )
```

## How Variants Work

Each variant has three key components:

1. **Unique Identifier**: The `uid` field identifies each variant
2. **Platform Filter**: The `filters` field determines when a variant applies
3. **MQL Query**: The actual check logic adapted for each platform

### Platform Filters

Filters ensure variants only run against relevant platforms:

```yaml
filters: asset.platform == "terraform-hcl" && terraform.resources.contains( nameLabel == "aws_s3_bucket" )
```

This filter ensures the check only runs when:

* We're scanning Terraform HCL code
* That code contains S3 bucket resources

### Adapting Queries

Notice how the MQL query adapts to each platform's structure:

* **AWS API**: Checks the live bucket configuration
* **Terraform HCL**: Examines the resource declarations
* **Terraform Plan**: Inspects planned changes
* **Terraform State**: Checks applied configurations

## Benefits of Using Variants

1. **Consistency**: One policy definition ensures consistent security requirements across environments
2. **Maintenance**: Update security requirements in one place
3. **Shift Left**: Catch issues early in development
4. **Complete Coverage**: Test security throughout the SDLC

## Best Practices

1. Use precise platform filters
2. Keep variant logic consistent
3. Document differences between variants
4. Test variants across all targeted platforms