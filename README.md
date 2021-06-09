# AWS HIGH AVAILABILITY WEBSITE (PROJECT)

The project highlights the working of various aws services to form a highly available architecture for website hosting.

## SERVICES USED IN THE PROJECT

- EC2 Instance
- AMI
- Application Load Balancer
- CloudFront
- RDS MySQL
- Security Groups
- Launch Configuration
- Auto Scaling Group
- Route 53
- S3 Bucket
- IAM

## THE APPROACH

The idea was to host a static website using S3 and EC2 . Increase it's availability using cloudfront and Route 53.

### PROJECT WALKTHROUGH

##### Creating S3 bucket :

We will create two S3 bucket for this project. First one will store Media Files and the other will store all the code configuration from wordpress. This will act as a backup if something goes wrong with our website.

##### Creating a CloudFront Distribution :

We will create a cloudfront distribution in which the origin path should be our media bucket since the media will be fetched through cloudfront.

##### Creating two Security Groups :

We will create Security Group for our EC2 instance and RDS Instance EC2 allows ssh and public access while RDS can be accessed only by ec2 thats why The SG for ec2 is present in SG of RDS.

##### Creating a RDS Instance :

