# CLAUDE-security.md

**Security Policies** (chính sách bảo mật) - **Access control rules** (quy tắc kiểm soát truy cập), **vulnerability assessments** (đánh giá lỗ hổng), và **compliance requirements** (yêu cầu tuân thủ) cho **Claude Code** environment.

## 🔐 **Access Control Policies** (Chính sách kiểm soát truy cập)

### **1. Permission Matrix** (Ma trận quyền)

#### **Tool-Level Permissions** (Quyền cấp công cụ)
```json
{
  "permissions": {
    "allow": [
      "Agent",           // AI-powered search và analysis
      "Glob",            // File pattern matching  
      "Grep",            // Content search
      "LS",              // Directory listing
      "NotebookRead",    // Jupyter notebook reading
      "Read",            // File reading
      "TodoRead",        // Task list reading  
      "TodoWrite",       // Task list management
      "Edit",            // File modification (requires permission)
      "MultiEdit",       // Batch file edits (requires permission)
      "NotebookEdit",    // Jupyter editing (requires permission)
      "Write",           // File creation (requires permission)
      "Bash",            // Shell command execution (requires permission)
      "WebFetch",        // External web requests (requires permission)
      "WebSearch"        // Web search capabilities (requires permission)
    ],
    "deny": [
      "Bash(rm:*)",      // Prevent destructive deletions
      "Bash(sudo:*)",    // Block administrative commands
      "Bash(chmod:*)",   // Prevent permission changes
      "Write(/etc/*)",   // Block system file writes
      "Edit(/etc/*)"     // Block system file edits
    ]
  }
}
```

#### **Directory-Level Access Control** (Kiểm soát truy cập cấp thư mục)
```json
{
  "permissions": {
    "additionalDirectories": [
      "/home/user/projects/",     // Project workspace
      "/tmp/claude-temp/",        // Temporary workspace
      "../shared-resources/"      // Shared team resources
    ],
    "restrictedPaths": [
      "/etc/",                    // System configuration
      "/root/",                   // Root user directory
      "/var/log/",               // System logs
      "~/.ssh/",                 // SSH keys
      "~/.aws/",                 // AWS credentials
      "~/.config/gcloud/"        // Google Cloud credentials
    ]
  }
}
```

### **2. Role-Based Access Control (RBAC)** (Kiểm soát truy cập dựa trên vai trò)

#### **Developer Role** (Vai trò phát triển)
```json
{
  "role": "developer",
  "permissions": {
    "allow": [
      "Read", "Edit", "Write",
      "Bash(git:*)", "Bash(npm:*)", "Bash(yarn:*)",
      "WebFetch", "TodoWrite"
    ],
    "deny": [
      "Bash(sudo:*)", "Bash(rm:*)",
      "Write(/etc/*)", "Edit(/etc/*)"
    ],
    "defaultMode": "acceptEdits"
  }
}
```

#### **DevOps Role** (Vai trò DevOps)
```json
{
  "role": "devops",
  "permissions": {
    "allow": [
      "Read", "Edit", "Write", "Bash",
      "WebFetch", "WebSearch", "TodoWrite"
    ],
    "deny": [
      "Bash(rm:/ *)", "Write(/etc/passwd)", 
      "Edit(/etc/shadow)"
    ],
    "defaultMode": "bypassPermissions",
    "requireApproval": ["Bash(docker:*)", "Bash(kubectl:*)"]
  }
}
```

#### **Auditor Role** (Vai trò kiểm toán)
```json
{
  "role": "auditor",
  "permissions": {
    "allow": [
      "Read", "Grep", "LS", "Agent",
      "NotebookRead", "TodoRead"
    ],
    "deny": [
      "Edit", "Write", "Bash", "MultiEdit",
      "NotebookEdit", "TodoWrite"
    ],
    "defaultMode": "readOnly"
  }
}
```

---

## 🛡️ **Data Protection Policies** (Chính sách bảo vệ dữ liệu)

### **1. Sensitive Data Classification** (Phân loại dữ liệu nhạy cảm)

#### **Confidential Data** (Dữ liệu bí mật)
- **API Keys** (khóa API) - `*_API_KEY`, `*_TOKEN`, `*_SECRET`
- **Database Credentials** (thông tin đăng nhập cơ sở dữ liệu) - Passwords, connection strings
- **Private Keys** (khóa riêng) - SSH keys, SSL certificates, cryptographic keys
- **Personal Information** (thông tin cá nhân) - Email addresses, phone numbers, addresses

#### **Detection Patterns** (Mẫu phát hiện)
```regex
# API Keys
[A-Za-z0-9_-]{20,}

# AWS Access Keys
AKIA[0-9A-Z]{16}

# Private Keys
-----BEGIN (RSA |DSA |EC |OPENSSH )?PRIVATE KEY-----

# Email Addresses
[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}

# Credit Card Numbers
\b\d{4}[- ]?\d{4}[- ]?\d{4}[- ]?\d{4}\b
```

### **2. Data Handling Procedures** (Thủ tục xử lý dữ liệu)

#### **Data at Rest** (Dữ liệu lưu trữ)
```bash
# Encryption Requirements
export ENCRYPTION_ALGORITHM="AES-256-GCM"
export KEY_DERIVATION="PBKDF2"
export SALT_LENGTH="32"

# File Encryption
encrypt_file() {
  local file="$1"
  local password="$2"
  
  openssl enc -aes-256-cbc -salt \
    -in "$file" \
    -out "$file.enc" \
    -pass pass:"$password"
  
  # Secure delete original
  shred -vfz -n 3 "$file"
}
```

#### **Data in Transit** (Dữ liệu truyền tải)
```bash
# TLS Configuration
export TLS_MIN_VERSION="1.2"
export CIPHER_SUITES="ECDHE-RSA-AES128-GCM-SHA256,ECDHE-RSA-AES256-GCM-SHA384"
export VERIFY_SSL_CERTIFICATES="true"

# Secure HTTP Requests
secure_curl() {
  curl --tlsv1.2 \
       --ciphers "$CIPHER_SUITES" \
       --cert-status \
       --fail \
       "$@"
}
```

### **3. Secret Management** (Quản lý bí mật)

#### **Environment Variable Security** (Bảo mật biến môi trường)
```bash
# Safe Environment Setup
setup_secure_env() {
  # Create secure environment file
  touch .env.secure
  chmod 600 .env.secure
  
  # Add to gitignore
  echo ".env.secure" >> .gitignore
  echo "*.key" >> .gitignore
  echo "*.pem" >> .gitignore
  
  # Set restrictive umask
  umask 077
}

# API Key Helper Pattern
api_key_helper() {
  local service="$1"
  
  case "$service" in
    "anthropic")
      # Use external credential manager
      vault kv get -field=api_key secret/anthropic
      ;;
    "openai")
      # Use AWS Secrets Manager
      aws secretsmanager get-secret-value \
        --secret-id "openai-api-key" \
        --query SecretString --output text
      ;;
    *)
      echo "Unknown service: $service" >&2
      return 1
      ;;
  esac
}
```

#### **Credential Rotation** (Xoay vòng thông tin đăng nhập)
```bash
# Automated Key Rotation
rotate_api_keys() {
  local service="$1"
  local old_key=$(get_current_key "$service")
  
  # Generate new key
  local new_key=$(generate_new_key "$service")
  
  # Test new key
  if test_api_key "$service" "$new_key"; then
    # Update in secret manager
    update_secret "$service" "$new_key"
    
    # Revoke old key after grace period
    schedule_key_revocation "$service" "$old_key" "+24hours"
  else
    echo "Failed to validate new key for $service" >&2
    return 1
  fi
}
```

---

## 🚨 **Vulnerability Management** (Quản lý lỗ hổng)

### **1. Dependency Scanning** (Quét phụ thuộc)

#### **Automated Vulnerability Checks** (Kiểm tra lỗ hổng tự động)
```bash
# NPM Security Audit
npm_security_check() {
  npm audit --audit-level=high
  npm audit fix --dry-run
}

# Python Security Scan
python_security_check() {
  # Safety check for known vulnerabilities
  safety check --json
  
  # Bandit for static analysis
  bandit -r . -f json
  
  # Semgrep for SAST
  semgrep --config=auto .
}

# Docker Image Scanning
docker_security_scan() {
  local image="$1"
  
  # Trivy vulnerability scanner
  trivy image "$image"
  
  # Docker Scout (if available)
  docker scout cves "$image"
}
```

#### **Supply Chain Security** (Bảo mật chuỗi cung ứng)
```bash
# Package Integrity Verification
verify_package_integrity() {
  local package="$1"
  local expected_hash="$2"
  
  # Download và verify checksum
  local actual_hash=$(sha256sum "$package" | cut -d' ' -f1)
  
  if [ "$actual_hash" != "$expected_hash" ]; then
    echo "Package integrity check failed for $package" >&2
    return 1
  fi
}

# Lock File Validation
validate_lock_files() {
  # Check for unexpected changes
  git diff --exit-code package-lock.json
  git diff --exit-code yarn.lock
  git diff --exit-code Pipfile.lock
}
```

### **2. Code Security Analysis** (Phân tích bảo mật code)

#### **Static Analysis Security Testing (SAST)** (Kiểm thử bảo mật phân tích tĩnh)
```bash
# CodeQL Analysis
codeql_scan() {
  codeql database create codeql-db --language=javascript
  codeql database analyze codeql-db \
    javascript-security-and-quality.qls \
    --format=sarif-latest \
    --output=results.sarif
}

# SonarQube Security Scan
sonarqube_scan() {
  sonar-scanner \
    -Dsonar.projectKey=claude-code-setup \
    -Dsonar.sources=. \
    -Dsonar.host.url=$SONAR_HOST_URL \
    -Dsonar.login=$SONAR_TOKEN
}
```

#### **Dynamic Application Security Testing (DAST)** (Kiểm thử bảo mật ứng dụng động)
```bash
# OWASP ZAP Scanning
zap_scan() {
  local target_url="$1"
  
  # Start ZAP daemon
  zap.sh -daemon -port 8080 -config api.disablekey=true
  
  # Spider the application
  zap-cli open-url "$target_url"
  zap-cli spider "$target_url"
  
  # Active scan
  zap-cli active-scan "$target_url"
  
  # Generate report
  zap-cli report -o zap-report.html -f html
}
```

---

## 🔍 **Security Monitoring** (Giám sát bảo mật)

### **1. Audit Logging** (Ghi log kiểm toán)

#### **Command Execution Logging** (Ghi log thực thi lệnh)
```bash
# Bash Command Auditing
audit_bash_command() {
  local command="$1"
  local user="$2"
  local timestamp=$(date -u +"%Y-%m-%dT%H:%M:%SZ")
  
  # Log to audit file
  echo "${timestamp}|${user}|${command}" >> /var/log/claude-audit.log
  
  # Send to SIEM
  logger -p local0.info "CLAUDE_AUDIT: user=${user} command=${command}"
}

# File Access Logging
audit_file_access() {
  local operation="$1"  # read, write, edit
  local file_path="$2"
  local user="$3"
  local timestamp=$(date -u +"%Y-%m-%dT%H:%M:%SZ")
  
  # Check if file contains sensitive data
  if contains_sensitive_data "$file_path"; then
    local classification="SENSITIVE"
  else
    local classification="NORMAL"
  fi
  
  # Log access
  echo "${timestamp}|${user}|${operation}|${file_path}|${classification}" \
    >> /var/log/claude-file-audit.log
}
```

#### **API Access Monitoring** (Giám sát truy cập API)
```bash
# API Call Auditing
audit_api_call() {
  local service="$1"
  local endpoint="$2"
  local method="$3"
  local user="$4"
  local response_code="$5"
  local timestamp=$(date -u +"%Y-%m-%dT%H:%M:%SZ")
  
  # Log API call
  cat << EOF >> /var/log/claude-api-audit.log
{
  "timestamp": "$timestamp",
  "user": "$user",
  "service": "$service",
  "endpoint": "$endpoint",
  "method": "$method",
  "response_code": "$response_code",
  "session_id": "$SESSION_ID"
}
EOF
}
```

### **2. Anomaly Detection** (Phát hiện bất thường)

#### **Behavioral Analysis** (Phân tích hành vi)
```bash
# Unusual Command Pattern Detection
detect_anomalies() {
  local user="$1"
  local recent_commands=$(tail -100 /var/log/claude-audit.log | grep "$user")
  
  # Check for unusual patterns
  local sudo_attempts=$(echo "$recent_commands" | grep -c "sudo")
  local rm_attempts=$(echo "$recent_commands" | grep -c "rm -rf")
  local network_access=$(echo "$recent_commands" | grep -c "curl\|wget")
  
  if [ "$sudo_attempts" -gt 5 ] || [ "$rm_attempts" -gt 2 ]; then
    alert_security_team "Suspicious activity detected for user $user"
  fi
}

# Rate Limiting Monitoring
check_rate_limits() {
  local user="$1"
  local time_window="300"  # 5 minutes
  local max_requests="100"
  
  local request_count=$(tail -n 1000 /var/log/claude-api-audit.log | \
    jq -r --arg user "$user" --arg since "$(($(date +%s) - time_window))" \
    'select(.user == $user and (.timestamp | strptime("%Y-%m-%dT%H:%M:%SZ") | mktime) > ($since | tonumber)) | .timestamp' | \
    wc -l)
  
  if [ "$request_count" -gt "$max_requests" ]; then
    echo "Rate limit exceeded for user $user: $request_count requests in ${time_window}s"
    return 1
  fi
}
```

---

## 📋 **Compliance Requirements** (Yêu cầu tuân thủ)

### **1. GDPR Compliance** (Tuân thủ GDPR)

#### **Data Processing Principles** (Nguyên tắc xử lý dữ liệu)
```bash
# Data Minimization
minimize_data_collection() {
  # Only collect necessary data
  # Automatically delete old logs
  find /var/log/claude-*.log -mtime +90 -delete
  
  # Anonymize IP addresses
  sed -i 's/\([0-9]\{1,3\}\.\)\{3\}[0-9]\{1,3\}/XXX.XXX.XXX.XXX/g' \
    /var/log/claude-access.log
}

# Right to be Forgotten
process_deletion_request() {
  local user_id="$1"
  
  # Remove user data from logs
  sed -i "/${user_id}/d" /var/log/claude-*.log
  
  # Clear user configurations
  rm -f "/home/${user_id}/.claude/settings.json"
  
  # Notify data processors
  echo "User $user_id data deletion completed at $(date)" \
    >> /var/log/gdpr-deletions.log
}
```

#### **Data Protection Impact Assessment** (Đánh giá tác động bảo vệ dữ liệu)
- **Processing Purpose** (mục đích xử lý): Development assistance và code generation
- **Data Categories** (danh mục dữ liệu): Technical code, configuration files, logs
- **Legal Basis** (cơ sở pháp lý): Legitimate interest for service provision
- **Retention Period** (thời gian lưu trữ): 90 days for logs, indefinite for code
- **Risk Mitigation** (giảm thiểu rủi ro): Encryption, access controls, audit logging

### **2. SOC 2 Compliance** (Tuân thủ SOC 2)

#### **Security Principle Controls** (Kiểm soát nguyên tắc bảo mật)
```bash
# Access Review Process
quarterly_access_review() {
  # Generate access report
  cat << EOF > access_review_$(date +%Y%Q).txt
# Quarterly Access Review - $(date +%Y-Q%q)

## Current Users with Claude Code Access:
$(cat /etc/passwd | awk -F: '$3 >= 1000 {print $1, $5}')

## Permission Configurations:
$(find /home -name ".claude" -type d -exec ls -la {}/settings.json \; 2>/dev/null)

## Recent Access Patterns:
$(tail -1000 /var/log/claude-audit.log | awk -F'|' '{print $2}' | sort | uniq -c | sort -nr)
EOF
  
  # Send for review
  mail -s "Quarterly Access Review" security-team@company.com < access_review_$(date +%Y%Q).txt
}

# Backup và Recovery Testing
test_backup_recovery() {
  local test_date=$(date +%Y%m%d)
  local backup_location="/backup/claude-$(date +%Y%m%d)"
  
  # Create test backup
  mkdir -p "$backup_location"
  cp -r /home/*/.claude "$backup_location/"
  cp /var/log/claude-*.log "$backup_location/"
  
  # Test recovery
  local test_restore="/tmp/restore-test-$test_date"
  mkdir -p "$test_restore"
  cp -r "$backup_location"/* "$test_restore/"
  
  # Validate integrity
  if diff -r "$backup_location" "$test_restore" > /dev/null; then
    echo "Backup recovery test PASSED - $test_date"
  else
    echo "Backup recovery test FAILED - $test_date" >&2
    return 1
  fi
  
  # Cleanup test files
  rm -rf "$test_restore"
}
```

---

## 🚨 **Incident Response** (Ứng phó sự cố)

### **1. Security Incident Classification** (Phân loại sự cố bảo mật)

#### **Severity Levels** (Mức độ nghiêm trọng)
- **Critical (P0)**: **Data breach** (vi phạm dữ liệu), unauthorized access to production
- **High (P1)**: **Privilege escalation** (leo thang đặc quyền), malware detection
- **Medium (P2)**: **Policy violations** (vi phạm chính sách), suspicious activities
- **Low (P3)**: **Configuration drift** (drift cấu hình), minor compliance issues

#### **Response Procedures** (Thủ tục ứng phó)
```bash
# Incident Response Automation
security_incident_response() {
  local severity="$1"
  local description="$2"
  local affected_systems="$3"
  
  case "$severity" in
    "critical"|"high")
      # Immediate isolation
      isolate_affected_systems "$affected_systems"
      
      # Alert security team
      alert_security_team "URGENT: $severity security incident - $description"
      
      # Start incident tracking
      create_incident_ticket "$severity" "$description" "$affected_systems"
      
      # Begin forensic collection
      collect_forensic_evidence "$affected_systems"
      ;;
    "medium"|"low")
      # Standard response
      log_security_incident "$severity" "$description"
      create_incident_ticket "$severity" "$description" "$affected_systems"
      ;;
  esac
}

# System Isolation
isolate_affected_systems() {
  local systems="$1"
  
  for system in $systems; do
    # Disable Claude Code access
    systemctl stop claude-code@"$system" 2>/dev/null || true
    
    # Revoke API keys
    revoke_user_api_keys "$system"
    
    # Block network access if needed
    iptables -A OUTPUT -m owner --uid-owner "$system" -j DROP
    
    echo "System $system isolated at $(date)"
  done
}
```

### **2. Forensic Data Collection** (Thu thập dữ liệu pháp y)
```bash
# Evidence Collection
collect_forensic_evidence() {
  local incident_id="$1"
  local evidence_dir="/tmp/forensics-$incident_id-$(date +%Y%m%d%H%M)"
  
  mkdir -p "$evidence_dir"
  
  # System state
  ps aux > "$evidence_dir/processes.txt"
  netstat -tulpn > "$evidence_dir/network.txt"
  df -h > "$evidence_dir/disk_usage.txt"
  
  # Log files
  cp /var/log/claude-*.log "$evidence_dir/"
  cp /var/log/auth.log "$evidence_dir/"
  cp /var/log/syslog "$evidence_dir/"
  
  # Configuration files
  find /home -name ".claude" -type d -exec cp -r {} "$evidence_dir/" \;
  
  # Create evidence hash
  find "$evidence_dir" -type f -exec sha256sum {} \; > "$evidence_dir/evidence.hash"
  
  # Compress và secure
  tar -czf "evidence-$incident_id.tar.gz" -C /tmp "forensics-$incident_id-$(date +%Y%m%d%H%M)"
  chmod 600 "evidence-$incident_id.tar.gz"
  
  echo "Forensic evidence collected: evidence-$incident_id.tar.gz"
}
```

---

*Security policies được **regularly reviewed** (xem xét thường xuyên) và **updated** (cập nhật) to address **emerging threats** (mối đe dọa mới nổi) và **compliance requirements** (yêu cầu tuân thủ) changes.*