---
id: WebServers
aliases: []
tags: []
---

## Setup Web Servers

### Apache

Apache is a popular web server software package. It is used by many large sites and companies to host their websites.

Install Apache on Ubuntu:

```bash
sudo apt-get install apache2
```

To start Apache, run:

```bash
sudo service apache2 start
```

To stop Apache, run:

```bash
sudo service apache2 stop
```

To restart Apache, run:

```bash
sudo service apache2 restart
```

To check the status of Apache, run:

```bash
sudo service apache2 status
```

To check the Apache configuration, run:

```bash
sudo apache2ctl -M
```

Apache configuration files are located in the `/etc/apache2` directory.

website configuration files are located in the `/etc/apache2/sites-available` directory.

The default website configuration file is `000-default.conf`.

To enable a website configuration file, create a symbolic link to it from the `sites-available` directory to the `sites-enabled` directory.

```bash
sudo ln -s /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-enabled/000-default.conf
```

Restart Apache to apply the changes:

```bash
sudo service apache2 restart
```

Sample website configuration file for redirecting all requests to `index.html`:

```bash
<VirtualHost *:80>
    ServerName yourwebsite.com
    DocumentRoot /var/www/yourwebsite

    <Directory /var/www/yourwebsite>
        Options -Indexes +FollowSymLinks
        AllowOverride All

        RewriteEngine On

        # Exclude existing files and directories from redirection
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d

        # Redirect all other requests to index.html
        RewriteRule ^ /index.html [L]
    </Directory>
</VirtualHost>
```

Enable the site configuration and restart Apache:

```bash
sudo a2ensite 000-default.conf
sudo service apache2 restart
```

Redirect /api requests to port 8080:

```bash
sudo a2enmod proxy
sudo a2enmod proxy_http
sudo systemctl restart apache2
```

Add the following to the end of the file `/etc/apache2/sites-available/000-default.conf`:

```bash
<VirtualHost *:80>
    ServerName yourwebsite.com
    DocumentRoot /var/www/yourwebsite

    <Directory /var/www/yourwebsite>
        Options -Indexes +FollowSymLinks
        AllowOverride All

        RewriteEngine On

        # Redirect all requests except existing files and directories to index.html
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule ^ /index.html [L]
    </Directory>

    # Redirect /api requests to port 8000 on localhost
    ProxyPass "/api" "http://localhost:8000/api"
    ProxyPassReverse "/api" "http://localhost:8000/api"
</VirtualHost>

```

Uninstall and purge Apache:

```bash
sudo systemctl stop apache2
sudo apt purge apache2 apache2-utils apache2-bin apache2.2-common
sudo apt autoremove --purge
sudo rm -rf /etc/apache2
sudo rm -rf /var/www/html
sudo rm -rf /var/log/apache2
sudo rm -rf /var/lib/apache2
```

### Nginx

Nginx is a free, open-source, high-performance HTTP server and reverse proxy, as well as an IMAP/POP3 proxy server.

Install Nginx on Ubuntu:

```bash
sudo apt-get install nginx
```

To start Nginx, run:

```bash
sudo service nginx start
```

To stop Nginx, run:

```bash
sudo service nginx stop
```

To restart Nginx, run:

```bash
sudo service nginx restart
```

To check the status of Nginx, run:

```bash
sudo service nginx status
```

To check the Nginx configuration, run:

```bash
sudo nginx -t
```

Nginx configuration files are located in the `/etc/nginx` directory.
