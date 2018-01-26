[![Build Status](https://travis-ci.org/opsview/cloud-aws-ec2-cloudwatch.svg?branch=master)](https://travis-ci.org/opsview/cloud-aws-ec2-cloudwatch)

# AWS - EC2 CloudWatch

Amazon Elastic Compute Cloud (Amazon EC2) is a web service that provides secure, resizable compute capacity in the cloud. It is designed to make web-scale cloud computing easier for developers and send metrics to CloudWatch for your EC2 instances making AWS Monitoring Simple.

## What Can You Monitor

Opsview Monitor's AWS EC2 Opspack helps detect issues within your EC2 instance. Opsview provides all of the latest service checks to make sure your EC2 instance is up and running. The EC2 credit usage and balance show the number of CPU credits consumed and available.

## Service Checks

| Service Check | Description |
|:------------- |:----------- |
|AWS/EC2.CPUCreditUsage | Must be T2 instance. The number of CPU credits consumed
|AWS/EC2.CPUCreditBalance | Must be T2 instance. The number of CPU credits available
|AWS/EC2.CPUUtilization | The percentage of allocated EC2 units that are in use
|AWS/EC2.DiskReadOps | The number of completed read operations
|AWS/EC2.DiskWriteOps | The number of completed write operations
|AWS/EC2.DiskWriteBytes | Bytes written to all instance store volumes available to the instance
|AWS/EC2.DiskReadBytes | Bytes read from all instance store volumes available to the instance
|AWS/EC2.NetworkIn | The number of bytes received by the instance
|AWS/EC2.NetworkOut | The number of bytes sent out on all network interfaces by the instance
|AWS/EC2.NetworkPacketsIn | The number of packets received on all network interfaces by the instance
|AWS/EC2.NetworkPacketsOut |The number of packets sent out on all network interfaces by the instance
|AWS/EC2.StatusCheckFailed | Reports whether the instance has passed both the instance status check and the system status check
## Notes

This Opspack knows when it was last run, so when testing the results in the troubleshoot section, you will need to wait a couple minutes each time you recheck the results. The time frame that is searched for is based around the last time the Opspack ran, so running it too quickly will result in no data being found and the service check going into an unknown.

## Prerequisites

There are two ways of adding your authentication credentials to the host. We recommend adding the access key and secret key directly using the variable 'AWS_CLOUDWATCH_AUTHENTICATION'. You can also add the access key and secret key to a file (default /usr/local/nagios/etc/aws_credentials.cfg) in the following format:

```
[default]
aws_access_key_id = "Your Access Key Id"
aws_secret_access_key = "Your Secret Key Id"
```

### Using AWS ARNs

If you have an Access and Secret key for one account, but need to monitoring instances in a different account, you need to make use of AWS roles and ARNs (Amazon Resource Name).

Assuming Account A is the one you have the Access and Secret keys for, and Account B is the one you want to monitor instances in, follow these steps:

* In Account A, within IAM->Users->"Username" for your user with the access key, get the 12 digit UserID (the number within the 'User ARN' on the Summary page)

* In Account B, go to IAM->Roles->Create Role->Another AWS Account, enter the 12 digit number (leave all other options as default), click Next to go to Permissions and assign at least "AmazonEC2ReadOnlyAccess" and "CloudWatchReadOnlyAccess".  Make a note of the ARN when the role is created.

* In Account A, within IAM->Policies->Create Policy, provide a suitable group name (such as 'AccessToEC2InAccountXXX') enter the following JSON, putting in the ARN from the previous step

```
{
    "Version": "2012-10-17",
    "Statement": {
        "Effect": "Allow",
        "Action": "sts:AssumeRole",
        "Resource": "ARN_FROM_THE_PREVIOUS_STEP"
    }
}
```

* In Account A, within IAM->Users->"Uername", 'Add Permission' using the Policy created in the previous step

## Setup and Configuration

To configure and utilize this Opspack, you need to add the 'Cloud - AWS - CloudWatch' Opspack to the host running the EC2 software.
You can supply the Public DNS name of the instance in the host Address or add the instance ID to the 'AWS_CLOUDWATCH_SEARCH_NAME'. If you supply both the Public DNS name and the Instance ID, then add the -p option to the Host Address to activate the mode to check that the public DNS name and instance ID match.

Step 1: Add the host template and if you are using the Public DNS name, add it to the Host Address.

![Add host template](/docs/img/host-template.png?raw=true)

Step 2: Add and configure the 'AWS_CLOUDWATCH_AUTHENTICATION' variable with either the file location or the access key and secret key, depending on your preferred way of supplying the access credential. Add the region you hosted in (default eu-west-1).  Then add and configure the 'AWS_EC2_INSTANCE_ID' by adding the instance ID and the ARN if you are using them.

![Add variable](/docs/img/variable.png?raw=true)

Step 3: Reload and view the EC2 statistics.

![View output](/docs/img/output.png?raw=true)
