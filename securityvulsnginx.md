
**Issue**


Web Server HTTP Header Information Disclosure:
Description
The HTTP headers sent by the remote web server disclose information that can aid an attacker, such as the server version and languages used by theweb server.
Solution
Modify the HTTP headers of the web server to not disclose detailed information about the underlying web server.
Output
Server type : NGINX

Additional data : X-Powered-By: ASP.NET

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

We have observed deprecated or unsupported versions of TLS. We have begun encouraging admins to begin transitioning to the cipher suites listed below. These are compatible with all browsers from the last five years.
TLSv1.3:
- 0x13,0x01 TLS_AES_128_GCM_SHA256 - 0x13,0x02 TLS_AES_256_GCM_SHA384 - 0x13,0x03 TLS_CHACHA20_POLY1305_SHA256
TLSv1.2:
- 0xC0,0x2B ECDHE-ECDSA-AES128-GCM-SHA256 - 0xC0,0x2F ECDHE-RSA-AES128-GCM-SHA256 - 0xC0,0x2C ECDHE-ECDSA-AES256-GCM-SHA384 - 0xC0,0x30 ECDHE-RSA-AES256-GCM-SHA384 - 0xCC,0xA9 ECDHE-ECDSA-CHACHA20-POLY1305 - 0xCC,0xA8 ECDHE-RSA-CHACHA20-POLY1305 - 0x00,0x9E DHE-RSA-AES128-GCM-SHA256 - 0x00,0x9F DHE-RSA-AES256-GCM-SHA384
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


**Solution**


To address the issues mentioned in your report, you need to:

1. **Modify the HTTP headers to prevent information disclosure**.
2. **Ensure that only supported and secure versions of TLS are used**.

### Step 1: Modify HTTP Headers in NGINX

To prevent information disclosure through HTTP headers, you need to configure NGINX to hide the server version and remove the `X-Powered-By` header.

#### Modify NGINX Configuration

1. **Open the NGINX Configuration File**:
   - The main NGINX configuration file is usually located at `/etc/nginx/nginx.conf`. You may also need to modify the configuration files in `/etc/nginx/conf.d/` or `/etc/nginx/sites-available/` depending on your setup.

   ```sh
   sudo nano /etc/nginx/nginx.conf
   ```

2. **Hide the NGINX Version**:
   - Add the following directive to the `http` block to hide the NGINX version:

   ```nginx
   http {
       ...
       server_tokens off;
       ...
   }
   ```

3. **Remove the `X-Powered-By` Header**:
   - Add the following directives to the `http` block to remove the `X-Powered-By` header:

   ```nginx
   http {
       ...
       server_tokens off;

       # Remove X-Powered-By header
       proxy_hide_header X-Powered-By;
       add_header X-Powered-By "";
       ...
   }
   ```

4. **Modify Server Block**:
   - If you have specific server blocks, you can also add the `proxy_hide_header` and `add_header` directives within each server block:

   ```nginx
   server {
       ...
       server_tokens off;

       # Remove X-Powered-By header
       proxy_hide_header X-Powered-By;
       add_header X-Powered-By "";
       ...
   }
   ```

5. **Test and Reload NGINX Configuration**:
   - Test the NGINX configuration to ensure there are no syntax errors:

   ```sh
   sudo nginx -t
   ```

   - Reload the NGINX configuration to apply the changes:

   ```sh
   sudo systemctl reload nginx
   ```

### Step 2: Configure Supported TLS Versions and Cipher Suites

To ensure that only supported and secure versions of TLS are used, you need to configure NGINX to use the recommended TLS versions and cipher suites.

#### Modify NGINX SSL Configuration

1. **Open the NGINX SSL Configuration File**:
   - This file is usually located at `/etc/nginx/nginx.conf` or in a separate SSL configuration file within `/etc/nginx/conf.d/` or `/etc/nginx/sites-available/`.

   ```sh
   sudo nano /etc/nginx/nginx.conf
   ```

2. **Configure Supported TLS Versions and Cipher Suites**:
   - Add the following directives to the `http` block or the specific server block handling SSL:

   ```nginx
   http {
       ...
       ssl_protocols TLSv1.2 TLSv1.3;
       ssl_prefer_server_ciphers on;
       ssl_ciphers 'TLS_AES_128_GCM_SHA256:TLS_AES_256_GCM_SHA384:TLS_CHACHA20_POLY1305_SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384';
       ...
   }
   ```

3. **Test and Reload NGINX Configuration**:
   - Test the NGINX configuration to ensure there are no syntax errors:

   ```sh
   sudo nginx -t
   ```

   - Reload the NGINX configuration to apply the changes:

   ```sh
   sudo systemctl reload nginx
   ```

### Summary

By following these steps, you can:

1. **Modify HTTP headers** to prevent information disclosure by hiding the server version and removing the `X-Powered-By` header.
2. **Configure supported TLS versions and cipher suites** to ensure that only secure and recommended protocols and ciphers are used.

These changes will enhance the security of your NGINX web server and mitigate the issues mentioned in your report.


