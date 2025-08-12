# School Management System - Deployment Guide

Complete guide for deploying the School Management System to your own server.

## 🎯 Deployment Options

### Option 1: VPS/Cloud Server (Recommended)
- **Providers**: DigitalOcean, AWS EC2, Google Cloud, Linode, Vultr
- **OS**: Ubuntu 20.04+ or CentOS 8+
- **Resources**: 2GB RAM, 2 CPU cores, 20GB storage minimum

### Option 2: Shared Hosting
- **Requirements**: Node.js and Python support
- **Limitations**: May have restrictions on long-running processes

### Option 3: Docker Deployment
- **Benefits**: Containerized, easy scaling, consistent environments
- **Requirements**: Docker and Docker Compose

## 🖥 VPS Server Deployment (Detailed Guide)

### 1. Server Setup

#### Initial Server Configuration
```bash
# Update system packages
sudo apt update && sudo apt upgrade -y

# Install essential packages
sudo apt install -y curl wget git unzip software-properties-common

# Create application user
sudo adduser schoolapp
sudo usermod -aG sudo schoolapp
su - schoolapp
```

#### Install Node.js
```bash
# Install Node.js 18.x
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# Verify installation
node --version
npm --version
```

#### Install Python and Poetry
```bash
# Install Python 3.10+
sudo apt install -y python3 python3-pip python3-venv

# Install Poetry
curl -sSL https://install.python-poetry.org | python3 -
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

# Verify installation
poetry --version
```

#### Install and Configure Nginx
```bash
# Install Nginx
sudo apt install -y nginx

# Start and enable Nginx
sudo systemctl start nginx
sudo systemctl enable nginx

# Check status
sudo systemctl status nginx
```

#### Install PM2 (Process Manager)
```bash
# Install PM2 globally
sudo npm install -g pm2

# Setup PM2 startup script
pm2 startup
sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u schoolapp --hp /home/schoolapp
```

### 2. Deploy Application Files

#### Upload Project Files
```bash
# Create application directory
mkdir -p /home/schoolapp/school-management-system
cd /home/schoolapp/school-management-system

# Option 1: Upload via SCP
# From your local machine:
scp -r school-management-system/ schoolapp@your-server-ip:/home/schoolapp/

# Option 2: Clone from Git (if using version control)
git clone https://github.com/yourusername/school-management-system.git .
```

#### Set Proper Permissions
```bash
# Set ownership
sudo chown -R schoolapp:schoolapp /home/schoolapp/school-management-system

# Set permissions
chmod -R 755 /home/schoolapp/school-management-system
```

### 3. Backend Deployment

#### Setup Backend Environment
```bash
cd /home/schoolapp/school-management-system/school-backend

# Install dependencies
poetry install --no-dev

# Create production environment file
cp .env.example .env
```

#### Configure Production Environment
Edit `/home/schoolapp/school-management-system/school-backend/.env`:
```env
# MongoDB Configuration
MONGODB_URL=mongodb+srv://schoolm:Kallesh717%40@cluster0.fhcvqur.mongodb.net/school_management?retryWrites=true&w=majority&appName=Cluster0

# JWT Configuration
JWT_SECRET_KEY=your-super-secure-production-jwt-secret-key-here
JWT_ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30

# SMS Configuration (Optional)
FAST2SMS_API_KEY=your-fast2sms-api-key

# Production Settings
ENVIRONMENT=production
DEBUG=false
```

#### Create PM2 Configuration
Create `/home/schoolapp/school-management-system/ecosystem.config.js`:
```javascript
module.exports = {
  apps: [
    {
      name: 'school-backend',
      cwd: '/home/schoolapp/school-management-system/school-backend',
      script: 'poetry',
      args: 'run uvicorn app.main:app --host 0.0.0.0 --port 8000',
      instances: 1,
      autorestart: true,
      watch: false,
      max_memory_restart: '1G',
      env: {
        NODE_ENV: 'production'
      },
      error_file: '/home/schoolapp/logs/backend-error.log',
      out_file: '/home/schoolapp/logs/backend-out.log',
      log_file: '/home/schoolapp/logs/backend-combined.log'
    }
  ]
};
```

#### Start Backend Service
```bash
# Create logs directory
mkdir -p /home/schoolapp/logs

# Start backend with PM2
cd /home/schoolapp/school-management-system
pm2 start ecosystem.config.js

# Save PM2 configuration
pm2 save

# Check status
pm2 status
pm2 logs school-backend
```

### 4. Frontend Deployment

#### Build Frontend
```bash
cd /home/schoolapp/school-management-system/school-frontend

# Install dependencies
npm install

# Create production environment
echo "VITE_API_URL=https://yourdomain.com/api" > .env

# Build for production
npm run build
```

#### Setup Nginx for Frontend
Create `/etc/nginx/sites-available/school-management`:
```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    
    # Frontend files
    root /home/schoolapp/school-management-system/school-frontend/dist;
    index index.html;
    
    # Handle React Router
    location / {
        try_files $uri $uri/ /index.html;
    }
    
    # API proxy to backend
    location /api {
        proxy_pass http://localhost:8000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
    
    # Static files caching
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
    
    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header Referrer-Policy "no-referrer-when-downgrade" always;
    add_header Content-Security-Policy "default-src 'self' http: https: data: blob: 'unsafe-inline'" always;
}
```

#### Enable Nginx Site
```bash
# Enable site
sudo ln -s /etc/nginx/sites-available/school-management /etc/nginx/sites-enabled/

# Remove default site
sudo rm /etc/nginx/sites-enabled/default

# Test Nginx configuration
sudo nginx -t

# Restart Nginx
sudo systemctl restart nginx
```

### 5. SSL Certificate Setup (Let's Encrypt)

#### Install Certbot
```bash
# Install Certbot
sudo apt install -y certbot python3-certbot-nginx

# Obtain SSL certificate
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com

# Test automatic renewal
sudo certbot renew --dry-run
```

### 6. Database Setup

#### MongoDB Atlas (Recommended)
1. **Create Account**: Visit `https://cloud.mongodb.com`
2. **Create Cluster**: Choose free tier or paid plan
3. **Create Database User**: With read/write permissions
4. **Whitelist IP**: Add your server's IP address
5. **Get Connection String**: Update in backend `.env` file

## 🐳 Docker Deployment

### 1. Create Docker Files

#### Backend Dockerfile
Create `school-backend/Dockerfile`:
```dockerfile
FROM python:3.11-slim

WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    gcc \
    && rm -rf /var/lib/apt/lists/*

# Install Poetry
RUN pip install poetry

# Copy dependency files
COPY pyproject.toml poetry.lock ./

# Configure poetry
RUN poetry config virtualenvs.create false \
    && poetry install --no-dev --no-interaction --no-ansi

# Copy application code
COPY . .

# Expose port
EXPOSE 8000

# Run application
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

#### Frontend Dockerfile
Create `school-frontend/Dockerfile`:
```dockerfile
# Build stage
FROM node:18-alpine as build

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

COPY . .
RUN npm run build

# Production stage
FROM nginx:alpine

# Copy built files
COPY --from=build /app/dist /usr/share/nginx/html

# Copy nginx configuration
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

### 2. Docker Compose Configuration

Create `docker-compose.yml`:
```yaml
version: '3.8'

services:
  backend:
    build: ./school-backend
    container_name: school-backend
    ports:
      - "8000:8000"
    environment:
      - MONGODB_URL=mongodb+srv://schoolm:Kallesh717%40@cluster0.fhcvqur.mongodb.net/school_management?retryWrites=true&w=majority&appName=Cluster0
      - JWT_SECRET_KEY=your-super-secure-jwt-secret-key
      - JWT_ALGORITHM=HS256
      - ACCESS_TOKEN_EXPIRE_MINUTES=30
    restart: unless-stopped
    networks:
      - school-network

  frontend:
    build: ./school-frontend
    container_name: school-frontend
    ports:
      - "80:80"
    depends_on:
      - backend
    restart: unless-stopped
    networks:
      - school-network

networks:
  school-network:
    driver: bridge
```

### 3. Deploy with Docker

```bash
# Build and start services
docker-compose up -d

# View logs
docker-compose logs -f

# Stop services
docker-compose down

# Update and restart
docker-compose down
docker-compose build --no-cache
docker-compose up -d
```

## 🔧 Production Optimizations

### 1. Performance Optimizations

#### Backend Optimizations
```python
# In app/main.py, add production settings
from fastapi import FastAPI
from fastapi.middleware.gzip import GZipMiddleware

app = FastAPI(
    title="School Management System",
    docs_url="/docs" if settings.environment == "development" else None,
    redoc_url="/redoc" if settings.environment == "development" else None,
)

# Add compression
app.add_middleware(GZipMiddleware, minimum_size=1000)
```

### 2. Security Hardening

#### Server Security
```bash
# Configure firewall
sudo ufw allow ssh
sudo ufw allow 80
sudo ufw allow 443
sudo ufw enable

# Disable root login
sudo sed -i 's/PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config
sudo systemctl restart ssh

# Install fail2ban
sudo apt install -y fail2ban
sudo systemctl enable fail2ban
```

## 🚀 Deployment Checklist

### Pre-Deployment
- [ ] Domain name configured and pointing to server
- [ ] SSL certificate obtained and configured
- [ ] MongoDB database setup and accessible
- [ ] Environment variables configured
- [ ] Firewall rules configured
- [ ] Backup strategy planned

### Deployment Steps
- [ ] Server setup completed
- [ ] Application files uploaded
- [ ] Dependencies installed
- [ ] Backend service running
- [ ] Frontend built and served
- [ ] Nginx configured and running
- [ ] SSL certificate active
- [ ] Database connection verified

### Post-Deployment
- [ ] Application accessible via domain
- [ ] All features tested
- [ ] Performance optimized
- [ ] Monitoring setup
- [ ] Backup system active
- [ ] Documentation updated

## 🔄 Maintenance and Updates

### Regular Maintenance
```bash
# Update system packages
sudo apt update && sudo apt upgrade -y

# Update application dependencies
cd /home/schoolapp/school-management-system/school-backend
poetry update

cd /home/schoolapp/school-management-system/school-frontend
npm update

# Restart services
pm2 restart all
sudo systemctl restart nginx
```

### Backup Strategy
```bash
# Create backup script
#!/bin/bash
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/home/schoolapp/backups"

# Create backup directory
mkdir -p $BACKUP_DIR

# Backup application files
tar -czf $BACKUP_DIR/app_backup_$DATE.tar.gz /home/schoolapp/school-management-system

# Remove old backups (keep last 7 days)
find $BACKUP_DIR -name "*.tar.gz" -mtime +7 -delete
```

---

**Note**: This deployment guide provides multiple options for hosting the School Management System on your own infrastructure with complete control and customization capabilities.
