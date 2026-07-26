---
title: "Xây dựng Lớp Phân tích dữ liệu Bảo mật (Private Analytics)"
weight: 54
chapter: false
pre: " <b> 5.4. </b> "
---

### 1. Quy trình Khởi tạo Hạ tầng Mạng Private (VPC Setup)

Trong bài thực hành này, bạn sẽ thiết lập lớp hạ tầng mạng cô lập an toàn tuyệt đối cho Data Warehouse và quy trình Batch ETL:

```
[ EventBridge (Rate 1 Hour) ] ──▶ [ Lambda ETL (In Private VPC) ]
                                            │
                             ┌──────────────┴──────────────┐
                             │ (S3 Gateway VPC Endpoint)   │ (Postgres Port 5432)
                             ▼                             ▼
                  [ S3 Raw Data Lake ]           [ EC2 DWH: clickstream_dw ]
```

---

### 2. Bước 1: Khởi tạo VPC, Private Subnet & VPC Endpoints

![VPC Endpoints S3 & SSM](/images/aws-vpc-endpoints-s3-ssm.png)

1. **Tạo VPC & Subnets**:
   - VPC CIDR: `10.0.0.0/16` (`SCAJ_Project_VPC`)
   - **Public Subnet**: `10.0.0.0/20` (Route table: `0.0.0.0/0` -> `igw-12345`)
   - **Private Subnet**: `10.0.128.0/20` (Không gán Internet Gateway hay NAT Gateway)

2. **Khởi tạo Gateway VPC Endpoint cho S3** (Tiết kiệm 100% chi phí NAT):
   - Service: `com.amazonaws.ap-southeast-1.s3` (Type: `Gateway`)
   - Route Table: Đính kèm vào **Private Route Table** của `10.0.128.0/20`.
   - Mục đích: Cho phép `SCAJ_Lambda_ETL` kết nối đọc file từ `clickstream-s3-ingest` mà không cần NAT Gateway.

3. **Khởi tạo Interface VPC Endpoints cho SSM**:
   - Tạo 3 Interface Endpoints tại Private Subnet:
     - `com.amazonaws.ap-southeast-1.ssm`
     - `com.amazonaws.ap-southeast-1.ssmmessages`
     - `com.amazonaws.ap-southeast-1.ec2messages`
   - Gán Security Group `sg_ssm_endpoints` cho phép Inbound Port 443 từ Private Subnet.

---

### 3. Bước 2: Khởi tạo Data Warehouse PostgreSQL (`clickstream_dw`)

Trên máy chủ EC2 Private `SCAJ_EC2_ShinyDWH`, tiến hành khởi chạy PostgreSQL 18 và tạo DDL Schema cho Data Warehouse:

```sql
-- Tạo Database & Kết nối
CREATE DATABASE clickstream_dw;
\c clickstream_dw;

-- DDL Bảng Phân tích Clickstream chính
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

-- Đánh Index tăng tốc truy vấn Dashboard R Shiny
CREATE INDEX idx_events_timestamp ON public.clickstream_events (event_timestamp DESC);
CREATE INDEX idx_events_name ON public.clickstream_events (event_name);
CREATE INDEX idx_events_product_id ON public.clickstream_events (context_product_id);
```

---

### 4. Bước 3: Triển khai Hàm Lambda ETL (`SCAJ_Lambda_ETL`)

#### 4.1 Mã nguồn Hàm Lambda ETL (Python 3.12 với `psycopg2` / `pg8000`)

Hàm ETL quét toàn bộ file JSON trong S3 của giờ trước, parse và upsert vào PostgreSQL DWH:

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
    # 1. Tính toán prefix giờ vừa qua (UTC)
    now = datetime.now(timezone.utc) - timedelta(hours=1)
    prefix = f"events/{now.strftime('%Y/%m/%d/%H')}/"
    
    print(f"Starting ETL for prefix: s3://{RAW_BUCKET}/{prefix}")
    
    # 2. Liệt kê danh sách file S3
    response = s3_client.list_objects_v2(Bucket=RAW_BUCKET, Prefix=prefix)
    objects = response.get('Contents', [])
    if not objects:
        return {'status': 'NO_NEW_FILES', 'count': 0}
        
    # 3. Connect DB Postgres trong VPC
    conn = psycopg2.connect(
        host=DB_HOST, database=DB_NAME, user=DB_USER, password=DB_PASS, port=5432
    )
    cursor = conn.cursor()
    
    inserted_count = 0
    
    # 4. Loop đọc và transform từng file JSON
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

### 5. Bước 4: Khởi tạo Lịch EventBridge Cron (`SCAJ_ETL_HOURLY_RULE`)

![EventBridge Rule](/images/aws-eventbridge-sbw-etl-hourly-rule.png)

1. Mở Amazon EventBridge Console -> Rule Name: `SCAJ_ETL_HOURLY_RULE`.
2. Schedule expression: `rate(1 hour)` (hoặc `cron(0 * * * ? *)`).
3. Target: Hàm Lambda `SCAJ_Lambda_ETL`.

---

### 6. Kinh nghiệm Thực chiến & Gỡ lỗi (Troubleshooting)

> [!WARNING]
> **Sự cố 1: Lambda ETL bị Timeout khi kết nối PostgreSQL DWH**
> - **Triệu chứng**: Lambda log báo `Task timed out after 3.00 seconds` hoặc connection timeout.
> - **Nguyên nhân**: Lambda chưa được gán đúng VPC Private Subnet, hoặc Security Group của EC2 DWH chưa cho phép Inbound 5432 từ Security Group của Lambda.
> - **Cách khắc phục**: Gán Security Group `sg_analytics_ShinyDWH` Inbound port 5432 nguồn từ `sg_Lambda_ETL`.

> [!TIP]
> **Sự cố 2: Lambda không đọc được S3 khi đặt vào VPC**
> - Khi gắn Lambda vào VPC, mặc định nó mất khả năng ra Internet. Phải đảm bảo **S3 Gateway VPC Endpoint** đã được gắn vào Route Table của Private Subnet mà Lambda đang đứng!
