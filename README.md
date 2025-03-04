# Serverless File Sharing Platform

This is a simple Serverless File Sharing Platform that allows users to upload and download files using a web interface. The platform is built using AWS Lambda, API Gateway, and Amazon S3. Users can upload a file by writing its content and download it by entering the file name.

## Features

* **Upload a File:** Write the file content and upload it to the platform.
* **Download a File:** Enter the file name to download the file.

## Architecture

The platform uses the following AWS services:

* **AWS Lambda:** Handles file uploads and downloads.
* **API Gateway:** Provides a RESTful API for interacting with the platform.
* **Amazon S3:** Stores the uploaded files.

## Prerequisites

* **AWS Account:** You need an AWS account with permissions to create Lambda functions, API Gateway, and S3 buckets.
* **Python 3.x:** The Lambda functions are written in Python.
* **GitHub Account:** To host the frontend using GitHub Pages.

## Setup Instructions

1.  **Create an S3 Bucket**
    * Go to the [S3 Console](https://console.aws.amazon.com/s3/).
    * Create a new bucket (e.g., `my-file-sharing-bucket`).
    * Enable **Static Website Hosting** in the bucket properties.

2.  **Create the Lambda Functions**

    * **Upload Function:**
        * Create a new Lambda function named `UploadFunction`.
        * Use the following code:

        ```python
        import json
        import boto3

        s3 = boto3.client('s3')
        BUCKET_NAME = 'my-file-sharing-bucket'

        def lambda_handler(event, context):
            try:
                file_content = event['body']
                file_name = event['queryStringParameters']['fileName']

                s3.put_object(Bucket=BUCKET_NAME, Key=file_name, Body=file_content)

                return {
                    'statusCode': 200,
                    'body': json.dumps('File uploaded successfully!')
                }
            except Exception as e:
                return {
                    'statusCode': 500,
                    'body': json.dumps('Error uploading file: ' + str(e))
                }
        ```

    * **Download Function:**
        * Create a new Lambda function named `DownloadFunction`.
        * Use the following code:

        ```python
        import json
        import boto3
        import base64

        s3 = boto3.client('s3')
        BUCKET_NAME = 'my-file-sharing-bucket'

        def lambda_handler(event, context):
            try:
                file_name = event['queryStringParameters']['fileName']
                file_obj = s3.get_object(Bucket=BUCKET_NAME, Key=file_name)
                file_content = file_obj['Body'].read()

                return {
                    'statusCode': 200,
                    'body': base64.b64encode(file_content).decode('utf-8'),
                    'isBase64Encoded': True,
                    'headers': {
                        'Content-Type': 'application/octet-stream',
                        'Content-Disposition': f'attachment; filename={file_name}'
                    }
                }
            except Exception as e:
                return {
                    'statusCode': 500,
                    'body': json.dumps(f"Error retrieving file: {str(e)}")
                }
        ```

3.  **Create an API Gateway**
    * Go to the [API Gateway Console](https://console.aws.amazon.com/apigateway/home).
    * Create a new API named `my-file-sharing-api`.
    * Create a resource named `/files`.
    * Add two methods:
        * `POST`: Integrate with `UploadFunction`.
        * `GET`: Integrate with `DownloadFunction`.
    * Deploy the API to a stage (e.g., `dev`).

4.  **Host the Frontend on GitHub Pages**
    * Create a new GitHub repository.
    * Upload the `index.html` file to the repository.
    * Enable **GitHub Pages** in the repository settings.

5.  **Update the Frontend Code**
    * Replace the `apiUrl` in the `index.html` file with your API Gateway endpoint:

        ```javascript
        const apiUrl = 'https://<api-id>.execute-api.<region>[.amazonaws.com/dev/files](https://www.google.com/search?q=https://.amazonaws.com/dev/files)';
        ```

## Usage

### Upload a File

1.  Open the website in your browser.
2.  Enter the file name and content.
3.  Click **Upload**.

### Download a File

1.  Enter the file name.
2.  Click **Download**.

## Example

### Upload a File

* **File Name:** `example.txt`
* **File Content:** `Hello, World!`

### Download a File

* **File Name:** `example.txt`
