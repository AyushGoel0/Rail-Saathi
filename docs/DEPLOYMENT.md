# Rail-Saathi Deployment Guide

## Table of Contents

- [Prerequisites](#prerequisites)
- [Environment Variables](#environment-variables)
- [Local Deployment](#local-deployment)
- [Render Deployment](#render-deployment)
- [Heroku Deployment](#heroku-deployment)
- [VPS Deployment](#vps-deployment)
- [Database Migration](#database-migration)
- [Post-Deployment](#post-deployment)
- [Troubleshooting](#troubleshooting)

---

## Prerequisites

Before deploying Rail-Saathi, ensure you have:

### Required Accounts & Services

- **RapidAPI Account**: For IRCTC API access
- **Deployment Platform Account**: 
  - Render.com (recommended)
  - Heroku
  - DigitalOcean/AWS/Azure (for VPS)
- **GitHub Account**: For repository hosting
- **Domain** (Optional): For custom domain

### Required Files Check

Ensure these files exist in your repository:
- `requirements.txt` - Python dependencies
- `Procfile` - Process configuration (Heroku)
- `render.yaml` - Render configuration
- `.env.example` - Environment variable template
- `manage.py` - Management script

---

## Environment Variables

All deployment platforms require these environment variables:

### Required Variables

```bash
# Flask Configuration
SECRET_KEY=<generate-a-secure-random-key>
FLASK_APP=app.app
FLASK_ENV=production

# Database (will vary by platform)
DATABASE_URL=<database-connection-url>

# API Keys
RAPIDAPI_KEY=<your-rapidapi-key>
RAPIDAPI_HOST=irctc1.p.rapidapi.com

# Session
SESSION_TIMEOUT=1800
```

### Optional Variables

```bash
# Email Configuration (if using email features)
MAIL_SERVER=smtp.gmail.com
MAIL_PORT=587
MAIL_USE_TLS=True
MAIL_USERNAME=<your-email>
MAIL_PASSWORD=<app-password>

# Payment Gateway (if enabled)
RAZORPAY_KEY_ID=<razorpay-key>
RAZORPAY_KEY_SECRET=<razorpay-secret>

# OpenAI (for AI features)
OPENAI_API_KEY=<openai-key>
```

### Generating SECRET_KEY

```bash
python -c "import secrets; print(secrets.token_hex(32))"
```

---

## Local Deployment

### Development Server

For local development with hot reload:

```bash
# Activate virtual environment
.\venv\Scripts\activate  # Windows
source venv/bin/activate  # macOS/Linux

# Run Flask development server
flask run --host=0.0.0.0 --port=5000
```

### Production-like Local Server

For testing production configuration locally:

```bash
# Using Waitress
waitress-serve --host=127.0.0.1 --port=8000 app.app:app
```

### Access Application

- Development: http://localhost:5000
- Production: http://localhost:8000

---

## Render Deployment

### Method 1: Using render.yaml (Recommended)

#### Step 1: Prepare Repository

Ensure `render.yaml` exists in your repository root:

```yaml
services:
  - type: web
    name: rail-saathi
    env: python
    buildCommand: pip install -r requirements.txt
    startCommand: waitress-serve --host=0.0.0.0 --port=$PORT app.app:app
    envVars:
      - key: FLASK_APP
        value: app.app
      - key: FLASK_ENV
        value: production
      - key: SECRET_KEY
        generateValue: true
      - key: RAPIDAPI_KEY
        sync: false
```

#### Step 2: Create Render Service

1. Go to [Render Dashboard](https://dashboard.render.com/)
2. Click **"New +"** → **"Blueprint"**
3. Connect your GitHub repository
4. Render will detect `render.yaml` automatically
5. Click **"Apply"**

#### Step 3: Configure Environment Variables

1. Go to your service dashboard
2. Navigate to **"Environment"** tab
3. Add required variables:
   - `RAPIDAPI_KEY`
   - `DATABASE_URL` (if using external DB)
   - Any other custom variables

#### Step 4: Deploy

Render will automatically:
- Install dependencies
- Run migrations
- Start the application

### Method 2: Manual Web Service

#### Step 1: Create New Web Service

1. Click **"New +"** → **"Web Service"**
2. Connect GitHub repository
3. Configure settings:
   - **Name**: rail-saathi
   - **Environment**: Python 3
   - **Branch**: main
   - **Build Command**: `pip install -r requirements.txt`
   - **Start Command**: `waitress-serve --host=0.0.0.0 --port=$PORT app.app:app`

#### Step 2: Add Environment Variables

Add all required environment variables in the **Environment** section.

#### Step 3: Create Database (Optional)

If using PostgreSQL:
1. Click **"New +"** → **"PostgreSQL"**
2. Name it `rail-saathi-db`
3. Copy the **Internal Database URL**
4. Add as `DATABASE_URL` in web service environment variables

#### Step 4: Deploy

Click **"Create Web Service"** and wait for deployment to complete.

### Render-Specific Configuration

**Free Tier Limitations**:
- Services spin down after 15 minutes of inactivity
- 750 hours/month of running time
- Slower cold starts

**Upgrading**:
- Consider paid plans for production use
- Eliminates spin-down
- Faster performance

---

## Heroku Deployment

### Step 1: Install Heroku CLI

```bash
# Download from https://devcenter.heroku.com/articles/heroku-cli

# Verify installation
heroku --version
```

### Step 2: Login to Heroku

```bash
heroku login
```

### Step 3: Create Heroku App

```bash
# Create app
heroku create rail-saathi-app

# Or with custom name
heroku create your-custom-name
```

### Step 4: Add PostgreSQL Database

```bash
heroku addons:create heroku-postgresql:essential-0
```

### Step 5: Configure Environment Variables

```bash
# Set SECRET_KEY
heroku config:set SECRET_KEY=$(python -c "import secrets; print(secrets.token_hex(32))")

# Set other variables
heroku config:set FLASK_APP=app.app
heroku config:set FLASK_ENV=production
heroku config:set RAPIDAPI_KEY=your_actual_key
```

### Step 6: Ensure Procfile Exists

Your `Procfile` should contain:

```
web: waitress-serve --host=0.0.0.0 --port=$PORT app.app:app
```

### Step 7: Deploy to Heroku

```bash
# Add Heroku remote (if not added automatically)
heroku git:remote -a rail-saathi-app

# Push to Heroku
git push heroku main

# Run migrations
heroku run flask db upgrade

# Open application
heroku open
```

### Managing Heroku App

```bash
# View logs
heroku logs --tail

# Restart application
heroku restart

# Run commands
heroku run flask shell

# Scale dynos
heroku ps:scale web=1
```

---

## VPS Deployment

### Using Ubuntu Server

#### Step 1: Server Setup

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Python and dependencies
sudo apt install python3 python3-pip python3-venv nginx -y

# Create application user
sudo useradd -m -s /bin/bash railsaathi
sudo su - railsaathi
```

#### Step 2: Clone Repository

```bash
# Clone repository
git clone https://github.com/AyushGoel0/Rail-Saathi.git
cd Rail-Saathi

# Create virtual environment
python3 -m venv venv
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt
```

#### Step 3: Configure Environment

```bash
# Create .env file
nano .env

# Add all required environment variables
# Save and exit (Ctrl+X, Y, Enter)
```

#### Step 4: Setup Gunicorn

```bash
# Install Gunicorn
pip install gunicorn

# Create systemd service file
sudo nano /etc/systemd/system/railsaathi.service
```

Add the following content:

```ini
[Unit]
Description=Rail-Saathi Flask Application
After=network.target

[Service]
User=railsaathi
WorkingDirectory=/home/railsaathi/Rail-Saathi
Environment="PATH=/home/railsaathi/Rail-Saathi/venv/bin"
ExecStart=/home/railsaathi/Rail-Saathi/venv/bin/gunicorn --workers 3 --bind 127.0.0.1:8000 app.app:app

[Install]
WantedBy=multi-user.target
```

```bash
# Start and enable service
sudo systemctl start railsaathi
sudo systemctl enable railsaathi
sudo systemctl status railsaathi
```

#### Step 5: Configure Nginx

```bash
# Create Nginx configuration
sudo nano /etc/nginx/sites-available/railsaathi
```

Add the following content:

```nginx
server {
    listen 80;
    server_name your-domain.com www.your-domain.com;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /static {
        alias /home/railsaathi/Rail-Saathi/app/static;
    }
}
```

```bash
# Enable site
sudo ln -s /etc/nginx/sites-available/railsaathi /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

#### Step 6: Setup SSL (Optional but Recommended)

```bash
# Install Certbot
sudo apt install certbot python3-certbot-nginx -y

# Get SSL certificate
sudo certbot --nginx -d your-domain.com -d www.your-domain.com
```

---

## Database Migration

### For All Platforms

After deploying, run database migrations:

**Render/Heroku:**
```bash
# Using platform CLI
heroku run flask db upgrade
render run flask db upgrade
```

**VPS:**
```bash
# SSH into server
cd /home/railsaathi/Rail-Saathi
source venv/bin/activate
flask db upgrade
```

### Creating New Migrations

When you modify models:

```bash
# Create migration
flask db migrate -m "Description of changes"

# Review generated migration file
# Then deploy and run upgrade
```

---

## Post-Deployment

### Verification Checklist

- [ ] Application loads successfully
- [ ] Database migrations completed
- [ ] Static files loading correctly
- [ ] Train search functionality works
- [ ] User registration/login works
- [ ] Booking flow works
- [ ] API calls successful
- [ ] No console errors
- [ ] SSL/HTTPS enabled (production)

### Health Check Endpoint

Consider adding a health check:

```python
@app.route('/health')
def health():
    return {'status': 'healthy', 'timestamp': datetime.utcnow().isoformat()}
```

### Monitoring

**Application Monitoring:**
- Set up logging aggregation (Papertrail, Loggly)
- Monitor error rates
- Track API usage and rate limits

**Server Monitoring (VPS):**
```bash
# Install monitoring tools
sudo apt install htop iotop nethogs -y

# Monitor logs
sudo journalctl -u railsaathi -f
```

---

## Troubleshooting

### Common Issues

#### 1. Application Won't Start

**Symptoms**: 502 Bad Gateway or application crash

**Solutions**:
- Check logs: `heroku logs --tail` or `journalctl -u railsaathi -f`
- Verify all environment variables are set
- Ensure `requirements.txt` is up to date
- Check Python version compatibility

#### 2. Database Connection Errors

**Symptoms**: Unable to connect to database

**Solutions**:
- Verify `DATABASE_URL` is correct
- Check database service is running
- Ensure firewall allows database connections
- Run migrations: `flask db upgrade`

#### 3. Static Files Not Loading

**Symptoms**: Missing CSS/images

**Solutions**:
- Check static file paths in templates
- Verify Nginx configuration (VPS)
- Ensure `FLASK_ENV=production`
- Check file permissions

#### 4. API Errors

**Symptoms**: Train search fails

**Solutions**:
- Verify `RAPIDAPI_KEY` is correct
- Check API subscription status
- Monitor rate limits
- Review API logs

#### 5. CSRF Token Errors

**Symptoms**: Forms submission fails

**Solutions**:
- Ensure `SECRET_KEY` is set and consistent
- Check CSRF protection is enabled
- Verify forms include `{{ form.hidden_tag() }}`

### Getting Help

- **Render Support**: https://render.com/docs
- **Heroku Support**: https://help.heroku.com/
- **GitHub Issues**: Report bugs on the repository
- **Documentation**: Check `/docs` folder

---

## Scaling Considerations

### Horizontal Scaling

**Render/Heroku:**
- Increase number of instances
- Use load balancer

**VPS:**
- Add more application servers
- Configure Nginx as load balancer

### Database Scaling

- Move from SQLite to PostgreSQL (required for production)
- Enable connection pooling
- Add read replicas for high traffic

### Caching

Implement caching to reduce database and API load:
- Redis for session storage
- Cache frequently accessed routes
- API response caching

---

## Backup Strategy

### Database Backups

**Automated (Render/Heroku):**
- Use platform's automatic backup features
- Schedule regular backups

**Manual (VPS):**
```bash
# PostgreSQL backup
pg_dump railsaathi > backup_$(date +%Y%m%d).sql

# Automate with cron
0 2 * * * pg_dump railsaathi > /backups/railsaathi_$(date +\%Y\%m\%d).sql
```

### Code Backups

- Keep code in GitHub (already done)
- Tag releases: `git tag v1.0.0`
- Maintain changelog

---

## Security Checklist

- [ ] HTTPS enabled (SSL certificate)
- [ ] Environment variables secured
- [ ] Debug mode disabled (`FLASK_ENV=production`)
- [ ] Database credentials secured
- [ ] API keys not in code
- [ ] CSRF protection enabled
- [ ] Password hashing implemented
- [ ] Input validation on all forms
- [ ] Rate limiting configured
- [ ] Regular security updates

---

## Maintenance

### Regular Tasks

**Weekly:**
- Review application logs
- Monitor API usage
- Check error rates

**Monthly:**
- Update dependencies
- Review database performance
- Backup verification
- Security patches

**Quarterly:**
- Performance optimization
- Code refactoring
- Feature updates
- User feedback review

---

## Additional Resources

- [Render Documentation](https://render.com/docs)
- [Heroku Python Guide](https://devcenter.heroku.com/articles/getting-started-with-python)
- [Flask Deployment Options](https://flask.palletsprojects.com/en/2.3.x/deploying/)
- [Nginx Configuration Guide](https://nginx.org/en/docs/)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)

---

**Last Updated**: October 2025

**Deployment Support**: For deployment issues, create an issue on GitHub or contact the development team.
