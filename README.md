# AWS Serverless ETL Pipeline

A serverless ETL pipeline on AWS that transforms JSON order data into Parquet format for analytics.

## Overview

Processes nested JSON orders from S3, flattens the data structure, and stores it as Parquet files in a data lake with automatic metadata cataloging via AWS Glue.

## Architecture

```
S3 (JSON) → Lambda → Flatten Data → Parquet → S3 (Data Lake) → Glue Crawler → AWS Athena
```

## Features

- **Serverless Processing** - AWS Lambda with automatic scaling
- **Data Flattening** - Converts nested JSON to flat relational format
- **Parquet Storage** - Efficient columnar format for analytics
- **Automated Cataloging** - AWS Glue Crawler updates metadata
- **Timestamp Versioning** - Track processed files by date/time

