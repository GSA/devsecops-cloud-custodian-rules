# devsecops-cloud-custodian-rules
A repo containing rule sets for cloud-custodian inside GSA AWS accounts. This repo does not contain cloud-custodian itself.

## What is this
Cloud Custodian is a tool that unifies the dozens of tools and scripts most organizations use for managing their AWS
accounts into one open source tool. It’s a stateless rules engine for policy definition and enforcement, with metrics and reporting for AWS.

http://www.capitalone.io/cloud-custodian/docs/
https://github.com/capitalone/cloud-custodian

## Whats here
see `/policies/operational` for rules that take actions (i.e. stop/start instances, block creation of non-compliant resources, automate encryption/backups)
see `/policies/compliance` for rules that verify compliance (i.e. users w/ console access have MFA enabled, resources are tag appropriate, check that encryption/backups are configured)

## Use Cases
Cost Savings:
* Resource Off Hours: Easy way to cut expenses by turning on/off resources on a automated schedule.
* Resource Resizing: Ability to automatically resize resources based on metrics over time.
* Garbage Collection: Automatic notifications and removal of stale and unused resources.

Compliance:
* Encryption: Verify and enforce encryption across numerous resources.
* Backups: Performs automated snapshots of servers and databases.
* Tag Enforcement: Proper tags are necessary for resource support and tracking.
* Security: Puts up automated safeguards to detect, remediate, and notify customers of non-compliant actions
* Standards: Ensure certain standards are used with consistency across all managed AWS accounts.

Examples:
* Verifies CloudTrail Logging is enabled and configured properly
* Verifies Network Logging is enabled and configured properly
* Verifies Root user’s access keys have been deleted
* Verifies MFA Token has been applied to Root user
* Verifies proper IAM password policy is enforced for users

## Getting Started
Cloud custodian requires [python, pip, virtualenv](https://virtualenv.pypa.io/en/stable/installation/) on client machine or [with docker using `docker exec` or `docker run`](https://github.com/capitalone/cloud-custodian/blob/master/Dockerfile) 

```
$ virtualenv --python=python2 custodian
$ source custodian/bin/activate
(custodian) $ pip install c7n aws-cli
(custodian) $ aws-cli configure

# Validate the configuration
$ custodian validate test.yml

# Perform a dryrun on the policies (no actions executed) to see what would change
$ custodian run --dryrun -s out test.yml

# Run the policy
$ custodian run -s out some/policy.yml
```
>Note - you can create an IAM user/roles with using `security audit` role if you are only using rules under `policies/compliance`
if you plan to use Cloud Custodian to enforce rules as lambda functions from Cloudtrial or perform actions on resources (e.g. turn on encyrption/backups, resize/start/stop resources that IAM will need `FullAccess` for those resources using least-priviledge principles for only those resources managed by Cloud Custodian)

## Concepts
```
name: Name for the policy
resource: Which resource type to check (ec2, rds, ebs, etc 100+)
description: |
    Brief statement of what the policy does
mode: How the policy will be executed (event(API Triggered), periodic(Cron Scheduled), config(Config Change Triggered), no mode for manual runs)
- filters: Narrow down resource matches with 1 or more of these (See filters below)
- actions: What to do with the resulting resource set found. (notify by email/sns/webhook or perform an operation `mark-for-op` stop, start, terminate, tag, resize, etc)
```

## Filters

* Operator matching (in, not-in, absent, not-null, gte, regex, etc)
* Arbitrary nesting of filters with ‘or’ and ‘and’ blocks.
* Simple key/value are equality matches with value expressions

`regex` fliters use Jmespath expressions: http://jmespath.org/
