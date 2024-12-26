
## Deploying MERN Stack Project on VPS




- Preparing the VPS Environment
- Setting Up the MongoDB Database
- Deploying the Express and Node.js Backend
- Deploying the React Frontends
- Configuring Nginx as a Reverse Proxy
- Setting Up SSL Certificates
### 1. Preparing the VPS Environment

Log in to Your VPS in Terminal 

```bash
 ssh root@your_vps_ip
```

Update and Upgrade Your System

```bash
  sudo apt update
```
```bash
  sudo apt upgrade -y
```

Install Node.js and npm ( if not pre-installed)

For Node.js 20
```bash
  curl -fsSL https://deb.nodesource.com/setup_20.x | sudo bash -
```
For Node.js 18
```bash
  curl -fsSL https://deb.nodesource.com/setup_18.x | sudo bash -
```
```bash
  sudo apt-get install -y nodejs
```
Install Git 
```bash
  sudo apt install -y git
```


###  2. Setting Up the MongoDB Database

If you want to setup MongoDB on VPS Follow this Guide: [click here](https://github.com/Usamapuward/mern_deployment/blob/main/MongoDB_Setup.md)

### 3. Deploying the Express and Node.js Backend

Clone Your Frontend Repository

```bash
 mkdir /var/www
```

```bash
 cd /var/www
```
```bash
 git clone https://github.com/yourusername/your-repo.git
```



Install Dependencies

```bash
 cd your-repo/backend
```

```bash
 npm install
```
Create .env file

```bash
 nano .env
```
Cconfigure Environment Variables (for my project)

```bash
 PORT = "5000"
 projectName = "your_project_name"
 JWT_SECRET = "your_jwt_secret"
 EMAIL = "example@mail.com"
 PASSWORD = "your_password"
 BASE_URL = "https://127.0.0.1:5000/"
 SECRET_KEY = "JIICGbfFkpwq01wQOfDNGEaKAdqGjJDC"
 MONGODB_CONNECTION_STRING = "mongodb://username:password@127.0.0.1:27017/database_name"
 MARX_LKR_MERCHANT_SECRET = "your_merchant_secret"
 MARX_USD_MERCHANT_SECRET = "your_merchant_secret"
```

add environment variables then save and exit (Ctrl + X, then Y and Enter).


Installing pm2 to Start Backend

```bash
 npm install -g pm2
```
```bash
 pm2 start server.js --name project-backend
```
Start Backend on startup
```bash
 pm2 startup
```
```bash
 pm2 save
```
Allowing backend port in firewall 

```bash
 sudo ufw status
```
If firewall is disable then enable it using 
```bash
 sudo ufw enable
```
```bash
 sudo ufw allow 'OpenSSH'
```
```bash
 sudo ufw allow 5000
```

### 4. Deploying the React Frontends

Creating Build of React Applications
```bash
 cd your-repo/frontend
```
```bash
 npm install
```
If you have ".env" file in your project

Create .env file 
```bash
 nano .env
```
Cconfigure Environment Variables (for my project)
```bash
 BASE_URL="https://127.0.0.1:5000/"
```

Create build of project
```bash
 npm run build
```

Repeat for the second or mulitiple React app.

Install Nginx

```bash
 sudo apt install -y nginx
```

adding Nginx in firewall

```bash
 sudo ufw status
```
```bash
 sudo ufw allow 'Nginx Full'
```


Configure Nginx for React Frontend


```bash
 nano /etc/nginx/sites-available/yourdomain.com.conf
```

```bash
 server {
    listen 80;
    server_name yourdomain1.com www.yourdomain.com;

    location / {
        root /var/www/your-repo/frontend/dist;
        try_files $uri /index.html;
    }
}
```
Save and exit (Ctrl + X, then Y and Enter).

Create symbolic link to enable the site.

```bash
ln -s /etc/nginx/sites-available/yourdomain.com.conf /etc/nginx/sites-enabled/
```

Test the Nginx configuration for syntax errors.

```bash
nginx -t
```

```bash
systemctl restart nginx
```

### 5. Configuring Nginx as a Reverse Proxy

Update Backend Nginx Configuration

```bash
nano /etc/nginx/sites-available/api.yourdomain.com.conf
```
```bash
server {
    listen 80;
    server_name api.yourdomain.com;

    location / {
        proxy_pass http://localhost:4000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Create symbolic links to enable the sites.

```bash
ln -s /etc/nginx/sites-available/api.yourdomain.com.conf /etc/nginx/sites-enabled/
```

Restart nginx

```bash
systemctl restart nginx
```

### Connect Domain Name with Website

Point all your domain & sub-domain on VPS IP address by adding DNS records in your domain manager 

Now your website will be live on domain name

### 6. Setting Up SSL Certificates 

Install Certbot

```bash
sudo apt install -y certbot python3-certbot-nginx
```

Obtain SSL Certificates

```bash
certbot --nginx -d yourdomain1.com -d www.yourdomain.com -d api.yourdomain.com
```

Verify Auto-Renewal

```bash
certbot renew --dry-run
```
