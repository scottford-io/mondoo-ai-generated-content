# Video Script: Understanding Mondoo Policy Variants

## Opening Shot [30s]
Hi, I'm Scott Ford, Field CTO at Mondoo. Today I'm going to show you how Mondoo policy variants help you enforce security consistently across your entire software development lifecycle.

## Problem Statement [30s]
Security teams face a common challenge: How do you ensure the same security requirements are met in both infrastructure as code and runtime environments? Traditionally, this meant maintaining separate policies for development and production, leading to duplication and potential inconsistencies.

## Solution Overview [1m]
Let me show you how Mondoo solves this with policy variants. Here's a complete policy checking S3 bucket versioning across every stage of your SDLC:

```yaml
queries:
  - uid: s3-versioning-enabled
    title: 'Ensure S3 buckets have versioning enabled'
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

      - uid: s3-versioning-enabled-terraform-plan
        filters: asset.platform == "terraform-plan" && terraform.plan.resourceChanges.contains( type == "aws_s3_bucket" )
        mql: |
          terraform.plan.resourceChanges.where( type == "aws_s3_bucket_versioning" ).all(
            change.after.versioning_configuration.any( status == "Enabled" )
          )

      - uid: s3-versioning-enabled-terraform-state
        filters: asset.platform == "terraform-state" && terraform.state.resources.contains( type == "aws_s3_bucket" )
        mql: |
          terraform.state.resources.where( type == "aws_s3_bucket_versioning" ).all(
            values.versioning_configuration.any( status == "Enabled" )
          )
```

## Live Demo [2m]

### AWS Runtime Check
* Run the policy against live AWS environment
* Show results from production S3 buckets
* Explain how the API variant works

### Terraform HCL Check
* Switch to Terraform code in IDE
* Show real-time feedback during development
* Demonstrate how it catches issues before `terraform plan`

### Terraform Plan Check
* Run `terraform plan`
* Show policy checking the plan output
* Explain how it catches issues before apply

### Terraform State Check
* Check against applied infrastructure
* Show state validation
* Demonstrate continuous compliance

## Key Benefits [1m]

### Highlight Each Benefit
* **One Policy, Complete Coverage**: Development through production
* **Shift-Left Security**: Catch issues at every stage
* **Consistent Enforcement**: Same rules everywhere
* **Reduced Maintenance**: Update once, apply everywhere

## Call to Action [30s]
Want to learn more about Mondoo policy variants? Visit mondoo.com or check out our documentation. Don't forget to like and subscribe for more security automation content.

## On-Screen Elements
* Code snippets with syntax highlighting
* Split screen comparisons
* Real-time policy evaluation
* Key term captions

## B-Roll Suggestions
* VS Code with Terraform files
* AWS Console
* Terminal running Terraform commands
* Mondoo UI showing results