### Frontend
#### upload frontend files
1. go to **S3 bucket**
2. create bucket. Fill in "Bucket name". Uncheck all "Block Public Acess settings for this bucket", acknowledge the warning. Click "Create Bucket".
3. open the bucket, upload frontend website files.
#### configure s3
for stactic website hosting  
1. open the bucket. Go to "Properties". Edit "Static website hosting" to "Enable", add "index.html" to "Index document". Save changes.
2. open "Permission" and add policy.
```
{
    "Version": "2012-10-17",
    "Statement": [
        {   "Sid":"PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::MY_BUCKET_NAME/*"
        }
    ]
}
```
### Database
1. Go to **DynamoDB**. Click "Create Table". Fill in table name, partition key (`id:String`). Click "Create Table".
2. Click "Actions" - "Explore items" - "Create Item". Add data (JSON View is easier).

### Lambda
create functions and set permissions.  
1. Go to **Lambda**. Click "Create function".
2. Fill in function name. Choose `Node.js 16.x` as Runtime. Click "Create function".
3. Add codes in "Code source".
```
const AWS = require("aws-sdk");
const dynamoDB = new AWS.DynamoDB.DocumentClient();
exports.handler = async (event) => {
    const params = { TableName: "TABLE_NAME" };
    const data = await dynamoDB.scan(params).promise();
    const response = {
        statusCode: 200,
        body: JSON.stringify(data.Items),
    };
    return response;
}
```
4. Click "Deploy".
5. Go to "Configuration" (the same line as Code, Test, Monitor,...).
6. Click "Permissions" (sidebar). Click the role name.
7. Click "Add permissions" - "Attach policies".
8. Select "Amazon DynamoDBFullAccess", click "Add permissions".
### API Gateway
set routes  
1. Go to **API Gateway**.
2. Click "Build" for "REST API".
3. Fill in API name, click "Create API".
4. Click "Create Resource". Fill in name, click "Create Resource".
5. Click "Create Method".
6. Select "GET", "Lambda function". Select the lambda function created before. Click "Create Method",
7. Click "Deploy API". Select "\*New stage\*". Fill in name and click "Deploy".
8. Go to "Resources", click “Enable CORS” for the route. Select "GET" and click "Save".
9. Deploy API again. Select the stage created in step 7, and Deploy.
10. Add the generated "Invoke URL" in API stages into the frontend files for fetching data.

### Reference:
[Building Serverless Applications in AWS](https://www.linkedin.com/learning/building-serverless-applications-in-aws)  
[Build Full-Stack Apps with Serverless and React on AWS](http://s3-bnb.s3-website-us-east-1.amazonaws.com/s3-bnb-serverless/)
