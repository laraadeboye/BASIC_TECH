
# NGINX Troubleshooting and Configuration Files

1. Check the status of the NGINX service to get more detailed information:
```sh
sudo systemctl status nginx
```
2. Look at the NGINX error log for more detailed error messages:
```sh
sudo tail -n 50 /var/log/nginx/error.log

```
3. Look for relevant entries in the system journal
```sh
sudo journalctl -u nginx.service -b
```
4. Ensure no other service is using the same ports (usually 80 and 443). You can check with:
```sh
sudo netstat -tuln | grep ':80\|:443'
```

5. If you recently changed NGINX configurations or updated the package, reload the systemd daemon. And check the status of the NGINX service to get more detailed information:
```sh
sudo systemctl daemon-reload
sudo systemctl restart nginx
sudo systemctl status nginx
```
6. Run syntax check
```sh
sudo nginx -t
```

7. If default NGINX page is not showing, you may need to create a symbolic link from where it exist to site-enabled
```sh
sudo ln -s /etc/nginx/sites-available/default /etc/nginx/sites-enabled/
```

8. Verify the document root
```sh
ls -l /var/www/html
```
9. If there is no index.html, create a simple one to test:
```sh
echo "<html><body><h1>Welcome to NGINX!</h1></body></html>" | sudo tee /var/www/html/index.html

```
10. Test page locally:
```sh
curl -I http://localhost

```


#### Example Default nginx.conf file

```sh
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
	worker_connections 768;
	# multi_accept on;
}

http {

	##
	# Basic Settings
	##

	sendfile on;
	tcp_nopush on;
	types_hash_max_size 2048;
	# server_tokens off;

	# server_names_hash_bucket_size 64;
	# server_name_in_redirect off;

	include /etc/nginx/mime.types;
	default_type application/octet-stream;

	##
	# SSL Settings
	##

	ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
	ssl_prefer_server_ciphers on;

	##
	# Logging Settings
	##

	access_log /var/log/nginx/access.log;
	error_log /var/log/nginx/error.log;

	##
	# Gzip Settings
	##

	gzip on;

	# gzip_vary on;
	# gzip_proxied any;
	# gzip_comp_level 6;
	# gzip_buffers 16 8k;
	# gzip_http_version 1.1;
	# gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

	##
	# Virtual Host Configs
	##

	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;
}


#mail {
#	# See sample authentication script at:
#	# http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript
#
#	# auth_http localhost/auth.php;
#	# pop3_capabilities "TOP" "USER";
#	# imap_capabilities "IMAP4rev1" "UIDPLUS";
#
#	server {
#		listen     localhost:110;
#		protocol   pop3;
#		proxy      on;
#	}
#
#	server {
#		listen     localhost:143;
#		protocol   imap;
#		proxy      on;
#	}
#}

```

### Full configuration that includes serving static content, reverse proxying, caching, gzip compression, SSL/TLS, rate limiting

#### `nginx.conf`

```sh
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
    worker_connections 1024;
}

http {
    # Cache path and settings
    proxy_cache_path /etc/nginx/cache levels=1:2 keys_zone=my_cache:10m max_size=10g inactive=60m use_temp_path=off;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    client_max_body_size 2m;

    # Gzip settings
    gzip on;
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_buffers 16 8k;
    gzip_http_version 1.1;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    # Logging settings
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    # Rate limiting
    limit_req_zone $binary_remote_addr zone=one:10m rate=1r/s;

    # Include virtual host configurations
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

#### Assuming your virtual hosts are located in conf.d (e.g `api.example.conf)

```sh
# Redirect HTTP to HTTPS for example.com
server {
    listen 80;
    server_name example.com www.example.com;

    location / {
        return 301 https://$host$request_uri;
    }
}

# HTTPS server configuration for example.com
server {
    listen 443 ssl http2;
    server_name example.com www.example.com;

    # SSL/TLS settings
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;

    root /path/to/your/static/files;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location /api/ {
        proxy_cache my_cache;
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        add_header X-Proxy-Cache $upstream_cache_status;
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /usr/share/nginx/html;
    }

    limit_req zone=one burst=5;
}

```

#### How to Split Configurations:
- **Global Settings**: Keep settings that apply to the entire server or multiple sites in `nginx.conf` (e.g., `proxy_cache_path`, `gzip`, `logging`, `rate limiting`).

- **Virtual Host/Server Blocks**: Use individual configuration files in conf.d for different domains or applications (e.g., `api.example.conf`, `app.example.conf`).


#### Explanation of the Configuration
- **Cache Path**: Configured using `proxy_cache_path` to store cached content.
- **Gzip Compression**: Enabled with various settings to reduce bandwidth usage.
- **SSL/TLS**: Configured for secure communication with certificates.
- **Rate Limiting**: Configured to protect against DDoS attacks.
- **Logging**: Access and error logs are specified.
- **Static and Dynamic Content**: Static files are served directly, while dynamic requests are proxied to backend servers.

# Optimizing Disk space when caching
1. Monitor Disk Space: Use tools like df and du to monitor disk space usage.

```sh
df -h   # Shows disk usage for all mounted filesystems
du -sh /etc/nginx/cache   # Shows disk usage for the cache directory

```

2. Automate Disk Cleanup: Set up a cron job or script to regularly clean up old or unnecessary cache files.

**Example cleanup script**
```sh
#!/bin/bash

# Define cache directory
CACHE_DIR="/etc/nginx/cache"

# Find and delete files older than 30 days
find $CACHE_DIR -type f -mtime +30 -exec rm {} \;

```
**Cron Job example**
```sh
# Edit the crontab
crontab -e

# Add a line to run the cleanup script daily at midnight
0 0 * * * /path/to/cleanup-script.sh

```
3. **Backup Cache**: If needed, you can create backups of the cache directory before deleting old files.

**Backup script example**
```sh
#!/bin/bash

# Define cache directory and backup location
CACHE_DIR="/etc/nginx/cache"
BACKUP_DIR="/path/to/backup"

# Create a timestamp
TIMESTAMP=$(date +%F-%T)

# Backup the cache directory
tar -czf $BACKUP_DIR/cache-backup-$TIMESTAMP.tar.gz $CACHE_DIR

```

4. **Optimize Cache Settings**: `Adjust max_size`, `inactive`, and `levels` based on your application’s needs and traffic patterns.

- `max_size`: Controls the maximum size of the cache.
- `inactive`: Determines how long cached items are kept if not accessed.
- `levels`: Controls directory structure for cached files.