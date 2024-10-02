# Caddy Configuration for Docker Registry

This guide provides a configuration block for Caddy to integrate with a running Docker registry. We use Caddy to add Basic Authentication and IP-based access control to secure the Docker registry.

### Steps:

1. **Edit the Caddyfile**: Add the following configuration to `/etc/caddy/Caddyfile`:

   ```caddy
   pv1.imagesd.kadri.io {
       # Allow only specific IP addresses to access the registry
       @blocked not remote_ip 1.2.3.4 5.6.7.8   # Replace with allowed IPs
       respond @blocked "Access Denied" 403

       # Basic Authentication
       basicauth {
           your_username $2a$14$ti9bNevT49m1IgydferbOiwuX2YkfjcwZfGG9xQSugwyOfXpe0Ra  # Replace with bcrypt hash
       }

       # Reverse Proxy to Docker Registry
       reverse_proxy 127.0.0.1:8080 {  # Replace with registry IP and port
           header_up Host {host}
           header_up X-Real-IP {remote_host}
           header_up X-Forwarded-For {remote_host}
           header_up X-Forwarded-Proto {scheme}
       }
   }
   ```

2. **Replace Placeholders**:
   - `your_username`: Replace this with the username that will be used to log in to the registry.
   - `bcrypt hash`: Replace the hashed password with a hash generated using the [Caddy `hash-password`](https://caddyserver.com/docs/command-line#caddy-hash-password) command. For example:

     ```bash
     caddy hash-password
     ```
     Enter the password twice.
     This will output a bcrypt hash for your password.
   
   - `127.0.0.1:8080`: Replace with the actual IP address and port of your Docker registry.

3. **IP Access Control**:
   - Replace `1.2.3.4` and `5.6.7.8` with the actual IP addresses that should be allowed access to your Docker registry.

---

### Notes:
- **Basic Auth Security**: The password is encrypted using bcrypt for security. Make sure you use a strong password and keep it secure.
- **IP Whitelisting**: Only the IPs specified will be allowed to access the registry; others will see a "403 Access Denied" response.

---
