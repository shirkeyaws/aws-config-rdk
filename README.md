# AWS Config Rules Development Kit (aws-config-rdk)

## Overview

The **AWS Config Rules Development Kit** (or **aws-config-rdk**) is a command line interface (CLI) to help set up, author and test custom AWS Config rules.

With **aws-config-rdk**, you can evaluate the configuration settings of your AWS resources. You do this by creating AWS Config rules, which represent your ideal configuration settings. AWS Config provides customizable, predefined rules called managed rules to help you get started. You can also create your own custom rules. For more information, [see the AWS Config documentation](http://docs.aws.amazon.com/config/latest/developerguide/evaluate-config.html).

## Getting Started

**aws-config-rdk** requires you to have AWS security credentials. For procedures to get security credentials, [see the documentation](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html)

### 1. Install the AWS Config RDK

Install the aws-config-rdk by downloading/cloning the [official project repository](https://github.com/awslabs/aws-config-rdk). For more details, see the [Installation](#Installation) section below.

### 2. Initialize your AWS account

Run the following command:

`aws-config-rdk init`

This will initialize your AWS environment to ensure that AWS Config is enabled, and to turn on the necessary mechanisms to support monitoring of environment changes in order to trigger AWS Config Rules to ensure compliance.

### 3. Create your new custom AWS Config rule

You will now create a custom AWS Config rule. You will create this new custom rule with a specific name, which will then create a folder in your current path with a custom rule template for you to begin creating your rule. For instance, the following command:

`aws-config-rdk create hello-world`

will generate a subfolder named hello-world containing Python 3 scripts that will be combined to form your new custom rule in production.

> *While other AWS Lambda runtime languages are supported, we have chosen Python 3 to demonstrate the aws-config-rdk workflow. To find out further options for initializing your new custom rule, see the [CLI Reference](#CLI-Reference) below.*

## 4. Edit the custom rule

The new hello-world rule will now be contained in a folder called hello-world, which will have the following contents:

- hello-world
  - main.py
  - TODO: what other contents will be in this folder?

**The _only_ file that requires further editing for evaluating compliance is the main.py file above (for languages other than Python 2/3, this file will have a different name -- see more in [Editing Rules](#Editing-Rules)**

## 5. Deploy the custom rule

TODO `aws-config-rdk deploy hello-world`

## 6. Test the custom rule in production

TODO `aws-config-rdk test hello-world`

## Editing Rules

TODO Deeper explanation of how rules are triggered(periodic/invoking events) and how results should be returned to AWS Config Rules to determine compliance or non-compliance (or if the specific compliance check is not applicable).

## CLI Reference

The aws-config-rdk command consists of a number of subcommands that represent different actions related to the lifecycle and monitoring of custom AWS Config rules in your AWS environment.

### init

The `init` subcommand helps to correctly configure your AWS enviroment to create and use custon AWS Config rules. Those steps include:

- enabling AWS Config

The subcommand runs with defaults for 

This subcommand should be run when first initializing your AWS environment and is only required once per AWS account in which you intend to run AWS Config rules.

### create

The `create` subcommand is the first step to creating a new custom rule. Additional arguments can be specified as follows:

`create {rule-name}` - creates a new directory named {rule-name} containing configuration files and scripts
that will be used to create the underlying AWS Lambda function with common defaults for language runtime (python3.6) and trigger ('periodic' every 24 hours) for the custom rule. These defaults can be overridden with appropriate switches. Those switches are demonstrated below, where the two commands that follow are equivalent:

- `aws-config-rdk create hello-world`
- `aws-config-rdk create hello-world --runtime python3.6 --periodic TwentyFour_Hours`

#### create --runtime

The `--runtime` switch specifies the AWS Lambda runtime for the custom AWS rule. You can choose the specific programming language for your rule based on the possible AWS Lambda runtimes available, as [listed here](http://docs.aws.amazon.com/cli/latest/reference/lambda/create-function.html) under the Runtime argument description. If you do not specify an AWS Lambda runtime, the default of `python3.6` will be used. For instance, the following commands are equivalent:
- `aws-config-rdk create hello-world`
- `aws-config-rdk create hello-world --runtime python3.6`

For a custom rule using the most NodeJS runtime, you would specify:
`aws-config-rdk create hello-world --runtime nodejs6.10`

#### create --periodic {frequency}

> **The `--periodic` switch cannot be used with the `--event` switch, and if `--event` is not specified, the default case is `--periodic`.**

The `--periodic` switch specifies the trigger type for executing the custom rule as one based on periodic frequency. If no {frequency} is provided, the default {frequency} of TwentyFour_Hours will be used, meaning that the rule will be run on a periodic basis within one day maximum frequency. The following commands are equivalent:

- `aws-config-rdk create hello-world`
- `aws-config-rdk create hello-world --periodic`
- `aws-config-rdk create hello-world --periodic TwentyFour_Hours`

Possible values for **{frequency}** are:

- One_Hour
- Three_Hours
- Six_Hours
- Twelve_Hours
- TwentyFour_Hours

For more information, see [AWS Config documentation](http://docs.aws.amazon.com/config/latest/APIReference/API_ConfigRule.html).

#### create --event {trigger}

> **The `--periodic` switch cannot be used with the `--event` switch, and if `--event` is not specified, the default case is `--periodic`.**

If the custom rule is to be triggered based on specific invoking events -- ie. as a result of specific changes to your AWS environment, such as the creation of a new EC2 instances, or updating tags for S3 buckets -- then use the `--event` switch followed b y the {trigger} representing the specific event that will trigger the custom rule.

For example, the following will create a custom rule that will trigger based on any changes to EC2 instances:

`aws-config-rdk create hello-world --event AWS::EC2::Instance`

For triggering based on any changes to IAM Users in your account:

`aws-config-rdk create hello-world --event AWS::IAM:User`

> While AWS Config supports invoking AWS Config rules based on many events reflecting changes in resources as defined by the [Cloudformation resource types](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html), not all resource types are currently supported -- for more information, see the [AWS Config documentation](http://docs.aws.amazon.com/config/latest/developerguide/evaluate-config_develop-rules_nodejs.html#creating-custom-rules-for-additional-resource-types)

### deploy

TODO

## Installation

You can install the **AWS Config Rules Development Kit** by downloading the source code directly from the official project site, located [here](https://github.com/awslabs/aws-config-rdk).

Once downloaded, you can add the appropriate project path  to your default path as follows (where {project path} is the location where the source code was downloaded):

- For Linux/Mac OS: 
  - `export PATH=$PATH:{project path}/MacLinux`
- For Windows OS:
  - `set PATH=%PATH%;{project path}/Windows`

To make these changes permanent, you can alter your system path depending on your specific operating system and system shell.

## Testing the installation

From your command line/shell, test that the aws-config-rdk is correctly installed, and confirm the most recent version, by entering:

`aws-config-rdk --version`

If your installation worked correctly and is updated, you should see the following result:

`AWS Config Rules DevelopmentKit v0.0.1`

## Testing the AWS environment
You can test that your AWS environment is reachable and has AWS Config enabled

`aws-config-rdk --check-env`

If your AWS environment is reachable, you should see a diagnostic result similar to the following:

```
AWS Environment Summary
-----------------------
AWS Account: 012345678901
AWS Config: Enabled
```

where values for AWS Account will reflect your AWS Account.

If `AWS Config: Disabled` is displayed, you will need to enable AWS Config via the `aws-config-rdk init` command, which will enable AWS Config and necessary components for deploying and running AWS Config rules.