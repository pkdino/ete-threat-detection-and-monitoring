End-to-End Threat Detection and Monitoring using Python and AWS

🛠️ Phase 1: Set Up the AWS Environment

Create a Dedicated AWS Account

Sign up for a free-tier AWS account.

Set up IAM roles and policies for secure access.

Use the AWS Identity and Access Management (IAM) best practices for least privilege.

Enable Logging and Monitoring Services

Activate AWS CloudTrail for API logging.

Set up VPC Flow Logs for network traffic analysis.

Use CloudWatch Logs for application and system logging.

🔴 Phase 2: Simulating a Cyber Attack (Red Team)

Install Attack Tools on Kali Linux (Attacker Machine)

sudo apt update && sudo apt install -y nmap hydra gobuster

Simulate a Brute Force Attack on an EC2 Instance (SSH)

hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://<TARGET_IP>

Expected Behavior:

Logs of failed SSH login attempts should appear in CloudWatch.

CloudTrail will record API calls related to instance management.

Simulate Data Exfiltration via S3 Bucket (Optional)

aws s3 cp s3://<BUCKET_NAME>/sensitive_data.txt ./

Expected Behavior:

CloudTrail logs should capture the S3 GET request.

🔵 Phase 3: Detecting Attacks in AWS (Blue Team)

Detect SSH Brute Force (Failed Logins)

Use GuardDuty to detect unusual login attempts.

Create custom CloudWatch metric filters for SSH login failures.

Detect Data Exfiltration (S3 Access)

Set up S3 bucket logging and use Athena for log analysis.

Create an alert for suspicious data transfers using EventBridge.

🤖 Phase 4: Automate Response with AWS Lambda

Create a Lambda Function to Block Brute Force IPs

Python Script for the Lambda Function:

import boto3

def lambda_handler(event, context):
    ip_address = event.get('sourceIPAddress')
    if ip_address:
        client = boto3.client('ec2')
        response = client.create_network_acl_entry(
            NetworkAclId='<YOUR_ACL_ID>',
            RuleNumber=100,
            Protocol='-1',
            RuleAction='deny',
            Egress=False,
            CidrBlock=f'{ip_address}/32',
            PortRange={'From': 0, 'To': 65535}
        )
        return f"Blocked IP: {ip_address}"

Save and test the function.

Use EventBridge to trigger this function based on CloudTrail events.

Create a Lambda Function to Isolate Compromised Instances

Python Script for the Lambda Function:

import boto3

def lambda_handler(event, context):
    instance_id = event.get('detail', {}).get('instance-id')
    if instance_id:
        client = boto3.client('ec2')
        response = client.modify_instance_attribute(
            InstanceId=instance_id,
            DisableApiTermination={'Value': True}
        )
        return f"Isolated Instance: {instance_id}"

Save and test the function.

Use EventBridge or SNS to trigger this function based on GuardDuty findings.

📊 Phase 5: Incident Response Dashboard & Report

Create a Real-Time Monitoring Dashboard

Use Amazon QuickSight or OpenSearch to visualize key security metrics.

Create panels for real-time attack detection and automated responses.

Generate an Incident Response Report

Include:

✅ Attack simulation details

✅ Detected events in CloudTrail and VPC Flow Logs

✅ Automated Lambda responses

✅ Dashboard screenshots

🎯 Expected Outcome & Deliverables

✅ AWS Environment with CloudTrail, VPC Flow Logs, and GuardDuty✅ Simulated Attacks (Brute Force, Data Exfiltration)✅ Threat Detection in AWS✅ Automated Incident Response (Block IPs, Isolate Instances)✅ AWS Dashboard for Monitoring Attacks & Responses✅ Incident Report with attack logs & automated response actions
