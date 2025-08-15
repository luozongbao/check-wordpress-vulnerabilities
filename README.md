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

---

**⚠️ Disclaimer**: This tool is for authorized security testing only. Users are responsible for ensuring they have permission to test target websites. The authors are not responsible for any misuse of this software.
