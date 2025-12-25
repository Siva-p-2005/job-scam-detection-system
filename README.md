Job Scam Detection System – Implementation Steps

This section explains the complete step-by-step process followed to build and deploy the Job Scam Detection System using AWS serverless services.

STEP 1: Create DynamoDB Table (Database)

The DynamoDB table is used to store all job scan results.

Open AWS Management Console

Navigate to DynamoDB

Click Create table

Table Name: JobScanResults

Partition Key: scanId (String)

Click Create table

This table stores job descriptions, detected scam indicators, risk levels, and scan timestamps.

STEP 2: Create IAM Role for Lambda

An IAM role is required to allow Lambda functions to access DynamoDB and CloudWatch.

Open IAM service

Go to Roles → Create role

Select AWS Service and choose Lambda

Attach the following policies:

AmazonDynamoDBFullAccess

CloudWatchLogsFullAccess

Role Name: JobScamLambdaRole

Click Create role

This role provides necessary permissions for backend processing.

STEP 3: Create Lambda Function – Job Scan Function

This Lambda function analyzes job descriptions and detects scam indicators.

Open AWS Lambda

Click Create function

Choose Author from scratch

Function Name: fake_job_detector

Runtime: Python 3.10

Execution Role: JobScamLambdaRole

Click Create function

This function processes job descriptions, classifies risk levels, and stores results in DynamoDB.

STEP 4: Create Lambda Function – Scan History Function

This Lambda function retrieves previously scanned job records.

Create another Lambda function

Function Name: scan_history

Runtime: Python 3.10

Execution Role: JobScamLambdaRole

Click Create function

This function fetches and returns all scan records from DynamoDB in sorted order.

STEP 5: Test Lambda Functions

Lambda testing is performed using built-in test events.

The job scan function is tested using a sample job description

The scan history function is tested without input parameters

Successful execution confirms correct classification and database storage

STEP 6: Create API Gateway (HTTP API)

API Gateway connects the frontend to Lambda functions.

Open API Gateway

Click Create API

Select HTTP API

Configure routes:

POST /scan → Job Scan Lambda

GET /history → Scan History Lambda

Enable CORS

Allow methods: GET, POST

Deploy the API

Copy the Invoke URL

This API acts as the communication layer between frontend and backend.

STEP 7: Create S3 Bucket (Frontend Hosting)

Amazon S3 is used to host the frontend web application.

Open Amazon S3

Click Create bucket

Provide a unique bucket name

Disable Block all public access

Create the bucket

The bucket stores frontend HTML files.

STEP 8: Upload Frontend Files

Upload index.html (job scan page)

Upload history.html (scan history page)

Ensure files are publicly accessible

These pages allow users to interact with the system.

STEP 9: Enable Static Website Hosting

Go to S3 bucket Properties

Enable Static website hosting

Set index document as index.html

Save changes

Copy the website endpoint URL

This URL is used to access the application.

STEP 10: Verify Complete Application Flow

Open the S3 website URL

Enter a job description

Submit for risk analysis

View risk classification (LOW / MEDIUM / HIGH)

Navigate to history page

Confirm previous scan records are displayed

Successful execution confirms end-to-end functionality.

Conclusion

The Job Scam Detection System is successfully implemented using AWS serverless services. The system demonstrates real-time job scam detection, secure data storage, and scalable architecture without the need for server management.
