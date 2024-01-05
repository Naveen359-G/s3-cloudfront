                                                                  # S3-CloudFront
Static website deployment with AWS S3 and CloudFront | Configure S3 file upload notification system
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

                                                                   Introduction:
In this tutorial, I will guide you through the process of hosting a static website using Amazon S3 for storage and Amazon CloudFront as a content delivery network (CDN). 
This setup will improve the performance, scalability, and availability of your website.

And, configuring S3 file upload notification system using SNS, SQS, Lambda function, IAM services
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Prerequisites:
Before you begin, make sure you have the following:

- An AWS account
- AWS CLI installed and configured
- Basic knowledge of HTML and CSS
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

=> Step 1: Create an S3 Bucket
Login to AWS Console: Log in to the AWS Management Console.

Navigate to S3: Go to the S3 service.

Create a Bucket:

- Click on the "Create Bucket" button.
- Choose a unique bucket name (e.g., [Your-S3-Bucket]).
- Select your preferred region.
- Upload Your Website Files:

- Upload your HTML and CSS files to the bucket.
- Set permissions for public read access.

json:

- {
-     "Version": "2012-10-17",
-     "Statement": [
-        {
-            "Effect": "Allow",
-            "Principal": {
-                "Service": "cloudfront.amazonaws.com"
-            },
-            "Action": "s3:GetObject",
-            "Resource": "arn:aws:s3:::[Your-S3-Bucket]/*"
-        }
-    ]
- }

- Create S3 bucket

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
## Please replace 'your-static-website-bucket' with the actual name of your S3 bucket.
This policy grants read access to objects in the specified S3 bucket to CloudFront.

Make sure to check the CloudFront Origin Access Identity (OAI) associated with your CloudFront distribution and replace the "Service": "cloudfront.amazonaws.com" with the correct OAI if you have a custom one.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
=> Step 2: Enable Static Website Hosting
Configure Static Website Hosting:

- Go to the Properties tab of your bucket.
- Click on "Static website hosting."
- Choose "Use this bucket to host a website."
- Specify the index document (e.g., index.html).
- Test Website Hosting:

- Access the provided endpoint or use the bucket URL to test hosting.
- Ensure your website is accessible.
- Static Website Hosting
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

=> Step 3: Create a CloudFront Distribution
Navigate to CloudFront:

- Go to the CloudFront service in the AWS Console.
- Create a Distribution:

- Click on "Create Distribution."
- Choose "Web" distribution.
- Select your S3 bucket as the origin.
- Configure Distribution Settings:

- Set up other configurations based on your preferences, and specify the S3 bucket as the origin.
- Click on "Create Distribution."
- Create CloudFront Distribution
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

=> Step 4: Set Proper Permissions for S3 Bucket
-Grant Read Permissions:
- Go back to the S3 service.
- Select your S3 bucket.
- Navigate to the "Permissions" tab.
- Edit the bucket policy and replace [Your-S3-Bucket] with your actual bucket name.

json

- {
-    "Version": "2012-10-17",
-    "Statement": [
-        {
-            "Effect": "Allow",
-            "Principal": {
-                "Service": "cloudfront.amazonaws.com"
-            },
-            "Action": "s3:GetObject",
-            "Resource": "arn:aws:s3:::[Your-S3-Bucket]/*"
-        }
-    ]
- }

- Update Bucket Policy:
Save the changes to update the bucket policy.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

=> Step 5: Test with CloudFront URL
Wait for Deployment:

- Wait for the CloudFront distribution status to change to "Deployed."
Find CloudFront URL:

- Get the CloudFront distribution domain name (e.g., [Your-CloudFront-Distribution].cloudfront.net).
Access Your Website:

- Open your website using the CloudFront URL.
Verify that the website loads correctly.


--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

                                                                                     Additional configuration:
** Enabling S3 access from an EC2 instance via IAM roles involves a few steps. Here's a detailed guide:
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


Step 1: Create an IAM Role
Go to the IAM Console - Sign in to the AWS Management Console and navigate to the Identity and Access Management (IAM) service.

- Choose Roles - Click on "Roles" in the left-hand panel.

- Create Role - Click "Create role."

- Select EC2 - Choose the service that will use this role (EC2 in this case) and click "Next."

- Attach Policies - Select the policies that grant access to the necessary S3 resources. For instance, "AmazonS3ReadOnlyAccess" provides read-only access to S3. You can create a custom policy for more specific access.

- Add Tags (Optional) - Optionally, add any relevant tags. Click "Next."

- Name and Review - Give the role a descriptive name and review the role policy to ensure it aligns with the necessary S3 access requirements.

- Create Role - Click "Create role."
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Step 2: Attach IAM Role to EC2 Instance
Go to EC2 Console - Navigate to the EC2 Dashboard.

- Select Instance - Choose the EC2 instance you want to grant access to.

- Actions => Instance Settings => Attach/Replace IAM Role - Click on "Actions," go to "Instance Settings," and select "Attach/Replace IAM Role."

- Choose IAM Role - Select the IAM role you created in Step 1 from the dropdown menu.
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Step 3: Verify S3 Access
SSH into EC2 Instance - Use SSH to connect to the EC2 instance.

=> Test Access - Use AWS Command Line Interface (CLI) or SDKs within the EC2 instance to verify S3 access. For instance, you can run aws s3 ls to list buckets.


--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

====================================================================================================================================================================================================================
=> Configure S3 file upload nofication service:
====================================================================================================================================================================================================================

Steps to follow:
1. Create S3 bucket
2. Create SNS Topic
3. Create SQS Queue
4. Setup Lambda Function
   - Create Lambda function
   - Grant permissions
   - Write Lambda function code
5. Configure S3 event trigger
6. Test the notification system
7. Examine logs using AWS CloudWatch
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Creating a File Upload Notification System using Amazon Web Services. The system will monitor the S3 bucket for new file uploads, activate a Lambda function to handle the uploaded files, 
send a notification using Simple Notification Service (SNS), and save details in a Simple Queue Service (SQS) queue for later processing.
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
1. Create S3 Bucket:
   
=> log in to the AWS Management Console and navigate to the Amazon S3 service. 
  - Click on the "Create bucket" button and provide a unique and globally-unique name for the bucket.
  - Choose the appropriate AWS Region and leave the other options at their default values.
  - Once the bucket is created, it will serve as the storage location for the uploaded files.
* We will be adding an S3 event rule to this bucket later.
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
2. Create a SNS Topic:
   - Navigate to the SNS service. Create a new SNS topic. Which will be used for sending notifications. Select Standard, does not care about the order.
   - Subscribe the topic to an email address. Choose the email protocol, enter the endpoint email. 
   - Activate the subscription by confirming the provided email. (This step ensures that notifications are sent to the specified email.)
   
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
3. Create a SQS Queue:
   - Navigate SQS dashboard, click on the "Create Queue" button.
   - Create a new SQS queue. Choose the queue type based on your requirements. In this case, select the "Standard Queue" option. 
     (Standard queues provide at-least-once delivery of messages, making them suitable for scenarios where occasional duplication of messages is acceptable.)
   * Depending on your specific requirements, you may adjust additional settings such as the message retention period and visibility timeout.
     This queue functions as a dedicated repository for storing metadata associated with uploaded files.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
4. Setupi Lambda Function:
   =>  Navigate to Lambda service. Create a new Lambda function, selecting the Python 3.12 runtime environment.
   =>  Grant permissions:
     Navigate to 'Confiuration', check for permission tab on the right side to the console. Lambda functions need specific permissions to interact with other AWS services.


   - Navigate to IAM console, check for the < Lambdafunction_name > Add permissions for AmazonSNSFullAccess  | AmazonSQSFullAccess
     AWS provides execution roles to manage these permissions. Grant permissions to read from the S3 bucket, write to the SQS queue, and publish to SNS.

   * Giving specific and limited permissions to your Lambda function makes it more secure. It means the function only gets the exact rights it requires, following the principle of least privilege.
     
   => Write Lambda Function Code:
   - When a new file is uploaded to an S3 bucket, this function is triggered. It extracts information about the uploaded file,
     such as the bucket name and file key. You can customize the processing logic for your specific needs.
   - After processing, the function sends a success notification using Amazon SNS (Simple Notification Service) to a specified topic.
     Additionally, it stores metadata related to the processed file in an Amazon SQS (Simple Queue Service) queue for further handling or analysis.
     This Lambda function orchestrates the notification system, connecting various AWS services seamlessly.

   -->   Check the code for the Lambda function in this repository.
   -  After defining the function code, deploy your Lambda function by clicking on the "Deploy" button.
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

5. Configure S3 Event Trigger: 
   - Navigate to S3--> Properties --> Event Notifications
   - -> Create event notification -> Enter a test name --scroll down--> enable the Lambda function --> Save changes.
* Configuring the S3 event trigger establishes a seamless connection between your S3 bucket and the Lambda function.
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

6.  Test the notification system:
 - Navigate to S3 service, upload any file.
 - Confirm that you receive a notification as configured, through the chosen SNS protocol (e.g., email).
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
7. Examine logs using AWS CloudWatch:
* To gain deeper insights into the execution of your Lambda function and identify potential issues or optimizations, AWS CloudWatch provides a comprehensive log monitoring solution.

  - Navigate to the CloudWatch service. Look for a log group related to your Lambda function.
  - Log groups are organized by AWS service and function name. Open the log stream to view log events generated during the execution of your Lambda function.
  - Each log event provides information about the function's behavior, including any print statements or errors.

=> Navigate to SQS --> send and receive messages -->  Check for poll for messages.

====================================================================================================================================================================================================================
* The S3 File Upload Notification System project effectively integrates AWS services, orchestrating a seamless workflow from file uploads to notifications and metadata storage.
* Configuring S3 event triggers, testing the system, and examining logs using AWS CloudWatch ensures reliability and provides ongoing monitoring capabilities.
* This simple task offers a concise blueprint for building responsive and scalable systems on the AWS cloud infrastructure, catering to diverse applications across industries.
====================================================================================================================================================================================================================






















