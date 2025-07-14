# CLAUDE-integrations.md

**External System Integration** (tích hợp hệ thống ngoài) - Documentation cho **API connections** (kết nối API), **third-party tools** (công cụ bên thứ ba), và **service dependencies** (phụ thuộc dịch vụ).

## 🔌 **MCP Server Integrations** (Tích hợp MCP Server)

### **1. Gemini CLI MCP** - Google AI Integration

#### **Configuration** (Cấu hình)
```bash
# Environment Setup
export GEMINI_API_KEY="your_gemini_api_key"
export OPENROUTER_API_KEY="your_openrouter_key"

# Server Registration
claude mcp add gemini-cli \
  /path/to/.venv/bin/python \
  /path/to/mcp_server.py \
  -s user \
  -e GEMINI_API_KEY="${GEMINI_API_KEY}" \
  -e OPENROUTER_API_KEY="${OPENROUTER_API_KEY}"
```

#### **Capabilities** (Khả năng)
- **AI Model Access** (truy cập mô hình AI) - Gemini Pro, Gemini Flash
- **Multi-Modal Support** (hỗ trợ đa phương thức) - Text, image, audio processing
- **Streaming Responses** (phản hồi streaming) - Real-time output
- **Function Calling** (gọi hàm) - Tool integration capabilities

#### **Use Cases** (Trường hợp sử dụng)
- **Alternative AI Provider** (nhà cung cấp AI thay thế) - Backup khi Anthropic unavailable
- **Specialized Tasks** (tác vụ chuyên biệt) - Google-specific integrations
- **Cost Optimization** (tối ưu chi phí) - Different pricing models
- **Model Comparison** (so sánh mô hình) - A/B testing different AI approaches

---

### **2. Cloudflare Documentation MCP** - Knowledge Base Access

#### **Configuration** (Cấu hình)
```bash
# SSE Transport Setup
claude mcp add --transport sse cf-docs \
  https://docs.mcp.cloudflare.com/sse \
  -s user
```

#### **Capabilities** (Khả năng)
- **Vectorized Documentation Search** (tìm kiếm tài liệu vector hóa) - Semantic search
- **Real-time Updates** (cập nhật thời gian thực) - Always current documentation
- **Context-Aware Retrieval** (truy xuất nhận thức ngữ cảnh) - Relevant information
- **Multi-Language Support** (hỗ trợ đa ngôn ngữ) - Documentation in various languages

#### **Integration Benefits** (Lợi ích tích hợp)
- **Enhanced Knowledge Base** (cơ sở kiến thức tăng cường) - Access to Cloudflare expertise
- **Reduced Context Switching** (giảm chuyển đổi ngữ cảnh) - Information within workflow
- **Updated Technical Information** (thông tin kỹ thuật cập nhật) - Latest best practices
- **Implementation Examples** (ví dụ triển khai) - Working code samples

---

### **3. Context 7 MCP** - Context Management

#### **Configuration** (Cấu hình)
```bash
# Context Management Setup
claude mcp add context7 \
  /path/to/context7-server \
  -s project \
  --config context7-config.json
```

#### **Context Management Features** (Tính năng quản lý ngữ cảnh)
- **Session Persistence** (bảo tồn phiên) - Context across conversations
- **Smart Summarization** (tóm tắt thông minh) - Key information extraction
- **Cross-Reference Linking** (liên kết tham chiếu chéo) - Related content discovery
- **Priority-Based Retrieval** (truy xuất theo độ ưu tiên) - Most relevant first

#### **Advanced Capabilities** (Khả năng nâng cao)
- **Context Compression** (nén ngữ cảnh) - Efficient token usage
- **Semantic Indexing** (lập chỉ mục ngữ nghĩa) - Content organization
- **Temporal Awareness** (nhận thức thời gian) - Time-based relevance
- **Multi-Project Support** (hỗ trợ đa dự án) - Isolated contexts

---

### **4. Notion MCP** - Workspace Integration

#### **Configuration** (Cấu hình)
```bash
# Notion API Setup
export NOTION_API_KEY="ntn_your_notion_integration_token"

# Server Registration
claude mcp add-json notionApi '{
  "type": "stdio",
  "command": "npx",
  "args": ["-y", "@notionhq/notion-mcp-server"],
  "env": {
    "OPENAPI_MCP_HEADERS": "{\"Authorization\": \"Bearer ${NOTION_API_KEY}\", \"Notion-Version\": \"2022-06-28\"}"
  }
}' -s user
```

#### **Workspace Capabilities** (Khả năng không gian làm việc)
- **Database Integration** (tích hợp cơ sở dữ liệu) - Read/write Notion databases
- **Page Management** (quản lý trang) - Create, update, archive pages
- **Block Manipulation** (thao tác khối) - Rich content editing
- **Template System** (hệ thống template) - Standardized page creation

#### **Business Value** (Giá trị kinh doanh)
- **Knowledge Management** (quản lý kiến thức) - Centralized documentation
- **Project Tracking** (theo dõi dự án) - Task và milestone management
- **Team Collaboration** (hợp tác nhóm) - Shared workspace integration
- **Reporting Automation** (tự động báo cáo) - Automated status updates

---

## 🔗 **API Integrations** (Tích hợp API)

### **Authentication Patterns** (Mẫu xác thực)

#### **API Key Authentication** (Xác thực API Key)
```bash
# Environment Variable Method
export SERVICE_API_KEY="your_api_key"

# Header-based Authentication
curl -H "Authorization: Bearer ${SERVICE_API_KEY}" \
     -H "Content-Type: application/json" \
     https://api.service.com/endpoint
```

#### **OAuth 2.0 Flow** (Luồng OAuth 2.0)
```bash
# OAuth Configuration
export OAUTH_CLIENT_ID="your_client_id"
export OAUTH_CLIENT_SECRET="your_client_secret"
export OAUTH_REDIRECT_URI="http://localhost:8080/callback"

# Token Refresh Automation
oauth_refresh_token() {
  curl -X POST https://oauth.service.com/token \
    -d "grant_type=refresh_token" \
    -d "refresh_token=${REFRESH_TOKEN}" \
    -d "client_id=${OAUTH_CLIENT_ID}" \
    -d "client_secret=${OAUTH_CLIENT_SECRET}"
}
```

#### **Service Account Authentication** (Xác thực Service Account)
```json
{
  "type": "service_account",
  "project_id": "your-project",
  "private_key_id": "key-id",
  "private_key": "-----BEGIN PRIVATE KEY-----\n...\n-----END PRIVATE KEY-----\n",
  "client_email": "service@your-project.iam.gserviceaccount.com",
  "auth_uri": "https://accounts.google.com/o/oauth2/auth",
  "token_uri": "https://oauth2.googleapis.com/token"
}
```

---

### **External Service Integrations** (Tích hợp dịch vụ ngoài)

#### **GitHub Integration** (Tích hợp GitHub)
```bash
# GitHub CLI Setup
gh auth login --with-token < github_token.txt

# Repository Operations
gh repo create my-new-repo --private
gh issue create --title "Bug Report" --body "Description"
gh pr create --title "Feature" --body "Implementation details"

# Webhook Configuration
gh api repos/:owner/:repo/hooks \
  --method POST \
  --field name=web \
  --field config[url]=https://your-webhook-endpoint.com \
  --field config[content_type]=json \
  --field events='["push","pull_request"]'
```

#### **Slack Integration** (Tích hợp Slack)
```bash
# Slack App Configuration
export SLACK_BOT_TOKEN="xoxb-your-bot-token"
export SLACK_SIGNING_SECRET="your-signing-secret"

# Message Posting
slack_notify() {
  curl -X POST https://slack.com/api/chat.postMessage \
    -H "Authorization: Bearer ${SLACK_BOT_TOKEN}" \
    -H "Content-Type: application/json" \
    -d "{
      \"channel\": \"#general\",
      \"text\": \"$1\",
      \"username\": \"Claude Code Bot\"
    }"
}

# Interactive Components
slack_interactive_message() {
  curl -X POST https://slack.com/api/chat.postMessage \
    -H "Authorization: Bearer ${SLACK_BOT_TOKEN}" \
    -H "Content-Type: application/json" \
    -d '{
      "channel": "#dev-alerts",
      "blocks": [
        {
          "type": "section",
          "text": {"type": "mrkdwn", "text": "Deployment completed!"},
          "accessory": {
            "type": "button",
            "text": {"type": "plain_text", "text": "View Logs"},
            "action_id": "view_logs"
          }
        }
      ]
    }'
}
```

#### **Jira Integration** (Tích hợp Jira)
```bash
# Jira API Configuration
export JIRA_URL="https://your-domain.atlassian.net"
export JIRA_USERNAME="your-email@company.com"
export JIRA_API_TOKEN="your-api-token"

# Issue Creation
create_jira_issue() {
  curl -X POST "${JIRA_URL}/rest/api/3/issue" \
    -u "${JIRA_USERNAME}:${JIRA_API_TOKEN}" \
    -H "Content-Type: application/json" \
    -d '{
      "fields": {
        "project": {"key": "PROJ"},
        "summary": "'"$1"'",
        "description": "'"$2"'",
        "issuetype": {"name": "Task"}
      }
    }'
}

# Issue Transition Automation
transition_issue() {
  local issue_key=$1
  local transition_id=$2
  
  curl -X POST "${JIRA_URL}/rest/api/3/issue/${issue_key}/transitions" \
    -u "${JIRA_USERNAME}:${JIRA_API_TOKEN}" \
    -H "Content-Type: application/json" \
    -d "{\"transition\":{\"id\":\"${transition_id}\"}}"
}
```

---

## 📊 **Database Integrations** (Tích hợp cơ sở dữ liệu)

### **PostgreSQL Integration** (Tích hợp PostgreSQL)
```bash
# Connection Configuration
export PGHOST="localhost"
export PGPORT="5432"
export PGDATABASE="your_database"
export PGUSER="your_username"
export PGPASSWORD="your_password"

# Connection Pool Setup
export PGPOOL_MAX_CONNECTIONS="20"
export PGPOOL_IDLE_TIMEOUT="30000"
export PGPOOL_CONNECTION_TIMEOUT="10000"

# SSL Configuration
export PGSSLMODE="require"
export PGSSLCERT="/path/to/client-cert.pem"
export PGSSLKEY="/path/to/client-key.pem"
export PGSSLROOTCERT="/path/to/ca-cert.pem"
```

### **Redis Integration** (Tích hợp Redis)
```bash
# Redis Configuration
export REDIS_URL="redis://localhost:6379"
export REDIS_PASSWORD="your_redis_password"
export REDIS_DB="0"

# Clustering Setup
export REDIS_CLUSTER_NODES="redis1:6379,redis2:6379,redis3:6379"
export REDIS_CLUSTER_REQUIRE_FULL_COVERAGE="false"

# Connection Pool
export REDIS_POOL_SIZE="10"
export REDIS_POOL_TIMEOUT="5000"
```

### **MongoDB Integration** (Tích hợp MongoDB)
```bash
# MongoDB Connection
export MONGODB_URI="mongodb://username:password@localhost:27017/database"
export MONGODB_MAX_POOL_SIZE="100"
export MONGODB_MIN_POOL_SIZE="5"
export MONGODB_MAX_IDLE_TIME="30000"

# Replica Set Configuration
export MONGODB_REPLICA_SET="rs0"
export MONGODB_READ_PREFERENCE="secondaryPreferred"
export MONGODB_WRITE_CONCERN="majority"
```

---

## 🌐 **Cloud Platform Integrations** (Tích hợp nền tảng đám mây)

### **AWS Integration** (Tích hợp AWS)
```bash
# AWS CLI Configuration
export AWS_ACCESS_KEY_ID="your_access_key"
export AWS_SECRET_ACCESS_KEY="your_secret_key"
export AWS_DEFAULT_REGION="us-west-2"
export AWS_PROFILE="your_profile"

# S3 Integration
aws_s3_upload() {
  aws s3 cp "$1" "s3://your-bucket/$2" \
    --storage-class STANDARD_IA \
    --server-side-encryption AES256
}

# Lambda Function Deployment
aws_lambda_deploy() {
  aws lambda update-function-code \
    --function-name your-function \
    --zip-file fileb://deployment.zip \
    --publish
}

# CloudFormation Deployment
aws_cfn_deploy() {
  aws cloudformation deploy \
    --template-file template.yaml \
    --stack-name your-stack \
    --parameter-overrides Environment=production \
    --capabilities CAPABILITY_IAM
}
```

### **Google Cloud Integration** (Tích hợp Google Cloud)
```bash
# GCP Authentication
export GOOGLE_APPLICATION_CREDENTIALS="/path/to/service-account.json"
export GOOGLE_CLOUD_PROJECT="your-project-id"

# Cloud Storage Operations
gsutil_upload() {
  gsutil -m cp -r "$1" "gs://your-bucket/$2"
}

# Cloud Functions Deployment
gcp_function_deploy() {
  gcloud functions deploy your-function \
    --runtime python39 \
    --trigger-http \
    --allow-unauthenticated \
    --set-env-vars "ENV=production"
}

# BigQuery Integration
bq_query() {
  bq query --use_legacy_sql=false \
    --max_rows=1000 \
    "$1"
}
```

### **Azure Integration** (Tích hợp Azure)
```bash
# Azure CLI Authentication
az login --service-principal \
  --username "$AZURE_CLIENT_ID" \
  --password "$AZURE_CLIENT_SECRET" \
  --tenant "$AZURE_TENANT_ID"

# Storage Account Operations
az_storage_upload() {
  az storage blob upload \
    --account-name your-storage-account \
    --container-name your-container \
    --file "$1" \
    --name "$2"
}

# Function App Deployment
az_function_deploy() {
  az functionapp deployment source config-zip \
    --resource-group your-resource-group \
    --name your-function-app \
    --src deployment.zip
}
```

---

## 🔧 **Monitoring và Observability** (Giám sát và quan sát)

### **Application Performance Monitoring** (Giám sát hiệu suất ứng dụng)

#### **Datadog Integration** (Tích hợp Datadog)
```bash
# Datadog Agent Configuration
export DD_API_KEY="your_datadog_api_key"
export DD_APP_KEY="your_datadog_app_key"
export DD_SITE="datadoghq.com"

# Custom Metrics
datadog_metric() {
  curl -X POST "https://api.${DD_SITE}/api/v1/series" \
    -H "Content-Type: application/json" \
    -H "DD-API-KEY: ${DD_API_KEY}" \
    -d '{
      "series": [{
        "metric": "claude.code.performance",
        "points": [['$(date +%s)', '$1']],
        "tags": ["environment:production"]
      }]
    }'
}
```

#### **New Relic Integration** (Tích hợp New Relic)
```bash
# New Relic Configuration
export NEW_RELIC_LICENSE_KEY="your_license_key"
export NEW_RELIC_APP_NAME="Claude Code Application"
export NEW_RELIC_LOG_LEVEL="info"

# Custom Events
newrelic_event() {
  curl -X POST https://insights-collector.newrelic.com/v1/accounts/YOUR_ACCOUNT_ID/events \
    -H "Content-Type: application/json" \
    -H "X-Insert-Key: YOUR_INSERT_KEY" \
    -d '{
      "eventType": "ClaudeCodeEvent",
      "action": "'"$1"'",
      "duration": '$2',
      "timestamp": '$(date +%s)'
    }'
}
```

### **Log Management** (Quản lý log)

#### **ELK Stack Integration** (Tích hợp ELK Stack)
```bash
# Elasticsearch Configuration
export ELASTICSEARCH_URL="http://localhost:9200"
export ELASTICSEARCH_USERNAME="elastic"
export ELASTICSEARCH_PASSWORD="your_password"

# Logstash Pipeline
logstash_send() {
  echo "$1" | nc localhost 5000
}

# Kibana Dashboard
export KIBANA_URL="http://localhost:5601"
```

#### **Splunk Integration** (Tích hợp Splunk)
```bash
# Splunk HTTP Event Collector
export SPLUNK_HEC_URL="https://your-splunk.com:8088/services/collector"
export SPLUNK_HEC_TOKEN="your_hec_token"

splunk_log() {
  curl -X POST "${SPLUNK_HEC_URL}" \
    -H "Authorization: Splunk ${SPLUNK_HEC_TOKEN}" \
    -H "Content-Type: application/json" \
    -d '{
      "event": "'"$1"'",
      "sourcetype": "claude_code",
      "index": "main"
    }'
}
```

---

## 🔒 **Security Integrations** (Tích hợp bảo mật)

### **Vulnerability Scanning** (Quét lỗ hổng)
```bash
# Snyk Integration
export SNYK_TOKEN="your_snyk_token"
snyk test --severity-threshold=high

# OWASP Dependency Check
dependency-check.sh \
  --project "Claude Code" \
  --scan . \
  --format ALL \
  --out ./reports
```

### **Secret Management** (Quản lý bí mật)
```bash
# HashiCorp Vault
export VAULT_ADDR="https://vault.company.com"
export VAULT_TOKEN="your_vault_token"

vault_get_secret() {
  vault kv get -field="$2" secret/"$1"
}

# AWS Secrets Manager
aws_get_secret() {
  aws secretsmanager get-secret-value \
    --secret-id "$1" \
    --query SecretString \
    --output text
}
```

---

*Integration documentation được updated khi **new services** (dịch vụ mới) được added hoặc **existing integrations** (tích hợp hiện tại) thay đổi configuration.*