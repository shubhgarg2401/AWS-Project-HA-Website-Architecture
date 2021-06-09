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

------------



### PROJECT WALKTHROUGH

##### Creating S3 bucket :

We will create two S3 bucket for this project. First one will store Media Files and the other will store all the code configuration from wordpress. This will act as a backup if something goes wrong with our website.
![creating s3 bucket config 1](https://user-images.githubusercontent.com/53488130/121358085-f41be400-c94f-11eb-8a9c-a49c6d22dafa.PNG)
![creating s3 bucket config 2](https://user-images.githubusercontent.com/53488130/121358100-f67e3e00-c94f-11eb-80c9-83f74ba1e3bb.PNG)
![s3 buckets 3](https://user-images.githubusercontent.com/53488130/121358096-f5e5a780-c94f-11eb-86e8-73100f173eb3.PNG)


##### Creating a CloudFront Distribution :

We will create a cloudfront distribution in which the origin path should be our media bucket since the media will be fetched through cloudfront.
![cloudfront 1](https://user-images.githubusercontent.com/53488130/121358278-1c0b4780-c950-11eb-817f-68cec9cbe247.PNG)
![cloudfront 2](https://user-images.githubusercontent.com/53488130/121358285-1dd50b00-c950-11eb-841b-86bc9609dff3.PNG)

##### Creating two Security Groups :

We create two Security Groups, one for the Ec2 instance and the other for the RDS instance. The EC2 SG will allow ssh and Public access while the RDS sg can only be accessed by the ec2 instance.
![final SG created for both](https://user-images.githubusercontent.com/53488130/121358496-4bba4f80-c950-11eb-8a0d-52e822746b89.PNG)
![inbound rules for RDS SG](https://user-images.githubusercontent.com/53488130/121358503-4d841300-c950-11eb-8046-c1c03dcb5742.PNG)
![2nd SG for RDS](https://user-images.githubusercontent.com/53488130/121358504-4e1ca980-c950-11eb-8b61-41d9c5ce19c2.PNG)
![sg inbound](https://user-images.githubusercontent.com/53488130/121358508-4eb54000-c950-11eb-9d56-a10c645821b2.PNG)
![sg1](https://user-images.githubusercontent.com/53488130/121358510-4f4dd680-c950-11eb-97f7-e5a6452bd605.PNG)

##### Creating a RDS Instance :

We choose a MySQL database under free tier of aws RDS . We do not get Multi Az under this config. but it can be used with the other two options.
We use our RDS SG as our security Group so that we our architecture the way we want.
Other settings are left as default with snapshots and backups disabled to save money.

###### configurations :
- My SQL version 5.7.33
- FreeTier
- DataBase name : shubhgargdb
- Instance : t2.micro
- SG : shubhgarg-RDS-SG (not the default)

##### Creating IAM Role :

We are creating an IAM Role so that Our EC2 user can access the S3 Bucket to send data and fetch it.

The Policy is attached by searching S3FullAccess. Provide a suitable name and create it.
![s3 access](https://user-images.githubusercontent.com/53488130/121359425-18c48b80-c951-11eb-9931-5c6dbaf37102.PNG)


##### Launching EC2 Instance :

We launch an ec2 instance using Linux AMI 2 as our base image. In the configuration page we attach our IAM role(s3 fullaccess) and add the script  "bootstrap script" given in place of User Data. The security group is our EC2 SG.
The rest is left default.
![sg for instance](https://user-images.githubusercontent.com/53488130/121359505-2da11f00-c951-11eb-933b-b157c2b1dcf2.PNG)
![adding the bootstrap script to ec2 instance](https://user-images.githubusercontent.com/53488130/121359512-2f6ae280-c951-11eb-80eb-0237c240dfe8.PNG)
![creating an instance](https://user-images.githubusercontent.com/53488130/121359515-309c0f80-c951-11eb-9578-291e180691a8.PNG)

##### Wordpress Setup :

Take the IPv4 Address and access the setup page in browser.

![wordpress setup](https://user-images.githubusercontent.com/53488130/121359569-3eea2b80-c951-11eb-9f48-f313dec029a3.PNG)
![wordpress installation](https://user-images.githubusercontent.com/53488130/121359823-7eb11300-c951-11eb-83c4-50c7d686724e.PNG)
![wordpress website dashboard](https://user-images.githubusercontent.com/53488130/121359837-82449a00-c951-11eb-8e4d-c1377c94a720.PNG)


##### Creating Backup in S3 :

Using SSH we can access our instance and copy all our files into s3 bucket using the command:

aws s3 cp --recursive /var/www/html/wp-content s3://yourbucketname

![top copy the media files from wordpress to s3 bucket](https://user-images.githubusercontent.com/53488130/121359679-59bca000-c951-11eb-81d4-5be954b2e03e.PNG)


##### Creating Application Load Balancer

We create a Application Load Balancer, continue to setup according to your region. Provide the ec2 Security Group.

In Health Check section provide the path as awsproject.html since it was created using our script code. This will perform health check by accessing it. Now using DNS name of ALB we can easily access our wordpress website.
![creating an application load balancer](https://user-images.githubusercontent.com/53488130/121359712-60e3ae00-c951-11eb-912d-f8c1e86fb88a.PNG)
##### Creating AMI from EC2 instance

AMI is created so that it could be used later in our Launch Configuration:
![create ec2 image](https://user-images.githubusercontent.com/53488130/121359757-6e993380-c951-11eb-9220-d0b802c6d947.PNG)


##### Creating Launch Configuration

Create launch configuration by providing our AMI and setting up the instance according to our ec2 instance.



##### Creating AutoScaling Group

Now we create ASG using our Launch Configuration We can provide scaling policies and Minimum and Maximum instances to be launched. In my case I added 2 minimum instances.

##### Cloudfront Setup for Wordpress

We need to edit the .htaccess file and provide Cloudfront Origin Host address as shown in the screenshot below:
![cloudfront setup](https://user-images.githubusercontent.com/53488130/121360330-eff0c600-c951-11eb-87ab-4a365387e914.jpg)


##### Route53 Configuration

![route53 hostedzone](https://user-images.githubusercontent.com/53488130/121359976-a2745900-c951-11eb-9589-77077b4eaca1.PNG)
![create route 53 hostedzone](https://user-images.githubusercontent.com/53488130/121359983-a4d6b300-c951-11eb-9234-9fa8304148ff.PNG)

In Route Traffic To add your ALB DNS.

Note: Update the NameServers found in Your AWS hosted zone to your domain provider settings

### Website Setup is Done!

------------


**Other Resources Used :-**

For free Domain Name (freenom) : [http://https://www.freenom.com/en/index.html?lang=en](http://https://www.freenom.com/en/index.html?lang=en)

Wordpress setup : [http://https://wordpress.org/download/](http://https://wordpress.org/download/)
