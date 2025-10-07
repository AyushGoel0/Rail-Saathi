# Rail-Saathi Setup Guide

## Table of Contents

- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Environment Configuration](#environment-configuration)
- [Database Setup](#database-setup)
- [Running the Application](#running-the-application)
- [Development Setup](#development-setup)
- [Troubleshooting](#troubleshooting)

---

## Prerequisites

Before setting up Rail-Saathi, ensure you have the following installed:

### Required Software

- **Python**: Version 3.8 or higher
  - Download from [python.org](https://www.python.org/downloads/)
  - Verify installation: `python --version`

- **pip**: Python package manager (usually comes with Python)
  - Verify installation: `pip --version`

- **Git**: For version control (optional but recommended)
  - Download from [git-scm.com](https://git-scm.com/)

### API Requirements

- **RapidAPI Account**: Required for IRCTC API access
  - Sign up at [rapidapi.com](https://rapidapi.com/)
  - Subscribe to IRCTC API
  - Get your API key from the dashboard

---

## Installation

### Step 1: Clone the Repository

```bash
# Using HTTPS
git clone https://github.com/AyushGoel0/Rail-Saathi.git

# Navigate to project directory
cd Rail-Saathi
```

### Step 2: Create Virtual Environment

It's recommended to use a virtual environment to isolate project dependencies.

**On Windows:**
```powershell
# Create virtual environment
python -m venv venv

# Activate virtual environment
.\venv\Scripts\activate
```

**On macOS/Linux:**
```bash
# Create virtual environment
python3 -m venv venv

# Activate virtual environment
source venv/bin/activate
```

You should see `(venv)` in your terminal prompt indicating the virtual environment is active.

### Step 3: Install Dependencies

```bash
# Install all required packages
pip install -r requirements.txt
```

**Key Dependencies:**
- Flask - Web framework
- Flask-SQLAlchemy - Database ORM
- Flask-WTF - Form handling and CSRF protection
- Flask-Migrate - Database migrations
- Requests - HTTP library for API calls
- Python-dotenv - Environment variable management
- Waitress - Production WSGI server

---

## Environment Configuration

### Step 1: Create .env File

Create a `.env` file in the project root directory:

```bash
# On Windows
type nul > .env

# On macOS/Linux
touch .env
```

### Step 2: Configure Environment Variables

Add the following variables to your `.env` file:

```env
# Flask Configuration
SECRET_KEY=your_secret_key_here_generate_a_random_string
FLASK_APP=app.app
FLASK_ENV=development

# Database Configuration
DATABASE_URL=sqlite:///instance/railsaathi.db

# API Configuration
RAPIDAPI_KEY=your_rapidapi_key_here
RAPIDAPI_HOST=irctc1.p.rapidapi.com

# Email Configuration (if using email features)
MAIL_SERVER=smtp.gmail.com
MAIL_PORT=587
MAIL_USE_TLS=True
MAIL_USERNAME=your_email@gmail.com
MAIL_PASSWORD=your_app_password

# Session Configuration
SESSION_TIMEOUT=1800
```

### Step 3: Generate SECRET_KEY

The SECRET_KEY is crucial for security. Generate a strong random key:

**Using Python:**
```python
import secrets
print(secrets.token_hex(32))
```

Copy the generated key and paste it as your `SECRET_KEY` in the `.env` file.

### Step 4: Get RapidAPI Key

1. Go to [RapidAPI IRCTC API](https://rapidapi.com/hub)
2. Search for "IRCTC" API
3. Subscribe to a plan (Free tier available)
4. Copy your API key from the dashboard
5. Paste it as `RAPIDAPI_KEY` in your `.env` file

---

## Database Setup

### Step 1: Initialize Database

The application uses SQLite by default. Initialize the database:

```bash
# Create instance folder if it doesn't exist
mkdir instance

# Initialize Flask-Migrate
flask db init

# Create initial migration
flask db migrate -m "Initial migration"

# Apply migration to create tables
flask db upgrade
```

### Step 2: Verify Database Creation

Check that `railsaathi.db` was created in the `instance/` folder:

```bash
# On Windows
dir instance

# On macOS/Linux
ls -la instance/
```

### Database Models

The application includes the following models:
- **User** - User authentication and profiles
- **Booking** - Train booking records
- **Train** - Train information (cached)
- **SessionConfig** - User session configurations

---

## Running the Application

### Development Mode

**Method 1: Using Flask CLI**
```bash
# Ensure virtual environment is activated
flask run

# Or specify host and port
flask run --host=0.0.0.0 --port=5000
```

**Method 2: Using Python**
```bash
python app/app.py
```

**Method 3: Using manage.py**
```bash
python manage.py
```

### Production Mode (Waitress)

For production deployment, use Waitress:

```bash
waitress-serve --host=0.0.0.0 --port=8000 app.app:app
```

### Access the Application

Once running, open your browser and navigate to:
- **Development**: `http://localhost:5000`
- **Production**: `http://localhost:8000`

You should see the Rail-Saathi homepage.

---

## Development Setup

### IDE Recommendations

**VS Code Extensions:**
- Python (Microsoft)
- Pylance
- Flask Snippets
- SQLite Viewer
- GitLens

**PyCharm:**
- Already includes excellent Flask support

### Code Style

The project follows PEP 8 style guidelines:

```bash
# Install code formatting tools (optional)
pip install black flake8 pylint

# Format code
black .

# Check style
flake8 app/
```

### Running Tests

```bash
# Install testing dependencies
pip install pytest pytest-flask

# Run tests
pytest
```

---

## Troubleshooting

### Common Issues

#### 1. ModuleNotFoundError

**Error:** `ModuleNotFoundError: No module named 'flask'`

**Solution:**
```bash
# Ensure virtual environment is activated
# Then reinstall dependencies
pip install -r requirements.txt
```

#### 2. Database Locked Error

**Error:** `sqlite3.OperationalError: database is locked`

**Solution:**
- Close any database viewers/editors
- Restart the Flask application
- Delete `instance/railsaathi.db` and recreate:
  ```bash
  rm instance/railsaathi.db
  flask db upgrade
  ```

#### 3. API Key Errors

**Error:** `401 Unauthorized` or `403 Forbidden`

**Solution:**
- Verify `RAPIDAPI_KEY` in `.env` file
- Check API subscription status on RapidAPI
- Ensure you haven't exceeded rate limits

#### 4. Template Not Found

**Error:** `TemplateNotFound: index.html`

**Solution:**
```bash
# Verify templates folder structure
ls app/templates/

# Should contain:
# - index.html
# - login.html
# - register.html
# - search.html
# etc.
```

#### 5. CSRF Token Error

**Error:** `The CSRF token is missing`

**Solution:**
- Ensure `SECRET_KEY` is set in `.env`
- Verify CSRF protection is enabled in form templates
- Clear browser cookies and try again

#### 6. Port Already in Use

**Error:** `OSError: [Errno 48] Address already in use`

**Solution:**
```bash
# On Windows
netstat -ano | findstr :5000
taskkill /PID <PID> /F

# On macOS/Linux
lsof -i :5000
kill -9 <PID>

# Or use a different port
flask run --port=5001
```

### Getting Help

- **Issues**: Report bugs on [GitHub Issues](https://github.com/AyushGoel0/Rail-Saathi/issues)
- **Documentation**: Check `/docs` folder for detailed guides
- **API Docs**: See `docs/API.md` for API integration details

---

## Next Steps

After successful setup:

1. **Explore the Application**
   - Register a new user account
   - Search for trains between stations
   - Make a test booking

2. **Customize Configuration**
   - Update styling in `app/static/css/`
   - Modify templates in `app/templates/`
   - Add new routes in `app/routes.py`

3. **Deploy to Production**
   - See `docs/DEPLOYMENT.md` for deployment instructions
   - Configure production database (PostgreSQL recommended)
   - Set up SSL/HTTPS

4. **Review Architecture**
   - Read `docs/ARCHITECTURE.md` for project structure
   - Understand the blueprint-based organization
   - Learn about the database models

---

## Additional Resources

- [Flask Documentation](https://flask.palletsprojects.com/)
- [Flask-SQLAlchemy Guide](https://flask-sqlalchemy.palletsprojects.com/)
- [Python Virtual Environments](https://docs.python.org/3/tutorial/venv.html)
- [RapidAPI Documentation](https://docs.rapidapi.com/)

---

**Last Updated**: October 2025

**Need Help?** Contact the development team or open an issue on GitHub.
