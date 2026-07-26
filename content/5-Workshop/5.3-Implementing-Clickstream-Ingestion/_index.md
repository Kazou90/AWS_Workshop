---
title: "Implementing Clickstream Ingestion"
weight: 53
chapter: false
pre: " <b> 5.3. </b> "
---

### 1. Ingestion Pipeline Workflow

In this section, you will construct a serverless ingestion pipeline designed for high availability and low latency:

```
[ Browser SDK ] ──(HTTP POST)──▶ [ API Gateway: /clickstream ] ──▶ [ Lambda Ingest ] ──▶ [ S3 Raw Bucket ]
```

---

### 2. Step 1: Create the Raw Clickstream S3 Bucket

1. Open AWS S3 Console and create a new bucket:
   - Bucket Name: `clickstream-s3-ingest` (Region: `ap-southeast-1`)
   - Enable **Block all public access** (Ensure zero public exposure).
   - Server-side encryption: `SSE-S3 (AES-256)`.
2. Raw storage convention:
   - Path prefix: `events/YYYY/MM/DD/HH/`
   - File naming: `event-<uuid>.json`

---

### 3. Step 2: Deploy Lambda Ingest (`clickstream-lambda-ingest`)

![Lambda Ingest Config](/images/aws-lambda-clickstream-ingest-config.png)

#### 2.1 Lambda Ingest Source Code (Python 3.12 / Node.js 20)

This Lambda receives JSON requests from API Gateway, appends an `_ingest` server metadata block, and saves raw files into S3:

```python
import json
import os
import uuid
from datetime import datetime, timezone
import boto3

s3_client = boto3.client('s3')
RAW_BUCKET = os.environ.get('RAW_BUCKET', 'clickstream-s3-ingest')

def lambda_handler(event, context):
    try:
        # 1. Parse JSON body from API Gateway payload
        body = json.loads(event.get('body', '{}')) if event.get('body') else {}
        
        # 2. Enrich server-side metadata (_ingest block)
        now = datetime.now(timezone.utc)
        request_context = event.get('requestContext', {})
        http_info = request_context.get('http', {})
        
        body['_ingest'] = {
            'receivedAt': now.isoformat(),
            'sourceIp': http_info.get('sourceIp'),
            'userAgent': http_info.get('userAgent'),
            'requestId': request_context.get('requestId'),
            'apiId': request_context.get('apiId')
        }
        
        # 3. Generate UTC hour-partitioned S3 key
        event_id = body.get('eventId') or str(uuid.uuid4())
        s3_key = f"events/{now.strftime('%Y/%m/%d/%H')}/event-{event_id}.json"
        
        # 4. Put raw JSON object into S3 Bucket
        s3_client.put_object(
            Bucket=RAW_BUCKET,
            Key=s3_key,
            Body=json.dumps(body, ensure_ascii=False),
            ContentType='application/json'
        )
        
        return {
            'statusCode': 200,
            'headers': {
                'Content-Type': 'application/json',
                'Access-Control-Allow-Origin': '*'
            },
            'body': json.dumps({'status': 'SUCCESS', 'eventId': event_id})
        }
    except Exception as e:
        print(f"Ingest Error: {str(e)}")
        return {
            'statusCode': 500,
            'body': json.dumps({'status': 'ERROR', 'message': str(e)})
        }
```

#### 2.2 IAM Execution Policy (`role-lambda-ingest`)

Attach the following policy to the Lambda Ingest IAM role:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:PutObject"],
      "Resource": "arn:aws:s3:::clickstream-s3-ingest/events/*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource": "*"
    }
  ]
}
```

---

### 4. Step 3: Configure API Gateway HTTP API (`clickstream-http-api`)

![Route POST /clickstream](/images/aws-apigw-clickstream-routes.png)

1. Open API Gateway Console -> Create an **HTTP API** named `clickstream-http-api`.
2. Add Route `POST /clickstream` -> Attach Lambda Integration `clickstream-lambda-ingest`.
3. Enable **CORS Settings** (Required for Amplify Frontend cross-origin calls):
   - **Access-Control-Allow-Origin**: `https://main.d12345amplify.amplifyapp.com` (or `*` during initial testing)
   - **Access-Control-Allow-Methods**: `POST, OPTIONS`
   - **Access-Control-Allow-Headers**: `Content-Type, Authorization`

---

### 5. Step 4: Client Tracking SDK Wiring (Next.js Frontend)

Inside your Next.js project (`ClickSteam.NextJS`), implement `lib/clickstreamClient.ts`:

```typescript
// lib/clickstreamClient.ts
export interface ClickstreamEvent {
  eventId: string;
  eventName: string;
  userId?: string;
  userLoginState: 'LOGGED_IN' | 'GUEST';
  clientId: string;
  sessionId: string;
  isFirstVisit: boolean;
  product?: {
    id: string;
    name: string;
    category: string;
    brand: string;
    price: number;
    discountPrice?: number;
    urlPath: string;
  };
}

export async function sendClickstreamEvent(payload: ClickstreamEvent) {
  const endpoint = process.env.NEXT_PUBLIC_CLICKSTREAM_ENDPOINT;
  if (!endpoint) return;

  try {
    await fetch(endpoint, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(payload),
      keepalive: true
    });
  } catch (err) {
    console.warn('Clickstream send non-blocking error:', err);
  }
}
```

---

### 6. Data Contract Projection Table

| Frontend Payload Field | S3 JSON Structure | PostgreSQL DWH Column (`clickstream_dw`) | ETL Transformation Notes |
| --- | --- | --- | --- |
| `eventId` | `eventId` | `event_id` (PRIMARY KEY) | Direct map, deduplicated via `ON CONFLICT DO NOTHING` |
| `eventName` | `eventName` | `event_name` | e.g. `product_view`, `add_to_cart`, `checkout` |
| `_ingest.receivedAt` | `_ingest.receivedAt` | `event_timestamp` | Cast to `TIMESTAMP WITH TIME ZONE` |
| `userId` | `userId` | `user_id` | Cognito User Pool Subject |
| `userLoginState` | `userLoginState` | `user_login_state` | `LOGGED_IN` or `GUEST` |
| `clientId` | `clientId` | `client_id` | Persisted in `localStorage` |
| `sessionId` | `sessionId` | `session_id` | Persisted in `sessionStorage` (30m idle timeout) |
| `product.id` | `product.id` | `context_product_id` | Laptop / computer accessory SKU |
| `product.name` | `product.name` | `context_product_name` | Item display name |
| `product.category` | `product.category` | `context_product_category` | Category (Laptop, Display, Accessories) |
| `product.brand` | `product.brand` | `context_product_brand` | Brand (Dell, ASUS, Apple, etc.) |
| `product.price` | `product.price` | `context_product_price` | Product price |

---

### 7. Troubleshooting & Production Field Notes

> [!WARNING]
> **Issue 1: CORS Policy Failures**
> - **Symptom**: Browser console throws `CORS policy: No 'Access-Control-Allow-Origin' header is present`.
> - **Fix**: Enable `OPTIONS` preflight route in API Gateway and ensure Lambda Ingest explicitly sets `Access-Control-Allow-Origin: *` in all HTTP responses.

> [!TIP]
> **Issue 2: Verifying Raw Event Ingestion**
> - Validate file uploads using AWS CLI:
> ```bash
> aws s3 ls s3://clickstream-s3-ingest/events/2026/07/27/ --recursive
> ```
