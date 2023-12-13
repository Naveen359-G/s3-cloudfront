                                                                  # S3-CloudFront
Static website deployment with AWS S3 and CloudFront
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

                                                                   Introduction:
In this tutorial, I will guide you through the process of hosting a static website using Amazon S3 for storage and Amazon CloudFront as a content delivery network (CDN). 
This setup will improve the performance, scalability, and availability of your website.
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

Choose Roles - Click on "Roles" in the left-hand panel.

Create Role - Click "Create role."

Select EC2 - Choose the service that will use this role (EC2 in this case) and click "Next."

Attach Policies - Select the policies that grant access to the necessary S3 resources. For instance, "AmazonS3ReadOnlyAccess" provides read-only access to S3. You can create a custom policy for more specific access.

Add Tags (Optional) - Optionally, add any relevant tags. Click "Next."

Name and Review - Give the role a descriptive name and review the role policy to ensure it aligns with the necessary S3 access requirements.

Create Role - Click "Create role."
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Step 2: Attach IAM Role to EC2 Instance
Go to EC2 Console - Navigate to the EC2 Dashboard.

Select Instance - Choose the EC2 instance you want to grant access to.

Actions > Instance Settings > Attach/Replace IAM Role - Click on "Actions," go to "Instance Settings," and select "Attach/Replace IAM Role."

Choose IAM Role - Select the IAM role you created in Step 1 from the dropdown menu.
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Step 3: Verify S3 Access
SSH into EC2 Instance - Use SSH to connect to the EC2 instance.

Test Access - Use AWS Command Line Interface (CLI) or SDKs within the EC2 instance to verify S3 access. For instance, you can run aws s3 ls to list buckets.


--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------




















