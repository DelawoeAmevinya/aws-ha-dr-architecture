AWS Infrastructure Decommissioning Guide
⚠️ Important Notice
This guide will permanently delete all resources created during this project.

All data will be lost
Action is irreversible
Follow the exact order to avoid errors
Estimated time: 30-45 minutes
Expected cost savings: ~$195/month


📋 Pre-Decommission Checklist

 All screenshots captured and uploaded to GitHub
 Documentation is complete
 Any important data backed up locally
 Ready to delete everything


🗑️ Decommissioning Steps
CRITICAL: Follow this exact order to avoid dependency errors!

Phase 1: Stop Monitoring (5 minutes)
Step 1: Delete CloudWatch Alarms

Go to CloudWatch Console → Alarms
Select all alarms you created
Click "Actions" → "Delete"
Confirm deletion

Step 2: Delete CloudWatch Dashboard

Go to CloudWatch Console → Dashboards
Select prod-ha-dashboard
Click "Actions" → "Delete dashboard"
Confirm deletion

Step 3: Delete SNS Topic

Go to SNS Console → Topics
Select prod-critical-alerts
Click "Delete"
Type "delete me" and confirm


Phase 2: Remove Compute Resources (10 minutes)
Step 4: Delete Auto Scaling Group

Go to EC2 Console → Auto Scaling Groups
Select web-app-asg
Click "Actions" → "Delete"
Type "delete" to confirm
Click "Delete"
WAIT 3-5 minutes for all instances to terminate

✅ This automatically terminates all EC2 instances
Step 5: Delete Load Balancer

Go to EC2 Console → Load Balancers
Select prod-alb
Click "Actions" → "Delete load balancer"
Type "confirm" and click "Delete"
WAIT 2-3 minutes for deletion

Step 6: Delete Target Group

Go to EC2 Console → Target Groups
Select web-targets
Click "Actions" → "Delete"
Confirm deletion

Step 7: Delete Launch Template

Go to EC2 Console → Launch Templates
Select web-app-template
Click "Actions" → "Delete template"
Confirm deletion


Phase 3: Remove Database (15 minutes)
Step 8: Delete RDS Database
⚠️ THIS DELETES ALL YOUR DATABASE DATA!

Go to RDS Console → Databases
Select prod-db-primary
Click "Actions" → "Delete"
Uncheck "Create final snapshot" (we don't need it)
Uncheck "Retain automated backups"
Type "delete me" to confirm
Click "Delete"
WAIT 10-15 minutes for deletion to complete

💰 Saves ~$90/month
Step 9: Delete RDS Snapshots (if any)

Go to RDS Console → Snapshots
Select any snapshots for your database
Click "Actions" → "Delete snapshot"
Confirm deletion


Phase 4: Remove Storage (10 minutes)
Step 10: Empty and Delete Primary S3 Bucket
⚠️ THIS DELETES ALL FILES PERMANENTLY!

Go to S3 Console
Select prod-app-data-primary-[your-id]
Click "Empty"
Type "permanently delete" and click "Empty"
Wait for completion, then click "Exit"
With bucket selected, click "Delete"
Type the bucket name exactly
Click "Delete bucket"

Step 11: Empty and Delete DR S3 Bucket

Select prod-app-data-dr-[your-id]
Click "Empty"
Type "permanently delete" and click "Empty"
Wait for completion, then click "Exit"
With bucket selected, click "Delete"
Type the bucket name exactly
Click "Delete bucket"

💰 Saves ~$10/month

Phase 5: Remove Networking (10 minutes)
Step 12: Delete NAT Gateway

Go to VPC Console → NAT Gateways
Select your NAT gateway(s)
Click "Actions" → "Delete NAT gateway"
Type "delete" and confirm
WAIT 5 minutes for deletion

💰 Saves ~$35/month
Step 13: Release Elastic IP

Go to VPC Console → Elastic IPs
Select the Elastic IP(s) that were used by NAT gateway
Click "Actions" → "Release Elastic IP address"
Confirm release

Step 14: Delete VPC

Go to VPC Console → Your VPCs
Select prod-vpc-ha
Click "Actions" → "Delete VPC"
Review what will be deleted (subnets, route tables, etc.)
Type "delete" to confirm
Click "Delete"

If deletion fails:

Check for remaining network interfaces
Go to Network Interfaces → Delete any unused ones
Try deleting VPC again


Phase 6: Clean Up Additional Resources (5 minutes)
Step 15: Delete AWS Backup Plan (if created)

Go to AWS Backup Console → Backup plans
Select prod-backup-plan
Click "Delete"
Confirm deletion

Step 16: Delete Recovery Points (if any)

Go to AWS Backup Console → Protected resources
Click on each resource to view recovery points
Select all recovery points
Click "Actions" → "Delete"
Type "delete" and confirm

Step 17: Delete CloudWatch Log Groups

Go to CloudWatch Console → Logs → Log groups
Select any log groups related to your project:

/aws/rds/instance/prod-db-primary/*
/aws/elasticloadbalancing/app/prod-alb/*


Click "Actions" → "Delete log group(s)"
Confirm deletion


✅ Final Verification
Check Everything is Deleted
EC2:

 No running instances
 No load balancers
 No target groups
 No Auto Scaling groups
 No launch templates

RDS:

 No databases
 No snapshots

S3:

 No project buckets

VPC:

 Custom VPC deleted
 No NAT gateways
 No Elastic IPs (other than default)

CloudWatch:

 No alarms
 No dashboards

SNS:

 No custom topics


💰 Verify Cost Savings
Check Your AWS Billing

Go to Billing Console → Bills
Check current month charges
Over next 24-48 hours, charges should drop to near $0
Resources may take up to 48 hours to stop billing

Set Up Billing Alert (Recommended)
To prevent future unexpected charges:

Go to Billing Console → Budgets
Click "Create budget"
Select "Cost budget"
Set monthly budget: $10 (or your preference)
Set alert at 80% ($8)
Enter your email
Click "Create budget"


🔍 Troubleshooting Common Issues
"Cannot delete VPC - has dependencies"
Solution:

Go to VPC Console → Network Interfaces
Filter by your VPC
Delete any unused network interfaces
Try deleting VPC again

"Cannot delete security group"
Solution:

Check if any instances still using it
Check if other security groups reference it
Delete or modify those references first

"S3 bucket won't empty"
Solution:

Make sure versioning is suspended
Delete all versions manually if needed
Try emptying again

"Still seeing charges"
Possible causes:

EBS volumes left behind (check EC2 → Volumes)
EBS snapshots (check EC2 → Snapshots)
Elastic IPs not released
Data transfer charges (will stop after buckets deleted)

To check:

Go to each service console
Verify no resources remain
Wait 24-48 hours for billing to update


📊 Expected Results
After completing decommissioning:
Cost Reduction

Before: ~$195/month
After: ~$0/month
Savings: ~$195/month

Timeline

Monitoring/Compute: Stops billing immediately
RDS: Stops billing after deletion completes (~15 min)
S3: Stops billing after buckets deleted
NAT Gateway: Stops billing after deletion (~5 min)
Data transfer: May see small charges for last hour

📝 Decommissioning Completion Checklist
□ Phase 1: Monitoring deleted (CloudWatch, SNS)
□ Phase 2: Compute deleted (ASG, ALB, EC2)
□ Phase 3: Database deleted (RDS)
□ Phase 4: Storage deleted (S3 buckets)
□ Phase 5: Networking deleted (VPC, NAT Gateway)
□ Phase 6: Additional resources cleaned up
□ Final verification completed
□ Billing dashboard checked
□ Budget alert created
□ All screenshots saved to GitHub
□ Documentation completed


Last Updated: [15/02/2026]
Total Time to Decommission: 30-45 minutes
Monthly Cost Savings: ~$195
