# devsecops-cloud-custodian-rules
A repo containing rule sets for cloud-custodian inside GSA AWS accounts. This repo does not contain cloud-custodian itself.

## What
Cloud Custodian is a tool that unifies the dozens of tools and scripts most organizations use for managing their AWS
accounts into one open source tool. It’s a stateless rules engine for policy definition and enforcement, with metrics and reporting for AWS.

## Uses
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


## Setup
Cloud custodian requires python, pip, virtualenv on client machine

```
Note: 
If you already have AWS credentials configured for AWS CLI i.e. `$USER/.aws/credentials`, then you may omit them on the command line. Otherwise you can add them with the command
(custodian) $ AWS_ACCESS_KEY_ID="foo" AWS_SECRET_ACCESS_KEY="bar"

## Quick Start
```
$ virtualenv --python=python2 custodian
$ source custodian/bin/activate
(custodian) $ pip install c7n

# Validate the configuration
$ custodian validate test.yml

# Perform a dryrun on the policies (no actions executed) to see what would change
$ custodian run --dryrun -s out test.yml

# Run the policy
$ custodian run -s out policy.yml
```

## Concepts
```
name: (Required) Name for the policy
resource: (Required) Which resource type to check (ec2, rds, ebs, etc 100+)
description: Brief statement of what the policy does
mode: How the policy will be executed (event(API Triggered), periodic(Cron Scheduled), config(Config Change Triggered), no mode for manual
runs)
- filters: Narrow down resource matches with 1 or more of these
- actions: What to do with the resulting resource set found. (notify, stop, start, terminate, tag, resize, etc)
```

## Filters
RegEx/fliters use Jmespath expressions: http://jmespath.org/

* Operator matching (in, not-in, absent, not-null, gte, regex, etc)
* Arbitrary nesting of filters with ‘or’ and ‘and’ blocks.
* Simple key/value are equality matches with value expressions
