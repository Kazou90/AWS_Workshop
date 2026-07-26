---
title: "Building the Private Analytics Layer"
weight: 54
chapter: false
pre: " <b> 5.4. </b> "
---

### 1. Private Infrastructure Setup Workflow

In this lab section, you will provision an isolated private network layer protecting the Data Warehouse and automated Batch ETL workflows:

```
[ EventBridge (Rate 1 Hour) ] ──▶ [ Lambda ETL (In Private VPC) ]
                                            │
                             ┌──────────────┴──────────────┐
                             │ (S3 Gateway VPC Endpoint)   │ (Postgres Port 5432)
                             ▼                             ▼
                  [ S3 Raw Data Lake ]           [ EC2 DWH: clickstream_dw ]
```

---

### 2. Step 1: Provision VPC, Private Subnet & VPC Endpoints

![VPC Endpoints S3 & SSM](/images/aws-vpc-endpoints-s3-ssm.png)

1. **VPC & Subnet Topology**:
   - VPC CIDR: `10.0.0.0/16` (`SCAJ_Project_VPC`)
   - **Public Subnet**: `10.0.0.0/20` (Route table: `0.0.0.0/0` -> `igw-12345`)
   - **Private Subnet**: `10.0.128.0/20` (No Internet Gateway or NAT Gateway routes)

2. **Provision S3 Gateway VPC Endpoint** (Zero-NAT Cost Optimization):
   - Service: `com.amazonaws.ap-southeast-1.s3` (Type: `Gateway`)
   - Route Table: Attach to **Private Route Table** (`10.0.128.0/20`).
   - Purpose: Grants `SCAJ_Lambda_ETL` direct internal route access to `clickstream-s3-ingest`.

3. **Provision SSM Interface VPC Endpoints**:
   - Create 3 Interface Endpoints attached to the Private Subnet:
     - `com.amazonaws.ap-southeast-1.ssm`
     - `com.amazonaws.ap-southeast-1.ssmmessages`
     - `com.amazonaws.ap-southeast-1.ec2messages`
   - Security Group `sg_ssm_endpoints`: Inbound Port 443 from Private Subnet.

---

### 3. Step 2: Provision PostgreSQL Data Warehouse Schema (`clickstream_dw`)

On the EC2 Private instance `SCAJ_EC2_ShinyDWH`, start PostgreSQL 18 and execute the DDL schema:

```sql
-- Database Initialization
CREATE DATABASE clickstream_dw;
\c clickstream_dw;

-- Primary Clickstream Events DWH Table
CREATE TABLE IF NOT EXISTS public.clickstream_events (
    event_id VARCHAR(64) PRIMARY KEY,
    event_timestamp TIMESTAMP WITH TIME ZONE NOT NULL,
    event_name VARCHAR(64) NOT NULL,
    user_id VARCHAR(128),
    user_login_state VARCHAR(32),
    identity_source VARCHAR(64),
    client_id VARCHAR(128) NOT NULL,
    session_id VARCHAR(128) NOT NULL,
    is_first_visit BOOLEAN DEFAULT FALSE,
    context_product_id VARCHAR(64),
    context_product_name VARCHAR(255),
    context_product_category VARCHAR(128),
    context_product_brand VARCHAR(128),
    context_product_price BIGINT,
    context_product_discount_price BIGINT,
    context_product_url_path TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Analytical Query Indexes for R Shiny Dashboard
CREATE INDEX idx_events_timestamp ON public.clickstream_events (event_timestamp DESC);
CREATE INDEX idx_events_name ON public.clickstream_events (event_name);
CREATE INDEX idx_events_product_id ON public.clickstream_events (context_product_id);
```

---

### 4. Step 3: Deploy Lambda ETL (`SCAJ_Lambda_ETL`)

#### 4.1 Lambda ETL Source Code (Python 3.12 with `psycopg2`)

The Lambda function scans the raw S3 JSON objects from the previous hour, flattens payload attributes, and upserts rows into PostgreSQL DWH:

```python
import os
import json
import boto3
import psycopg2
from datetime import datetime, timedelta, timezone

s3_client = boto3.client('s3')

DB_HOST = os.environ['DWH_HOST']
DB_NAME = os.environ.get('DWH_DATABASE', 'clickstream_dw')
DB_USER = os.environ['DWH_USER']
DB_PASS = os.environ['DWH_PASSWORD']
RAW_BUCKET = os.environ.get('RAW_BUCKET', 'clickstream-s3-ingest')

def lambda_handler(event, context):
    # 1. Compute previous UTC hour prefix
    now = datetime.now(timezone.utc) - timedelta(hours=1)
    prefix = f"events/{now.strftime('%Y/%m/%d/%H')}/"
    
    print(f"Starting ETL for prefix: s3://{RAW_BUCKET}/{prefix}")
    
    # 2. List S3 objects
    response = s3_client.list_objects_v2(Bucket=RAW_BUCKET, Prefix=prefix)
    objects = response.get('Contents', [])
    if not objects:
        return {'status': 'NO_NEW_FILES', 'count': 0}
        
    # 3. Connect PostgreSQL DWH inside VPC
    conn = psycopg2.connect(
        host=DB_HOST, database=DB_NAME, user=DB_USER, password=DB_PASS, port=5432
    )
    cursor = conn.cursor()
    
    inserted_count = 0
    
    # 4. Parse and transform JSON event objects
    for obj in objects:
        try:
            file_content = s3_client.get_object(Bucket=RAW_BUCKET, Key=obj['Key'])['Body'].read().decode('utf-8')
            data = json.loads(file_content)
            
            ingest_meta = data.get('_ingest', {})
            event_ts = ingest_meta.get('receivedAt') or now.isoformat()
            product = data.get('product') or {}
            
            sql = """
                INSERT INTO public.clickstream_events (
                    event_id, event_timestamp, event_name, user_id, user_login_state,
                    identity_source, client_id, session_id, is_first_visit,
                    context_product_id, context_product_name, context_product_category,
                    context_product_brand, context_product_price, context_product_discount_price,
                    context_product_url_path
                ) VALUES (%s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s)
                ON CONFLICT (event_id) DO NOTHING;
            """
            
            cursor.execute(sql, (
                data.get('eventId'), event_ts, data.get('eventName'),
                data.get('userId'), data.get('userLoginState'), data.get('identitySource'),
                data.get('clientId'), data.get('sessionId'), data.get('isFirstVisit', False),
                product.get('id'), product.get('name'), product.get('category'),
                product.get('brand'), product.get('price'), product.get('discountPrice'),
                product.get('urlPath')
            ))
            inserted_count += 1
        except Exception as err:
            print(f"Error processing key {obj['Key']}: {err}")
            
    conn.commit()
    cursor.close()
    conn.close()
    
    return {'status': 'SUCCESS', 'inserted_count': inserted_count}
```

---

### 5. Step 4: Configure EventBridge Hourly Cron Rule (`SCAJ_ETL_HOURLY_RULE`)

![EventBridge Rule](/images/aws-eventbridge-sbw-etl-hourly-rule.png)

1. Open Amazon EventBridge Console -> Rule Name: `SCAJ_ETL_HOURLY_RULE`.
2. Schedule expression: `rate(1 hour)` (or `cron(0 * * * ? *)`).
3. Target: Lambda function `SCAJ_Lambda_ETL`.

---

### 6. Troubleshooting & Production Field Notes

> [!WARNING]
> **Issue 1: Lambda ETL Database Connection Timeouts**
> - **Symptom**: Lambda execution log displays `Task timed out after 3.00 seconds`.
> - **Fix**: Ensure Lambda function is attached to the VPC Private Subnet, and the Security Group `sg_analytics_ShinyDWH` allows Inbound port 5432 traffic originating from `sg_Lambda_ETL`.

> [!TIP]
> **Issue 2: Lambda Unable to Access S3 inside VPC**
> - When attaching Lambda to a VPC, public internet routes are disabled. Verify that the **S3 Gateway VPC Endpoint** is attached to the Private Subnet Route Table.
