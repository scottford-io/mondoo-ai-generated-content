# Video Script: Understanding Mondoo Policy Variants

## Opening Shot [30s]
Hi, I'm Scott Ford, Field CTO at Mondoo. Today I'm going to show you how Mondoo policy variants help you enforce security consistently across your entire software development lifecycle.

## Problem Statement [30s]
Security teams face a common challenge: How do you ensure the same security requirements are met in both infrastructure as code and runtime environments? Traditionally, this meant maintaining separate policies for development and production, leading to duplication and potential inconsistencies.

## Solution Overview [1m]
Let me show you how Mondoo solves this with policy variants. Here's a policy checking for S3 bucket versioning:

```yaml
queries:
  - uid: s3-versioning-enabled
    variants:
      - uid: s3-versioning-enabled-api
        filters: asset.platform == "aws"
        mql: |
          aws.s3.buckets.all(
            versioning.Status == "Enabled"
          )
```

## Live Demo [2m]

### AWS Runtime Check
* Show the AWS runtime variant in action
* Demonstrate checking live S3 buckets
* Explain the results

### Terraform Checks
* Switch to Terraform code
* Show the same policy working on IaC
* Demonstrate real-time feedback

## Key Benefits [1m]

### Highlight Each Benefit
* **Eliminate Duplication**: One policy, multiple environments
* **Consistent Security**: Same checks everywhere
* **Shift-Left Security**: Catch issues early
* **Complete Coverage**: Development through production

## Call to Action [30s]
Want to learn more about Mondoo policy variants? Visit mondoo.com or check out our documentation. Don't forget to like and subscribe for more security automation content.

## On-Screen Elements
* Code snippets should be syntax highlighted
* Use split screen for comparing environments
* Show real-time policy evaluation
* Include captions for technical terms

## B-Roll Suggestions
* IDE showing Terraform code
* AWS Console
* Mondoo UI
* Terminal sessions