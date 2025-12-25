🔵 STEP 1: CREATE DYNAMODB TABLE (DATABASE)

Open AWS Console

Go to DynamoDB

Click Create table

Table details

Table name: JobScanResults

Partition key: scanId (String)

Click Create table

✅ Database ready

🔵 STEP 2: CREATE IAM ROLE FOR LAMBDA

Go to IAM → Roles

Click Create role

Select AWS Service → Lambda

Click Next

Attach policies

AmazonDynamoDBFullAccess

CloudWatchLogsFullAccess

Role name: JobScamLambdaRole

Click Create role

✅ Permissions ready

🔵 STEP 3: CREATE LAMBDA – JOB SCAN FUNCTION

Go to AWS Lambda

Click Create function

Choose Author from scratch

Function settings

Name: fake_job_detector

Runtime: Python 3.10

Role: JobScamLambdaRole

Click Create function

🔹 Lambda Code (Scan Job & Store Result)
import json
import boto3
import uuid
from datetime import datetime

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('JobScanResults')

STRONG_KEYWORDS = [
    "registration fee",
    "pay money",
    "no interview",
    "guaranteed income",
    "work from home"
]

MEDIUM_KEYWORDS = [
    "urgent hiring",
    "whatsapp only",
    "quick money"
]

def lambda_handler(event, context):
    body = json.loads(event['body'])
    job_text = body.get("jobText", "").lower()

    strong_flags = sum(1 for k in STRONG_KEYWORDS if k in job_text)
    medium_flags = sum(1 for k in MEDIUM_KEYWORDS if k in job_text)

    if strong_flags >= 2:
        risk = "HIGH"
    elif strong_flags == 1 or medium_flags >= 1:
        risk = "MEDIUM"
    else:
        risk = "LOW"

    item = {
        "scanId": str(uuid.uuid4()),
        "jobText": job_text,
        "strongFlags": strong_flags,
        "mediumFlags": medium_flags,
        "riskLevel": risk,
        "scannedAt": datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    }

    table.put_item(Item=item)

    return {
        "statusCode": 200,
        "headers": {
            "Access-Control-Allow-Origin": "*",
            "Content-Type": "application/json"
        },
        "body": json.dumps(item)
    }


Click Deploy

🔵 STEP 4: CREATE LAMBDA – HISTORY FUNCTION

Create another Lambda

Name: scan_history

Runtime: Python 3.10

Role: same IAM role

🔹 History Lambda Code
import json
import boto3

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('JobScanResults')

def lambda_handler(event, context):
    response = table.scan()
    items = response.get("Items", [])
    items.sort(key=lambda x: x["scannedAt"], reverse=True)

    return {
        "statusCode": 200,
        "headers": {
            "Access-Control-Allow-Origin": "*",
            "Content-Type": "application/json"
        },
        "body": json.dumps(items)
    }


Click Deploy

🔵 STEP 5: TEST LAMBDA FUNCTIONS
Test Event for Scan Lambda
{
  "body": "{\"jobText\":\"Work from home job. No interview. Pay registration fee.\"}"
}


Expected result → HIGH risk

Test Event for History Lambda
{}


Expected → List of scan records

🔵 STEP 6: CREATE API GATEWAY (HTTP API)

Go to API Gateway

Click Create API

Choose HTTP API

Click Build

🔹 Configure Routes
Method	Path	Integration
POST	/scan	fake_job_detector
GET	/history	scan_history

Enable CORS

Allow origin: *

Allow methods: GET, POST

Click Deploy

Copy Invoke URL

Example:

https://abcd123.execute-api.ap-south-1.amazonaws.com

🔵 STEP 7: CREATE S3 BUCKET (FRONTEND)

Go to S3

Click Create bucket

Bucket name:

fake-job-detector-frontend-siva


Uncheck Block all public access

Create bucket

🔵 STEP 8: FRONTEND – index.html

Upload this file to S3:

<!DOCTYPE html>
<html>
<head>
<title>Fake Job Posting Detection</title>
</head>
<body>
<h1>Fake Job Posting Detection System</h1>

<textarea id="jobText"></textarea><br><br>

<button onclick="scan()">Check Job Risk</button>
<button onclick="location.href='history.html'">View Scan History</button>

<div id="result"></div>

<script>
function scan() {
fetch("https://YOUR_API_URL/scan", {
method:"POST",
headers:{ "Content-Type":"application/json" },
body: JSON.stringify({ jobText: document.getElementById("jobText").value })
})
.then(res=>res.json())
.then(data=>{
document.getElementById("result").innerHTML =
"<h2>"+data.riskLevel+" RISK</h2>";
});
}
</script>
</body>
</html>

🔵 STEP 9: FRONTEND – history.html
<!DOCTYPE html>
<html>
<body>
<h2>Scan History</h2>
<table border="1">
<tr>
<th>Time</th>
<th>Job</th>
<th>Strong</th>
<th>Medium</th>
<th>Risk</th>
</tr>
<tbody id="data"></tbody>
</table>

<script>
fetch("https://YOUR_API_URL/history")
.then(res=>res.json())
.then(items=>{
items.forEach(i=>{
document.getElementById("data").innerHTML += `
<tr>
<td>${i.scannedAt}</td>
<td>${i.jobText}</td>
<td>${i.strongFlags}</td>
<td>${i.mediumFlags}</td>
<td>${i.riskLevel}</td>
</tr>`;
});
});
</script>
</body>
</html>

🔵 STEP 10: ENABLE S3 STATIC WEBSITE

S3 → Bucket → Properties

Enable Static website hosting

Index document: index.html

Save

Copy website URL

🔵 STEP 11: VERIFY COMPLETE FLOW

Open S3 website URL

Paste job description

Click Check Job Risk

Result shows HIGH / MEDIUM / LOW

Click View Scan History

History page loads records

✅ END-TO-END WORKING
