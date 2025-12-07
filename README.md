# Orders ETL Pipeline

A serverless ETL pipeline on AWS that transforms JSON order data into Parquet format for analytics.

## Overview

Processes nested JSON orders from S3, flattens the data structure, and stores it as Parquet files in a data lake with automatic metadata cataloging via AWS Glue.

## Architecture

```
S3 (JSON) → Lambda → Flatten Data → Parquet → S3 (Data Lake) → Glue Crawler
```

## Features

- **Serverless Processing** - AWS Lambda with automatic scaling
- **Data Flattening** - Converts nested JSON to flat relational format
- **Parquet Storage** - Efficient columnar format for analytics
- **Automated Cataloging** - AWS Glue Crawler updates metadata
- **Timestamp Versioning** - Track processed files by date/time

## Quick Start

### 1. Create S3 Buckets
```bash
aws s3 mb s3://orders-input-bucket
aws s3 mb s3://orders-data-lake
```

### 2. Deploy Lambda Function
```bash
zip lambda_function.zip lambda_function.py
aws lambda create-function \
  --function-name orders-etl-pipeline \
  --runtime python3.11 \
  --role arn:aws:iam::ACCOUNT_ID:role/lambda-role \
  --handler lambda_function.lambda_handler \
  --zip-file fileb://lambda_function.zip
```

### 3. Configure S3 Trigger
```bash
aws s3api put-bucket-notification-configuration \
  --bucket orders-input-bucket \
  --notification-configuration '{"LambdaFunctionConfigurations": [{"LambdaFunctionArn": "arn:aws:lambda:REGION:ACCOUNT_ID:function:orders-etl-pipeline", "Events": ["s3:ObjectCreated:*"]}]}'
```

### 4. Upload Data
```bash
aws s3 cp orders_etl.json s3://orders-input-bucket/
```

## Data Schema

**Input:** Nested JSON with orders, customers, and products
**Output:** Flattened Parquet with columns:
- order_id, order_date, total_amount
- customer_id, customer_name, email, address
- product_id, product_name, category, price, quantity

## Output Location

```
s3://orders-data-lake/orders_parquet_datalake/orders_ETL_YYYYMMDD_HHMMSS.parquet
```

## Requirements

```
boto3>=1.26.0
pandas>=1.5.0
pyarrow>=10.0.0
```

## Prerequisites

- AWS Account with IAM permissions
- Python 3.9+
- S3 buckets and IAM roles configured

## Query Data

```sql
SELECT order_id, customer_name, product_name, quantity
FROM orders_db.orders_etl_*
WHERE order_date >= '2024-01-10'
ORDER BY order_date DESC;
```

## License

MIT License