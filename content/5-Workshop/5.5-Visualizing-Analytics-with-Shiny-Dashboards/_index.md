---
title: "Visualizing Analytics with Shiny Dashboards"
weight: 55
chapter: false
pre: " <b> 5.5. </b> "
---

### 1. Dashboard Deployment Architecture (Private Access Pattern)

The **R Shiny Dashboard** runs on the EC2 Private instance `SCAJ_EC2_ShinyDWH`, querying data directly from the local PostgreSQL Data Warehouse. Access is secured strictly through **AWS SSM Session Manager Port Forwarding**:

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

### 2. Step 1: Install Ubuntu System Dependencies

Connect to the EC2 Private instance via **SSM Session Manager** and install the required R runtime and PostgreSQL development headers:

```bash
# 1. Update Ubuntu 22.04 LTS package lists
sudo apt-get update -y

# 2. Install R Base and PostgreSQL development headers (Required to compile RPostgres)
sudo apt-get install -y r-base postgresql-client-18 postgresql-server-dev-18 libpq-dev libssl-dev

# 3. Verify PostgreSQL header existence
ls -l /usr/include/postgresql/libpq-fe.h
```

---

### 3. Step 2: Install R Packages for Service User `shiny`

Configure the R library paths and install the required dashboard packages under the `shiny` user:

```bash
sudo -u shiny R --vanilla <<'EOF'
# Provision R user library directory
dir.create(Sys.getenv("R_LIBS_USER"), recursive = TRUE, showWarnings = FALSE)
.libPaths(c(Sys.getenv("R_LIBS_USER"), .libPaths()))

# Install R Packages for Dashboard & Database Connection Pooling
install.packages(
  c("shiny", "DBI", "RPostgres", "dplyr", "ggplot2", "lubridate", "pool"),
  repos = "https://cloud.r-project.org"
)

cat("R Packages installed successfully!\n")
q("no")
EOF
```

---

### 4. Step 3: Deploy R Shiny Source Code (`app.R`)

Provision the application directory and set permissions for `shiny`:

```bash
sudo mkdir -p /srv/shiny-server/sbw_dashboard
sudo chown -R shiny:shiny /srv/shiny-server/sbw_dashboard
```

Create `/srv/shiny-server/sbw_dashboard/app.R` with the dashboard source:

```r
library(shiny)
library(DBI)
library(RPostgres)
library(dplyr)
library(ggplot2)
library(pool)

# 1. Provision Database Connection Pool to PostgreSQL DWH
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

# 2. UI Layout Definition
ui <- fluidPage(
  titlePanel("f5-SCAJ Clickstream Analytics Dashboard"),
  
  fluidRow(
    column(4, wellPanel(h4("Total Events"), textOutput("total_events"))),
    column(4, wellPanel(h4("Total Users"), textOutput("total_users"))),
    column(4, wellPanel(h4("Total Sessions"), textOutput("total_sessions")))
  ),
  
  hr(),
  
  fluidRow(
    column(6, h4("Event Mix Visualization"), plotOutput("event_mix_plot")),
    column(6, h4("Top Computer Products Viewed"), plotOutput("top_products_plot"))
  ),
  
  hr(),
  
  fluidRow(
    column(12, h4("Latest Raw Clickstream Data"), tableOutput("raw_table"))
  )
)

# 3. Server Processing Logic
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
      labs(x = "Event Name", y = "Count")
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
      labs(x = "Product", y = "Views")
  })
  
  output$raw_table <- renderTable({
    events_data() %>% select(event_id, event_timestamp, event_name, user_login_state, context_product_name) %>% head(10)
  })
}

shinyApp(ui = ui, server = server)
```

Restart Shiny Server service:

```bash
sudo chown -R shiny:shiny /srv/shiny-server/sbw_dashboard
sudo systemctl restart shiny-server
```

---

### 5. Step 4: Access Dashboard via SSM Port Forwarding

From your local terminal (with AWS CLI & Session Manager Plugin installed), launch the port forwarding tunnel:

```bash
aws ssm start-session \
  --target <INSTANCE_ID_PRIVATE_EC2> \
  --document-name AWS-StartPortForwardingSessionToRemoteHost \
  --parameters '{"host":["127.0.0.1"],"portNumber":["3838"],"localPortNumber":["3838"]}'
```

Open your local browser and navigate to:
👉 **`http://127.0.0.1:3838/sbw_dashboard/`**

---

### 6. Troubleshooting & Production Field Notes

> [!WARNING]
> **Issue 1: Compilation Error `RPostgres compilation failed`**
> - **Cause**: Missing system library headers `libpq-dev` or `postgresql-server-dev-18`.
> - **Fix**: Execute `sudo apt-get install -y libpq-dev postgresql-server-dev-18` prior to installing `RPostgres`.

> [!TIP]
> **Issue 2: Shiny Server HTTP 500 Errors**
> - Inspect application crash logs:
> ```bash
> sudo tail -n 100 /var/log/shiny-server/sbw_dashboard-shiny-*.log
> ```
