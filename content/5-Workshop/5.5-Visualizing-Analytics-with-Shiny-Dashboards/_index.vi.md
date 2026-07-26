---
title: "Trực quan hóa Phân tích dữ liệu với R Shiny Dashboard"
weight: 55
chapter: false
pre: " <b> 5.5. </b> "
---

### 1. Kiến trúc Hiển thị Dashboard (Private Access Pattern)

Ứng dụng **R Shiny Dashboard** được chạy trực tiếp trên EC2 Private `SCAJ_EC2_ShinyDWH`, truy vấn dữ liệu từ CSDL PostgreSQL Data Warehouse nội bộ và chỉ có thể truy cập qua **AWS SSM Session Manager Port Forwarding**:

```
[ Local Browser: localhost:3838 ] 
               │
      (SSM Port Forwarding)
               ▼
[ SSM Interface Endpoints ] ──▶ [ EC2 Private: R Shiny Server (Port 3838) ]
                                                │
                                    (PostgreSQL DWH: Port 5432)
                                                ▼
                                    [ DB: clickstream_dw ]
```

---

### 2. Bước 1: Cài đặt Packages Hệ thống (System Dependencies)

Truy cập EC2 Private qua **SSM Session Manager** (hoặc VPC Console), thực thi các lệnh cài đặt môi trường R và Postgres headers:

```bash
# 1. Cập nhật hệ thống Ubuntu 22.04 LTS
sudo apt-get update -y

# 2. Cài đặt R Base và CSDL client headers (Bắt buộc để biên dịch RPostgres)
sudo apt-get install -y r-base postgresql-client-18 postgresql-server-dev-18 libpq-dev libssl-dev

# 3. Kiểm tra sự tồn tại của thư viện C/C++ Postgres headers
ls -l /usr/include/postgresql/libpq-fe.h
```

---

### 3. Bước 2: Cài đặt R Packages dưới quyền user `shiny`

Thiết lập thư mục thư viện R và cài đặt các packages cần thiết cho Dashboard:

```bash
sudo -u shiny R --vanilla <<'EOF'
# Tạo thư mục thư viện người dùng shiny
dir.create(Sys.getenv("R_LIBS_USER"), recursive = TRUE, showWarnings = FALSE)
.libPaths(c(Sys.getenv("R_LIBS_USER"), .libPaths()))

# Cài đặt các R Packages phục vụ Dashboard & Database Connection Pool
install.packages(
  c("shiny", "DBI", "RPostgres", "dplyr", "ggplot2", "lubridate", "pool"),
  repos = "https://cloud.r-project.org"
)

cat("Cài đặt R Packages thành công!\n")
q("no")
EOF
```

---

### 4. Bước 3: Triển khai Mã nguồn App R Shiny (`app.R`)

Khởi tạo thư mục ứng dụng và phân quyền cho user `shiny`:

```bash
sudo mkdir -p /srv/shiny-server/sbw_dashboard
sudo chown -R shiny:shiny /srv/shiny-server/sbw_dashboard
```

Tạo file `/srv/shiny-server/sbw_dashboard/app.R` với nội dung Dashboard hoàn chỉnh:

```r
library(shiny)
library(DBI)
library(RPostgres)
library(dplyr)
library(ggplot2)
library(pool)

# 1. Khởi tạo Connection Pool tới PostgreSQL DWH
pool <- dbPool(
  drv = RPostgres::Postgres(),
  dbname = "clickstream_dw",
  host = "127.0.0.1",
  port = 5432,
  user = "postgres",
  password = "YourSecurePassword123!"
)

onStop(function() {
  poolClose(pool)
})

# 2. Định nghĩa Giao diện Người dùng UI
ui <- fluidPage(
  titlePanel("f5-SCAJ Clickstream Analytics Dashboard"),
  
  fluidRow(
    column(4, wellPanel(h4("Tổng Events"), textOutput("total_events"))),
    column(4, wellPanel(h4("Tổng Users"), textOutput("total_users"))),
    column(4, wellPanel(h4("Tổng Sessions"), textOutput("total_sessions")))
  ),
  
  hr(),
  
  fluidRow(
    column(6, h4("Biểu đồ Phân bổ Sự kiện (Event Mix)"), plotOutput("event_mix_plot")),
    column(6, h4("Top Sản phẩm máy tính được xem nhiều nhất"), plotOutput("top_products_plot"))
  ),
  
  hr(),
  
  fluidRow(
    column(12, h4("Dữ liệu Clickstream Mới nhất"), tableOutput("raw_table"))
  )
)

# 3. Định nghĩa Logic Server Processing
server <- function(input, output, session) {
  
  # Reactive Data Queries
  events_data <- reactivePoll(10000, session,
    checkFunc = function() {
      dbGetQuery(pool, "SELECT COUNT(*) FROM public.clickstream_events")
    },
    valueFunc = function() {
      dbGetQuery(pool, "SELECT * FROM public.clickstream_events ORDER BY event_timestamp DESC LIMIT 100")
    }
  )
  
  output$total_events <- renderText({ nrow(events_data()) })
  output$total_users <- renderText({ length(unique(events_data()$user_id)) })
  output$total_sessions <- renderText({ length(unique(events_data()$session_id)) })
  
  output$event_mix_plot <- renderPlot({
    df <- events_data()
    if(nrow(df) == 0) return(NULL)
    ggplot(df, aes(x = event_name, fill = event_name)) +
      geom_bar() +
      theme_minimal() +
      labs(x = "Tên Sự kiện", y = "Số lượng")
  })
  
  output$top_products_plot <- renderPlot({
    df <- events_data() %>% 
      filter(!is.na(context_product_name)) %>%
      group_by(context_product_name) %>%
      summarise(views = n()) %>%
      arrange(desc(views)) %>%
      head(5)
      
    if(nrow(df) == 0) return(NULL)
    ggplot(df, aes(x = reorder(context_product_name, views), y = views, fill = context_product_name)) +
      geom_col() +
      coord_flip() +
      theme_minimal() +
      labs(x = "Sản phẩm", y = "Lượt xem")
  })
  
  output$raw_table <- renderTable({
    events_data() %>% select(event_id, event_timestamp, event_name, user_login_state, context_product_name) %>% head(10)
  })
}

shinyApp(ui = ui, server = server)
```

Khởi động lại dịch vụ Shiny Server:

```bash
sudo chown -R shiny:shiny /srv/shiny-server/sbw_dashboard
sudo systemctl restart shiny-server
```

---

### 5. Bước 4: Kết nối Dashboard qua SSM Port Forwarding

Trên máy tính local của bạn (đã cài AWS CLI & SSM Plugin), mở terminal và chạy lệnh mở tunnel:

```bash
aws ssm start-session \
  --target <INSTANCE_ID_PRIVATE_EC2> \
  --document-name AWS-StartPortForwardingSessionToRemoteHost \
  --parameters '{"host":["127.0.0.1"],"portNumber":["3838"],"localPortNumber":["3838"]}'
```

Mở trình duyệt local và truy cập đường dẫn:
👉 **`http://127.0.0.1:3838/sbw_dashboard/`**

---

### 6. Kinh nghiệm Thực chiến & Gỡ lỗi (Troubleshooting)

> [!WARNING]
> **Sự cố 1: Lỗi `compilation failed for package 'RPostgres'`**
> - **Nguyên nhân**: Thiếu gói thư viện hệ thống `libpq-dev` hoặc `postgresql-server-dev-18`.
> - **Cách khắc phục**: Chạy `sudo apt-get install -y libpq-dev postgresql-server-dev-18` trước khi gọi `install.packages("RPostgres")`.

> [!TIP]
> **Sự cố 2: Shiny App trả về Lỗi HTTP 500**
> - Mở xem log lỗi chi tiết của Shiny Server tại thư mục:
> ```bash
> sudo tail -n 100 /var/log/shiny-server/sbw_dashboard-shiny-*.log
> ```
