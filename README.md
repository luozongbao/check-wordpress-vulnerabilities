# WordPress Vulnerability Scanner

A comprehensive bash script to assess WordPress websites for common security vulnerabilities and misconfigurations.

## 🔍 Overview

This tool performs automated security assessments of WordPress websites, checking for various vulnerabilities including outdated software, exposed sensitive files, weak configurations, and common attack vectors.

## ✨ Features

### Core Security Checks
- **WordPress Detection** - Automatically detects if target site runs WordPress
- **Version Analysis** - Identifies WordPress version and checks for outdated installations
- **Exposed Files Scanning** - Searches for publicly accessible sensitive files
- **Directory Listing Detection** - Identifies improperly configured directories
- **XML-RPC Assessment** - Tests for exposed XML-RPC endpoints (brute force targets)
- **User Enumeration Testing** - Checks for username disclosure vulnerabilities
- **Login Security Analysis** - Evaluates admin area protection
- **SSL/TLS Configuration** - Validates HTTPS setup and certificate status
- **Security Headers Review** - Checks for essential HTTP security headers
- **Plugin Detection** - Identifies common plugins with known vulnerabilities

### Output Features
- **Color-coded Results** - Easy-to-read severity levels (Critical/Warning/Safe)
- **Detailed Reporting** - Comprehensive vulnerability explanations
- **Multiple Protocol Support** - Tests both HTTP and HTTPS connections
- **Connectivity Validation** - Ensures target accessibility before testing

## 🚀 Quick Start

### Prerequisites
- Linux/Unix environment
- Bash shell
- Standard utilities: `curl`, `grep`, `openssl`

### Installation
```bash
# Clone or download the script
git clone <repository-url>
cd check_wordpress_vulnerability

# Make executable
chmod +x check_wp_vulnerability.sh
```

### Basic Usage
```bash
./check_wp_vulnerability.sh example.com
```

### Example Output
```
==========================================
    WordPress Vulnerability Scanner
==========================================

[INFO] Scanning: example.com
[SAFE] HTTPS connection successful
[SAFE] WordPress detected
[WARNING] WordPress version detected: 5.8.1
[CRITICAL] WordPress version is significantly outdated!
[CRITICAL] Exposed file: readme.html
[SAFE] Directory listing check completed
[CRITICAL] XML-RPC is enabled and accessible (potential brute force target)
...
```

## 🔧 Vulnerability Categories

### 🔴 Critical Issues
- Exposed configuration files (`wp-config.php`, backups)
- Directory listing enabled
- User enumeration possible
- XML-RPC publicly accessible
- Missing HTTPS encryption
- Severely outdated WordPress versions

### 🟡 Warnings
- Missing security headers (HSTS, CSP, X-Frame-Options)
- Accessible login pages without protection
- Moderately outdated WordPress versions
- Exposed documentation files

### 🟢 Safe Indicators
- Proper file permissions and access controls
- Security headers implemented
- Recent WordPress versions
- Protected admin areas

## 📋 Detailed Checks

### File Exposure Assessment
The scanner checks for these sensitive files:
```
wp-config.php (and variants/backups)
readme.html
license.txt
.htaccess
debug.log
phpinfo.php
error_log
```

### Security Headers Validation
- **X-Frame-Options** - Clickjacking protection
- **X-Content-Type-Options** - MIME type sniffing prevention
- **X-XSS-Protection** - Cross-site scripting filters
- **Content-Security-Policy** - Content injection prevention
- **Strict-Transport-Security** - HTTPS enforcement

### Directory Security Testing
```
wp-content/
wp-content/uploads/
wp-content/plugins/
wp-content/themes/
wp-includes/
wp-admin/
```

## 🛠️ Advanced Usage

### Custom Domain Testing
```bash
# Test with subdomain
./check_wp_vulnerability.sh subdomain.example.com

# Test with specific protocol (script auto-detects)
./check_wp_vulnerability.sh secure-site.com
```

### Batch Testing
```bash
# Create a list of domains
echo "site1.com" > domains.txt
echo "site2.com" >> domains.txt

# Test multiple sites
while read domain; do
    echo "Testing $domain..."
    ./check_wp_vulnerability.sh "$domain"
    echo "------------------------"
done < domains.txt
```

## 🔒 Security Considerations

### Ethical Usage
- **Only test websites you own or have explicit permission to test**
- This tool performs non-invasive reconnaissance
- No actual exploitation attempts are made
- Respect rate limits and server resources

### Limitations
- **Surface-level assessment** - Not a replacement for professional penetration testing
- **Public information only** - Cannot detect internal vulnerabilities
- **Version detection may fail** - If WordPress version is properly hidden
- **Network dependent** - Requires internet connectivity

## 📊 Interpreting Results

### Risk Levels
| Color | Level | Action Required |
|-------|-------|----------------|
| 🔴 Red | Critical | Immediate attention needed |
| 🟡 Yellow | Warning | Should be addressed |
| 🟢 Green | Safe | Good security practice |

### Common Fixes
- **Update WordPress** to latest version
- **Remove exposed files** (readme.html, license.txt)
- **Disable directory listing** in web server config
- **Implement security headers** via .htaccess or server config
- **Disable XML-RPC** if not needed
- **Use HTTPS** with proper certificates
- **Protect admin areas** with additional authentication

## 🧪 Testing Examples

### Test Local Development
```bash
./check_wp_vulnerability.sh localhost:8080
```

### Test Staging Environment
```bash
./check_wp_vulnerability.sh staging.yoursite.com
```

## 🤝 Contributing

Contributions are welcome! Areas for improvement:
- Additional vulnerability checks
- Performance optimizations
- Enhanced reporting formats
- Integration with other security tools

## 📝 Changelog

### v1.0.0
- Initial release with core vulnerability checks
- Color-coded output system
- SSL/TLS validation
- Security headers assessment

## ⚖️ License

This project is provided for educational and authorized security testing purposes only.

## 🔗 Related Tools

For more comprehensive testing, consider:
- **WPScan** - Specialized WordPress vulnerability scanner
- **Nessus** - Professional vulnerability assessment
- **OWASP ZAP** - Web application security testing
- **Nikto** - Web server scanner

## 📞 Support

For issues or questions:
1. Check existing issues in the repository
2. Create detailed bug reports with target URLs (if public)
3. Include error messages and environment details

## 📚 Appendix: Vulnerability Remediation Guide

### WordPress-Level Fixes

#### 🔴 Critical Vulnerabilities

**Exposed wp-config.php**
```bash
# Move wp-config.php one directory above web root
mv wp-config.php ../
# Or add .htaccess protection
echo '<files wp-config.php>
order allow,deny
deny from all
</files>' >> .htaccess
```

**Outdated WordPress Version**
```bash
# Update via WP-CLI
wp core update
wp core update-db

# Or via WordPress admin dashboard
# Dashboard → Updates → Update Now
```

**XML-RPC Exposed**
```php
// Add to wp-config.php
add_filter('xmlrpc_enabled', '__return_false');

// Or via .htaccess
<Files xmlrpc.php>
Order Deny,Allow
Deny from all
</Files>
```

**User Enumeration**
```php
// Add to functions.php
function disable_user_enumeration() {
    if (is_admin() && !defined('DOING_AJAX')) return;
    if (isset($_REQUEST['author'])) {
        wp_redirect(home_url(), 301);
        exit;
    }
}
add_action('init', 'disable_user_enumeration');

// Block REST API user endpoint
function restrict_rest_api_users($result, $server, $request) {
    if (strpos($request->get_route(), '/wp/v2/users') !== false) {
        return new WP_Error('rest_user_cannot_view', 'Sorry, you are not allowed to list users.', array('status' => 401));
    }
    return $result;
}
add_filter('rest_pre_dispatch', 'restrict_rest_api_users', 10, 3);
```

#### 🟡 Warning-Level Issues

**Missing Security Headers**
```php
// Add to wp-config.php or functions.php
function add_security_headers() {
    header('X-Frame-Options: SAMEORIGIN');
    header('X-Content-Type-Options: nosniff');
    header('X-XSS-Protection: 1; mode=block');
    header('Referrer-Policy: strict-origin-when-cross-origin');
    header('Permissions-Policy: geolocation=(), microphone=(), camera=()');
}
add_action('send_headers', 'add_security_headers');
```

**Accessible Login Page**
```php
// Rename login URL (via plugin or custom code)
// Or add IP restrictions to .htaccess
<Files wp-login.php>
Order Deny,Allow
Deny from all
Allow from 192.168.1.0/24
Allow from your.office.ip.address
</Files>
```

### Apache Server Configuration

#### Security Headers in .htaccess
```apache
# Add comprehensive security headers
<IfModule mod_headers.c>
    # Prevent clickjacking
    Header always set X-Frame-Options "SAMEORIGIN"
    
    # Prevent MIME type sniffing
    Header always set X-Content-Type-Options "nosniff"
    
    # XSS Protection
    Header always set X-XSS-Protection "1; mode=block"
    
    # Strict Transport Security (HTTPS only)
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains; preload"
    
    # Content Security Policy
    Header always set Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline'; img-src 'self' data: https:; font-src 'self' https:; connect-src 'self'; frame-ancestors 'self';"
    
    # Referrer Policy
    Header always set Referrer-Policy "strict-origin-when-cross-origin"
    
    # Remove server signature
    Header always unset Server
    Header always unset X-Powered-By
</IfModule>

# Disable directory browsing
Options -Indexes

# Protect sensitive files
<FilesMatch "^(wp-config\.php|\.htaccess|\.htpasswd|error_log|debug\.log)$">
    Order Allow,Deny
    Deny from all
</FilesMatch>

# Protect wp-content directories
<Directory "*/wp-content/uploads/">
    <Files "*.php">
        Order Allow,Deny
        Deny from all
    </Files>
</Directory>

# Block access to wp-includes
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteBase /
    RewriteRule ^wp-admin/includes/ - [F,L]
    RewriteRule !^wp-includes/ - [S=3]
    RewriteRule ^wp-includes/[^/]+\.php$ - [F,L]
    RewriteRule ^wp-includes/js/tinymce/langs/.+\.php - [F,L]
    RewriteRule ^wp-includes/theme-compat/ - [F,L]
</IfModule>

# Limit file upload size
LimitRequestBody 10485760  # 10MB

# Disable XML-RPC
<Files xmlrpc.php>
    Order Allow,Deny
    Deny from all
</Files>
```

#### Apache Virtual Host SSL Configuration
```apache
<VirtualHost *:443>
    ServerName example.com
    DocumentRoot /var/www/html
    
    # SSL Configuration
    SSLEngine on
    SSLCertificateFile /path/to/certificate.crt
    SSLCertificateKeyFile /path/to/private.key
    SSLCertificateChainFile /path/to/chain.crt
    
    # Modern SSL configuration
    SSLProtocol all -SSLv2 -SSLv3 -TLSv1 -TLSv1.1
    SSLCipherSuite ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384
    SSLHonorCipherOrder on
    
    # OCSP Stapling
    SSLUseStapling on
    SSLStaplingResponderTimeout 5
    SSLStaplingReturnResponderErrors off
    
    # Security headers
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains; preload"
    
    # Hide server information
    ServerTokens Prod
    ServerSignature Off
</VirtualHost>

# Redirect HTTP to HTTPS
<VirtualHost *:80>
    ServerName example.com
    Redirect permanent / https://example.com/
</VirtualHost>
```

### Nginx Server Configuration

#### Security Headers in nginx.conf
```nginx
server {
    listen 443 ssl http2;
    server_name example.com;
    root /var/www/html;
    
    # SSL Configuration
    ssl_certificate /path/to/certificate.crt;
    ssl_certificate_key /path/to/private.key;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384;
    ssl_prefer_server_ciphers off;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    
    # OCSP Stapling
    ssl_stapling on;
    ssl_stapling_verify on;
    ssl_trusted_certificate /path/to/chain.crt;
    
    # Security Headers
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;
    add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline'; img-src 'self' data: https:; font-src 'self' https:; connect-src 'self'; frame-ancestors 'self';" always;
    
    # Hide Nginx version
    server_tokens off;
    
    # Disable access to sensitive files
    location ~* /(?:uploads|files)/.*\.php$ {
        deny all;
    }
    
    location ~ /\. {
        deny all;
        access_log off;
        log_not_found off;
    }
    
    location ~ ~$ {
        deny all;
        access_log off;
        log_not_found off;
    }
    
    # Block access to wp-config and other sensitive files
    location ~* /(wp-config\.php|readme\.html|license\.txt|xmlrpc\.php) {
        deny all;
    }
    
    # Protect wp-admin
    location /wp-admin/ {
        # Allow only specific IPs
        allow 192.168.1.0/24;
        allow your.office.ip.address;
        deny all;
        
        location ~ \.php$ {
            include fastcgi_params;
            # Adjust the PHP version below to match your installed PHP-FPM version (e.g., php8.2-fpm.sock)
            fastcgi_pass unix:/var/run/php/php<VERSION>-fpm.sock;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        }
    }
    
    # Rate limiting for login attempts
    location = /wp-login.php {
        limit_req zone=login burst=2 nodelay;
        include fastcgi_params;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
    
    # Standard PHP processing
    location ~ \.php$ {
        include fastcgi_params;
        # Adjust PHP version as needed (e.g., php8.1-fpm.sock, php8.2-fpm.sock)
        fastcgi_pass unix:/var/run/php/php<version>-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
    
    # Standard PHP processing
    location ~ \.php$ {
        include fastcgi_params;
        # Adjust PHP version as needed (e.g., php8.1-fpm.sock, php8.2-fpm.sock)
        fastcgi_pass unix:/var/run/php/php<version>-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_intercept_errors on;
    }
}

# HTTP to HTTPS redirect
server {
    listen 80;
    server_name example.com;
    return 301 https://$server_name$request_uri;
}

# Rate limiting configuration (add to http block)
http {
    limit_req_zone $binary_remote_addr zone=login:10m rate=1r/m;
    limit_req_zone $binary_remote_addr zone=general:10m rate=10r/s;
}
```

### System-Level Security Enhancements

#### File Permissions
```bash
# WordPress recommended permissions
find /var/www/html/ -type d -exec chmod 755 {} \;
find /var/www/html/ -type f -exec chmod 644 {} \;
chmod 600 wp-config.php
chmod 644 .htaccess

# Secure ownership
chown -R www-data:www-data /var/www/html/
```

#### Firewall Configuration (UFW)
```bash
# Basic firewall setup
ufw default deny incoming
ufw default allow outgoing
ufw allow ssh
ufw allow 80/tcp
ufw allow 443/tcp
ufw enable

# Rate limiting for HTTP(S)
ufw limit 80/tcp
ufw limit 443/tcp
```

#### Fail2Ban Configuration
```ini
# /etc/fail2ban/jail.local
[wordpress]
enabled = true
filter = wordpress
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600
findtime = 600

[nginx-limit-req]
enabled = true
filter = nginx-limit-req
action = iptables-multiport[name=ReqLimit, port="http,https", protocol=tcp]
logpath = /var/log/nginx/error.log
findtime = 600
bantime = 7200
maxretry = 10
```

### Monitoring and Maintenance

#### Log Monitoring
```bash
# Monitor failed login attempts
tail -f /var/log/auth.log | grep wordpress

# Monitor web server access
tail -f /var/log/nginx/access.log | grep -E "(wp-login|wp-admin)"

# Check for suspicious PHP errors
tail -f /var/log/nginx/error.log | grep -i "php"
```

#### Automated Security Updates
```bash
# Enable automatic WordPress core updates
echo "define('WP_AUTO_UPDATE_CORE', true);" >> wp-config.php

# Cron job for security updates (Ubuntu/Debian)
# Enable unattended security upgrades (Ubuntu/Debian)
apt update && apt install -y unattended-upgrades
dpkg-reconfigure --priority=low unattended-upgrades
# For more configuration, edit /etc/apt/apt.conf.d/50unattended-upgrades
```

#### Regular Security Audits
```bash
# Create monthly security check script
#!/bin/bash
# /opt/security-check.sh

echo "=== Monthly Security Audit ===" > /var/log/security-audit.log
date >> /var/log/security-audit.log

# Check for outdated packages
apt list --upgradable >> /var/log/security-audit.log 2>/dev/null

# Check file permissions
find /var/www/html -name "*.php" -perm 777 >> /var/log/security-audit.log

# Check for suspicious files
find /var/www/html -name "*.php" -mtime -7 -ls >> /var/log/security-audit.log

# Run WordPress vulnerability scan
/path/to/check_wp_vulnerability.sh yourdomain.com >> /var/log/security-audit.log

# Email results
mail -s "Monthly Security Audit" admin@yourdomain.com < /var/log/security-audit.log
```

### Emergency Response Procedures

#### Suspected Compromise
```bash
# 1. Immediately change all passwords
wp user update admin --user_pass=new_strong_password

# 2. Check for malicious files
find /var/www/html -name "*.php" -type f -exec grep -l "eval\|base64_decode\|exec\|system" {} \;

# 3. Review recent file changes
find /var/www/html -type f -mtime -7 -ls

# 4. Check access logs for suspicious activity
grep -E "(POST|GET).*(wp-admin|wp-login)" /var/log/nginx/access.log | tail -100

# 5. Restore from clean backup if necessary
# Always keep clean, tested backups!
```

---

**⚠️ Disclaimer**: This tool is for authorized security testing only. Users are responsible for ensuring they have permission to test target websites. The authors are not responsible for any misuse of this software.
