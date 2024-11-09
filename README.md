# Go-Daddy-SSL-Configuration

sudo mkdir -p /etc/nginx/ssl

sudo openssl req -new -newkey rsa:2048 -nodes -keyout /etc/nginx/ssl/devray_co_in.key -out /etc/nginx/ssl/devray_co_in.csr

During this process, you’ll be prompted to enter information like:
Country Name: e.g., US
State or Province Name: e.g., California
Locality Name: e.g., Los Angeles
Organization Name: Your company name
Organizational Unit Name: Your department
Common Name: devray.co.in (use the domain name here)
Email Address: Your contact email


Step -1 Save the CSR content:

Display the contents of the CSR to copy and paste during the certificate order process:
bash
Copy code

cat /etc/nginx/ssl/devray_co_in.csr

Step 2: Order the SSL Certificate on GoDaddy

Log in to your GoDaddy account and navigate to SSL Certificates.
Order a new SSL certificate (or use an existing one if available).
During the setup, paste the CSR content copied from Step 1.
Complete domain validation as required by GoDaddy.
Once validated, download the issued SSL certificate files. GoDaddy will typically provide:
devray_co_in.crt: The main certificate file.
gd_bundle-g2-g1.crt: The intermediate certificate chain.

Step 3: Upload SSL Certificates to Your Server

Upload the downloaded certificate files to the /etc/nginx/ssl/ directory

sudo cp /path/to/devray_co_in.crt /etc/nginx/ssl/
sudo cp /path/to/gd_bundle-g2-g1.crt /etc/nginx/ssl/

Set permissions on the SSL files for security

sudo chmod 600 /etc/nginx/ssl/devray_co_in.key
sudo chmod 644 /etc/nginx/ssl/devray_co_in.crt
sudo chmod 644 /etc/nginx/ssl/gd_bundle-g2-g1.crt

Step 4: Configure NGINX to Use SSL
Edit your NGINX configuration for devray.co.in, typically found in /etc/nginx/sites-available/ or /etc/nginx/conf.d/

1- sudo nano /etc/nginx/sites-available/devray.co.in.conf

2- Add the following SSL configuration to the server block:

  # HTTP server block (redirects all HTTP traffic to HTTPS)
server {
    listen 80;
    server_name devray.co.in www.devray.co.in;
    
    # Redirect all requests to HTTPS
    return 301 https://$host$request_uri;
}

# HTTPS server block for SSL traffic
server {
    listen 443 ssl;
    server_name devray.co.in www.devray.co.in;

    ssl_certificate /etc/nginx/ssl/devray_co_in.crt;
    ssl_certificate_key /etc/nginx/ssl/devray_co_in.key;
    ssl_trusted_certificate /etc/nginx/ssl/gd_bundle-g2-g1.crt;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    # Update the root path to your actual project directory
    root /home/ubuntu/azura;
    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }
}

3- Save and close the configuration file


Step 5: Test and Reload NGINX

1- Check the configuration syntax to ensure there are no errors:

   sudo nginx -t

2- Reload NGINX to apply the changes:

   sudo systemctl restart nginx



**Renewal Process for SSL on devray.co.in**

Step 1: Renew the SSL Certificate on GoDaddy

Log in to your GoDaddy account and navigate to My Products > SSL Certificates.
Locate the SSL certificate for devray.co.in and select the Renew option.
GoDaddy will handle the renewal using the existing private key and CSR, so you won’t need to generate new ones.

Step 2: Download the Renewed Certificate Files

Once the renewal is processed, download the renewed certificate files from GoDaddy. The new files will include:
devray_co_in.crt (renewed certificate file).
gd_bundle-g2-g1.crt (may be unchanged but always replace with the latest).


Step 3: Replace the Old Certificates on Your Server
Upload the renewed .crt files to the same directory on your server, overwriting the old ones:

sudo cp /path/to/renewed/devray_co_in.crt /etc/nginx/ssl/
sudo cp /path/to/renewed/gd_bundle-g2-g1.crt /etc/nginx/ssl/

Ensure the permissions remain correct:

sudo chmod 644 /etc/nginx/ssl/devray_co_in.crt
sudo chmod 644 /etc/nginx/ssl/gd_bundle-g2-g1.crt


Step 4: Reload NGINX to Apply the Renewal
Reload NGINX to apply the new certificate:

sudo systemctl restart nginx





   

 




