---
title: "Triển khai Lớp Thu nhận Clickstream"
weight: 53
chapter: false
pre: " <b> 5.3. </b> "
---

### 1. Quy trình Thực hành Đầu cuối (Ingestion Steps)

Trong phần này, bạn sẽ xây dựng điểm thu nhận dữ liệu (Ingestion Layer) bằng mô hình Serverless hoàn toàn:

```
[ Browser SDK ] ──(HTTP POST)──▶ [ API Gateway: /clickstream ] ──▶ [ Lambda Ingest ] ──▶ [ S3 Raw Bucket ]
```

---

### 2. Bước 1: Khởi tạo S3 Raw Clickstream Bucket

1. Mở AWS S3 Console, tạo bucket mới:
   - Tên Bucket: `clickstream-s3-ingest` (Region: `ap-southeast-1`)
   - Bật **Block all public access** (Tuyệt đối không công khai S3).
   - Server-side encryption: `SSE-S3 (AES-256)`.
2. Quy chuẩn lưu trữ dữ liệu thô:
   - Path prefix: `events/YYYY/MM/DD/HH/`
   - Tên file: `event-<uuid>.json`

---

### 3. Bước 2: Triển khai Hàm Lambda Ingest (`clickstream-lambda-ingest`)

![Lambda Ingest Config](/images/aws-lambda-clickstream-ingest-config.png)

#### 2.1 Mã nguồn Hàm Lambda Ingest (Python 3.12 / Node.js 20)

Hàm Lambda này nhận JSON payload từ API Gateway, gắn thêm khối `_ingest` metadata và ghi file vào S3 Raw Bucket:

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
        # 1. Parse JSON body từ API Gateway request
        body = json.loads(event.get('body', '{}')) if event.get('body') else {}
        
        # 2. Enrich metadata phía Server (_ingest block)
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
        
        # 3. Tạo key lưu trữ phân vùng theo giờ UTC
        event_id = body.get('eventId') or str(uuid.uuid4())
        s3_key = f"events/{now.strftime('%Y/%m/%d/%H')}/event-{event_id}.json"
        
        # 4. Ghi file JSON thô vào S3 Bucket
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

#### 2.2 Chính sách IAM Policy cho Execution Role (`role-lambda-ingest`)

Gán policy sau cho IAM Role của hàm Lambda Ingest:

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

### 4. Bước 3: Đóng gói API Gateway HTTP API (`clickstream-http-api`)

![Route POST /clickstream](/images/aws-apigw-clickstream-routes.png)

1. Mở AWS API Gateway Console -> Tạo **HTTP API** với tên `clickstream-http-api`.
2. Tạo Route `POST /clickstream` -> Tích hợp với Lambda Integration `clickstream-lambda-ingest`.
3. Cấu hình **CORS Settings** (Bắt buộc cho Amplify Frontend):
   - **Access-Control-Allow-Origin**: `https://main.d12345amplify.amplifyapp.com` (hoặc `*` khi test)
   - **Access-Control-Allow-Methods**: `POST, OPTIONS`
   - **Access-Control-Allow-Headers**: `Content-Type, Authorization`

---

### 5. Bước 4: Tích hợp Client Tracking SDK (Next.js Frontend)

Trong dự án Next.js (`ClickSteam.NextJS`), tạo helper `lib/clickstreamClient.ts` để tự động gửi sự kiện:

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

### 6. Bảng Ánh xạ Trường Dữ liệu (Data Contract Projection Table)

| Trường Frontend Payload | S3 JSON Structure | Cột PostgreSQL DWH (`clickstream_dw`) | Ghi chú Xử lý ETL |
| --- | --- | --- | --- |
| `eventId` | `eventId` | `event_id` (PRIMARY KEY) | Map trực tiếp, deduplicate với `ON CONFLICT DO NOTHING` |
| `eventName` | `eventName` | `event_name` | Ví dụ: `product_view`, `add_to_cart`, `checkout` |
| `_ingest.receivedAt` | `_ingest.receivedAt` | `event_timestamp` | Cast sang `TIMESTAMP WITH TIME ZONE` |
| `userId` | `userId` | `user_id` | Định danh từ Cognito Pool |
| `userLoginState` | `userLoginState` | `user_login_state` | `LOGGED_IN` hoặc `GUEST` |
| `clientId` | `clientId` | `client_id` | Lưu tại `localStorage` |
| `sessionId` | `sessionId` | `session_id` | Lưu tại `sessionStorage` (30m timeout) |
| `product.id` | `product.id` | `context_product_id` | Mã sản phẩm laptop/phụ kiện |
| `product.name` | `product.name` | `context_product_name` | Tên sản phẩm |
| `product.category` | `product.category` | `context_product_category` | Danh mục (Laptop, Display, Accessories) |
| `product.brand` | `product.brand` | `context_product_brand` | Hãng sản xuất (Dell, ASUS, Apple,...) |
| `product.price` | `product.price` | `context_product_price` | Đơn giá sản phẩm |

---

### 7. Kinh nghiệm Thực chiến & Gỡ lỗi (Troubleshooting)

> [!WARNING]
> **Sự cố 1: Lỗi CORS khi gọi từ Amplify Frontend**
> - **Triệu chứng**: Console trình duyệt báo lỗi `CORS policy: No 'Access-Control-Allow-Origin' header is present`.
> - **Cách khắc phục**: Phải bật route `OPTIONS` preflight trong API Gateway và đảm bảo Lambda Ingestion trả về header `Access-Control-Allow-Origin: *` trong cả trường hợp 200 OK và 500 Error.

> [!TIP]
> **Sự cố 2: Kiểm tra dữ liệu thô trong S3**
> - Bạn có thể dùng AWS CLI trên máy local để kiểm tra số lượng event bắn về:
> ```bash
> aws s3 ls s3://clickstream-s3-ingest/events/2026/07/27/ --recursive
> ```
