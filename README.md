# Set up a secure production-like web server using Nginx on Linux.

## Update package list
```
sudo apt update && sudo apt upgrade
```
## Install NGINX
```
sudo apt install nginx -y
```
## Start NGINX
```
sudo systemctl start nginx
```
## Enable NGINX to start on boot
```
sudo systemctl enable nginx
```
## Check status
```
sudo sysetmctl status nginx
```
## Install OpenSSL
```
sudo apt install openssl -y
```
## Verify installation
```
openssl version
```
## Git Clone
```
git clone :your git repositoy name
```
## Create SSL directory
```
mkdir /home/ubuntu/nginx-ssl-cert/
```
## Generate private key + Certificate for 365 days
```
sudo openssl req -x509 -nodes -days 365 \
-newkey rsa:2048 \
-keyout /home/ubuntu/nginx-ssl-cert/nginx-cert.key \
-out /home/ubuntu/nginx-ssl-cert/nginx-cert.crt
```
## Fill in details
```
You’ll be prompted for info:

Country Name → BD
State → your district
Organization → anything (e.g. your company)
Common Name → your domain or server IP
```
## Configure nginx.conf file
### nano /etc/nginx/nginx.conf
```
#how many worker should run
worker_processes 1;

events {
    worker_connections 1024;
}

http {
    include /etc/nginx/mime.types;

    #upstream block to define the application
    upstream nodejs_cluster {
        server 127.0.0.1:5173;  #configure this port based on application running port
    }

    server {
        listen 443 ssl;
        server_name localhost;

        # SSL certificate paths
        ssl_certificate /home/ubuntu/nginx-ssl-cert/nginx-cert.crt;
        ssl_certificate_key /home/ubuntu/nginx-ssl-cert/nginx-cert.key;
   
        #server location

        location / {
            proxy_pass http://nodejs_cluster;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }
    }

    server {
        listen 80;
        server_name localhost;
        
        #redirect
        location / {
            return 301 https://$host$request_uri;
        }
    }
}
```
## Reload nginx
```
sudo systemctl reload nginx
```

## Picture http redirect to https
<img width="1476" height="432" alt="Screenshot_20260419_195433" src="https://github.com/user-attachments/assets/0c79850d-3b59-4da7-9722-310cb6289a41" />

