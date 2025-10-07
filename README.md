# RailSaathi 🚂 - AI-Powered Railway Assistance System

[![Python](https://img.shields.io/badge/Python-3.8%2B-blue)](https://www.python.org/)
[![Flask](https://img.shields.io/badge/Flask-3.x-green)](https://flask.palletsprojects.com/)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

## 📋 Project Overview

**RailSaathi** is an AI-powered railway assistance system that simplifies train ticket booking for Indian Railways. It integrates with the IRCTC RapidAPI to provide real-time train search, seat availability checking, and booking management. Built with Flask, SQLAlchemy, and modern web technologies, RailSaathi delivers a seamless booking experience.

---

## ✨ Features

- 🔐 **User Authentication**: Secure registration, login, and password recovery
- 🔍 **Real-Time Train Search**: Search trains between stations with live data
- 💺 **Seat Availability**: Check live seat status for all classes
- 📊 **User Dashboard**: Manage bookings and view travel history
- 💳 **Payment Integration**: Razorpay payment gateway support
- 🎨 **Responsive Design**: Mobile-friendly Bootstrap 5 interface
- ⚡ **Session Management**: Secure sessions with automatic timeout
- 🔒 **CSRF Protection**: Form security with Flask-WTF

---

## 🛠️ Technologies Used

### Backend
- **Framework**: Flask 3.x
- **Database**: SQLite (Development) / PostgreSQL (Production)
- **ORM**: SQLAlchemy with Flask-SQLAlchemy
- **Migrations**: Flask-Migrate (Alembic)
- **Forms**: Flask-WTF
- **WSGI Server**: Waitress

### Frontend
- **UI Framework**: Bootstrap 5.3.3
- **Icons**: Bootstrap Icons
- **Templating**: Jinja2

### APIs & Integration
- **Train Data**: IRCTC RapidAPI
- **Payment**: Razorpay
- **AI Features**: OpenAI (planned)

---

## 📚 Documentation

Comprehensive documentation is available in the `/docs` folder:

- **[Setup Guide](docs/SETUP.md)** - Installation and configuration instructions
- **[API Documentation](docs/API.md)** - IRCTC API integration details and field descriptions
- **[Architecture](docs/ARCHITECTURE.md)** - Project structure and design patterns
- **[Deployment Guide](docs/DEPLOYMENT.md)** - Deploy to Render, Heroku, or VPS

---

## 🚀 Quick Start

### Prerequisites

- Python 3.8 or higher
- pip package manager
- RapidAPI account (for IRCTC API access)
- Git (optional)

### Installation

1. **Clone the repository**:
   ```bash
   git clone https://github.com/AyushGoel0/Rail-Saathi.git
   cd Rail-Saathi
   ```

2. **Create virtual environment**:
   ```bash
   # Windows
   python -m venv venv
   .\venv\Scripts\activate

   # macOS/Linux
   python3 -m venv venv
   source venv/bin/activate
   ```

3. **Install dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

4. **Configure environment variables**:
   Create a `.env` file in the project root:
   ```env
   SECRET_KEY=your_secret_key_here
   FLASK_APP=app.app
   FLASK_ENV=development
   DATABASE_URL=sqlite:///instance/railsaathi.db
   RAPIDAPI_KEY=your_rapidapi_key_here
   ```

5. **Initialize database**:
   ```bash
   flask db upgrade
   ```

6. **Run the application**:
   ```bash
   flask run
   ```

7. **Access the application**:
   Open your browser and visit `http://localhost:5000`

For detailed setup instructions, see **[docs/SETUP.md](docs/SETUP.md)**.

---

## 📁 Project Structure

```
Rail-Saathi/
├── app/                    # Main application package
│   ├── __init__.py        # App factory and initialization
│   ├── routes.py          # Blueprint routes
│   ├── models.py          # Database models
│   ├── forms.py           # WTForms definitions
│   ├── auth.py            # Authentication logic
│   ├── bookings.py        # Booking management
│   ├── trains.py          # Train API integration
│   ├── static/            # CSS, JS, images
│   └── templates/         # Jinja2 templates
├── docs/                  # Documentation
│   ├── API.md            # API documentation
│   ├── SETUP.md          # Setup guide
│   ├── ARCHITECTURE.md   # Architecture docs
│   └── DEPLOYMENT.md     # Deployment guide
├── instance/              # Instance-specific files
├── migrations/            # Database migrations
├── requirements.txt       # Python dependencies
├── manage.py             # Management script
└── README.md             # This file
```

For detailed architecture information, see **[docs/ARCHITECTURE.md](docs/ARCHITECTURE.md)**.

---

## 🌐 Deployment

RailSaathi can be deployed to various platforms:

### Render (Recommended)
```bash
# Using render.yaml blueprint
1. Connect GitHub repository
2. Render auto-detects configuration
3. Add environment variables
4. Deploy
```

### Heroku
```bash
heroku create rail-saathi
heroku addons:create heroku-postgresql:essential-0
git push heroku main
```

### VPS (Ubuntu/Linux)
```bash
# Using Gunicorn + Nginx
# See docs/DEPLOYMENT.md for complete guide
```

For complete deployment instructions, see **[docs/DEPLOYMENT.md](docs/DEPLOYMENT.md)**.

---

## 🔑 Environment Variables

Required environment variables:

| Variable | Description | Example |
|----------|-------------|---------|
| `SECRET_KEY` | Flask secret key | `abc123...` |
| `DATABASE_URL` | Database connection string | `sqlite:///railsaathi.db` |
| `RAPIDAPI_KEY` | IRCTC RapidAPI key | Your API key |
| `FLASK_ENV` | Environment mode | `development` or `production` |

See **[docs/SETUP.md#environment-configuration](docs/SETUP.md#environment-configuration)** for complete list.

---

## 📖 API Integration

RailSaathi integrates with the IRCTC RapidAPI for real-time train data:

- **Train Search**: Find trains between stations
- **Seat Availability**: Check live seat status
- **Train Information**: Departure times, duration, class types

For API documentation and field descriptions, see **[docs/API.md](docs/API.md)**.

---

## Future Work

- Real-time train updates and notifications.
- Expand the AI model for personalized recommendations.
- Add multi-language support.
- Integrate third-party APIs for food ordering and other services.

---

## Team Members

- **Ayush Goel** - Backend, Database Integration, AI Logic, Deployment
- **Meghna Singh** - Frontend UI/UX, CSS Design, Template Structure
- **Rajeev Mishra** - Frontend JavaScript & Dynamic Features
- **Azad Tiwari** - User Dashboard, Interactive Components

---

## Contributing

Contributions are welcome! Please open an issue or submit a pull request for any suggestions or improvements.

---

## License

This project is licensed under the MIT License - see the LICENSE file for details.