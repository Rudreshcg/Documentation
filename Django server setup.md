# Gunicorn Setup for Django Application


### 1. Install Gunicorn in your Virtual Environment

```bash
pip install gunicorn
```

## Create a systemd service file for Gunicorn:
```bash
sudo nano /etc/systemd/system/gunicorn.service
```

## Add the following content to the file, adjusting paths and names as necessary:

```bash
[Unit]
Description=gunicorn daemon
After=network.target

[Service]
User=ubuntu
Group=www-data
WorkingDirectory=/path/to/your/project
ExecStart=/path/to/your/virtualenv/bin/gunicorn --access-logfile - --workers 3 --bind unix:/run/gunicorn.sock your_project_name.wsgi:application

[Install]
WantedBy=multi-user.target
```
## Reload systemd and start the Gunicorn service:

```bash
sudo systemctl daemon-reload
sudo systemctl start gunicorn
```

# Install and Configure Nginx

## Install Nginx:

```bash
sudo apt-get install nginx
```

## Configure Nginx to proxy requests to Gunicorn. Edit the default Nginx configuration file:

```bash
sudo nano /etc/nginx/sites-available/default
```

## Add the following configuration, adjusting paths and names as necessary:

```bash
server {
    listen 80;
    server_name your_server_domain_or_IP;

    location = /favicon.ico { access_log off; log_not_found off; }
    location /static/ {
        root /path/to/your/static/files;
    }

    location / {
        include proxy_params;
        proxy_pass http://unix:/run/gunicorn.sock;
    }
}
```

## Enable the Nginx site configuration:
```bash
sudo ln -s /etc/nginx/sites-available/default /etc/nginx/sites-enabled/
```

## Test the Nginx configuration and reload Nginx:

```bash
sudo nginx -t
sudo systemctl reload nginx
```



