# Rail-Saathi Architecture Documentation

## Table of Contents

- [Project Overview](#project-overview)
- [Technology Stack](#technology-stack)
- [Project Structure](#project-structure)
- [Application Architecture](#application-architecture)
- [Database Schema](#database-schema)
- [Blueprint Organization](#blueprint-organization)
- [Request Flow](#request-flow)
- [Key Components](#key-components)
- [Security Features](#security-features)
- [Design Patterns](#design-patterns)

---

## Project Overview

**Rail-Saathi** is a Flask-based web application for train ticket booking and travel planning. It integrates with the IRCTC RapidAPI to provide real-time train search, seat availability checking, and booking management.

### Core Features

- **User Authentication**: Registration, login, password recovery
- **Train Search**: Real-time search between stations
- **Seat Availability**: Live seat status checking
- **Booking Management**: Create and track bookings
- **User Dashboard**: Booking history and profile management
- **Session Management**: Secure session handling with timeout

---

## Technology Stack

### Backend

- **Framework**: Flask 3.x
- **Database ORM**: Flask-SQLAlchemy
- **Migrations**: Flask-Migrate (Alembic)
- **Forms**: Flask-WTF
- **Security**: CSRF Protection, Password Hashing
- **WSGI Server**: Waitress (Production)

### Frontend

- **HTML/CSS**: Bootstrap 5.3.3
- **Icons**: Bootstrap Icons
- **JavaScript**: Vanilla JS with Bootstrap Bundle

### APIs & Services

- **IRCTC API**: RapidAPI integration for train data
- **Payment**: Razorpay (configured)
- **AI**: OpenAI (for future features)

### Development Tools

- **Python**: 3.8+
- **Environment**: python-dotenv for configuration
- **Version Control**: Git

---

## Project Structure

```
Rail-Saathi/
│
├── app/                          # Main application package
│   ├── __init__.py              # App factory and initialization
│   ├── app.py                   # Entry point for running the app
│   ├── routes.py                # All route handlers (blueprints)
│   ├── models.py                # Database models
│   ├── forms.py                 # WTForms form definitions
│   ├── auth.py                  # Authentication utilities
│   ├── bookings.py              # Booking management logic
│   ├── trains.py                # Train API logic (to be populated)
│   ├── ai_logic.py              # AI-powered features (future)
│   │
│   ├── static/                  # Static assets
│   │   ├── css/
│   │   │   ├── style.css       # Main stylesheet
│   │   │   └── form.css        # Form-specific styles
│   │   └── images/
│   │       ├── file.png        # Logo
│   │       └── train.jpg       # Background image
│   │
│   └── templates/               # Jinja2 templates
│       ├── index.html          # Homepage
│       ├── login.html          # Login page
│       ├── register.html       # Registration page
│       ├── dashboard.html      # User dashboard
│       ├── search.html         # Train search results
│       ├── booking.html        # Booking form
│       ├── payment.html        # Payment page
│       ├── history.html        # Booking history
│       ├── contact-us.html     # Contact page
│       ├── forgot_password.html # Password recovery
│       └── error.html          # Error pages
│
├── instance/                    # Instance-specific files (not in git)
│   └── railsaathi.db           # SQLite database
│
├── migrations/                  # Alembic migrations
│   ├── alembic.ini             # Alembic configuration
│   ├── env.py                  # Migration environment
│   ├── README                  # Migration instructions
│   ├── script.py.mako          # Migration template
│   └── versions/               # Migration version files
│
├── docs/                        # Documentation
│   ├── API.md                  # API documentation
│   ├── SETUP.md                # Setup guide
│   ├── ARCHITECTURE.md         # This file
│   └── DEPLOYMENT.md           # Deployment guide
│
├── .env                         # Environment variables (not in git)
├── .gitignore                   # Git ignore rules
├── manage.py                    # Management script
├── requirements.txt             # Python dependencies
├── Procfile                     # Heroku deployment
├── render.yaml                  # Render deployment
├── run.sh                       # Shell script for running
├── API.md                       # API notes (legacy)
└── README.md                    # Project readme
```

---

## Application Architecture

### Application Factory Pattern

The application uses the **Factory Pattern** for initialization:

```python
# app/__init__.py
def create_app():
    app = Flask(__name__)
    
    # Configure app
    app.config['SQLALCHEMY_DATABASE_URI'] = os.getenv('DATABASE_URL')
    app.config['SECRET_KEY'] = os.getenv('SECRET_KEY')
    
    # Initialize extensions
    db.init_app(app)
    csrf.init_app(app)
    migrate.init_app(app, db)
    
    # Register blueprints
    from app.routes import home_bp, register_bp, login_bp, ...
    app.register_blueprint(home_bp)
    app.register_blueprint(register_bp)
    # ... other blueprints
    
    return app
```

### Blueprint-Based Modular Design

The application is organized into **blueprints** for better modularity:

| Blueprint | URL Prefix | Purpose |
|-----------|------------|---------|
| `home_bp` | `/` | Homepage and landing |
| `register_bp` | `/register` | User registration |
| `login_bp` | `/login` | User login |
| `logout_bp` | `/logout` | User logout |
| `dashboard_bp` | `/dashboard` | User dashboard |
| `search_bp` | `/search` | Train search |
| `booking_bp` | `/booking` | Booking management |
| `history_bp` | `/history` | Booking history |
| `payment_bp` | `/payment` | Payment processing |
| `contact_us_bp` | `/contact-us` | Contact form |
| `forgot_password_bp` | `/forgot-password` | Password recovery |

---

## Database Schema

### Entity Relationship Overview

```
┌─────────┐         ┌─────────────────┐         ┌────────┐
│  User   │────────>│    Booking      │────────>│ Train  │
└─────────┘    1:N  └─────────────────┘    N:1  └────────┘
     │                                               
     │ 1:N                                           
     ▼                                               
┌──────────────────┐                                
│ SessionConfig    │                                
└──────────────────┘                                
```

### Models

#### 1. User Model

```python
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    phone = db.Column(db.String(15), unique=True)
    password_hash = db.Column(db.String(200), nullable=False)
    created_at = db.Column(db.DateTime, default=datetime.utcnow)
    
    # Relationships
    bookings = db.relationship('Booking', backref='user', lazy=True)
    session_configs = db.relationship('SessionConfig', backref='user', lazy=True)
```

**Purpose**: Stores user account information and authentication credentials.

#### 2. Booking Model

```python
class Booking(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)
    train_number = db.Column(db.String(10), nullable=False)
    train_name = db.Column(db.String(100))
    from_station = db.Column(db.String(50), nullable=False)
    to_station = db.Column(db.String(50), nullable=False)
    travel_date = db.Column(db.Date, nullable=False)
    travel_class = db.Column(db.String(10), nullable=False)
    seat_count = db.Column(db.Integer, default=1)
    booking_status = db.Column(db.String(20), default='Pending')
    created_at = db.Column(db.DateTime, default=datetime.utcnow)
```

**Purpose**: Tracks train bookings made by users.

#### 3. Train Model

```python
class Train(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    train_number = db.Column(db.String(10), unique=True, nullable=False)
    train_name = db.Column(db.String(100), nullable=False)
    # Additional fields for caching train data
```

**Purpose**: Caches frequently accessed train information to reduce API calls.

#### 4. SessionConfig Model

```python
class SessionConfig(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    key = db.Column(db.String(100), nullable=False)
    value = db.Column(db.String(500))
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=True)
```

**Purpose**: Stores user-specific or global session configurations.

---

## Blueprint Organization

### Blueprint Structure

Each blueprint follows this pattern:

```python
# Create blueprint
blueprint_name = Blueprint('name', __name__, template_folder="templates")

# Define routes
@blueprint_name.route('/path', methods=['GET', 'POST'])
def route_handler():
    # Route logic
    return render_template('template.html')
```

### Route Decorators

**Authentication Required**:
```python
@require_authentication
def protected_route():
    # Only accessible when logged in
    pass
```

**Restrict Authenticated** (prevent logged-in users from accessing):
```python
@restrict_authenticated
def public_only_route():
    # Redirect to dashboard if logged in
    pass
```

---

## Request Flow

### 1. User Registration Flow

```
User fills form → POST /register → Validate input
    ↓
Hash password → Create User record → Redirect to login
```

### 2. User Login Flow

```
User submits credentials → POST /login → Validate credentials
    ↓
Check password hash → Create session → Set session vars → Redirect to dashboard
```

### 3. Train Search Flow

```
User searches trains → POST / → Redirect to /search with params
    ↓
GET /search → Fetch from IRCTC API → Filter by class
    ↓
Check seat availability → Render results in search.html
```

### 4. Booking Flow

```
User selects train → GET /booking → Pre-fill form
    ↓
User submits → POST /booking → Create Booking record
    ↓
Redirect to payment → Process payment → Update booking status
```

---

## Key Components

### 1. Session Management

**Location**: `app/__init__.py`

```python
SESSION_TIMEOUT = 1800  # 30 minutes

@app.before_request
def check_session_timeout():
    # Check last activity
    # Clear session if expired
    # Update last activity timestamp
```

**Features**:
- Automatic timeout after 30 minutes of inactivity
- Session data stored in database via `SessionConfig`
- Dynamic SECRET_KEY management

### 2. API Integration

**Location**: `app/routes.py` (to be moved to `app/trains.py`)

**Functions**:
- `fetch_live_station_data()` - Train search
- `fetch_live_seat_availability_data()` - Seat availability

**Error Handling**:
- HTTP error catching
- Retry logic (commented out, can be enabled)
- Fallback responses

### 3. Form Handling

**Location**: `app/forms.py`

Uses Flask-WTF for:
- CSRF protection
- Form validation
- Error messages

### 4. Authentication

**Location**: `app/auth.py`

**Features**:
- Password hashing with Werkzeug
- User validation by email/phone/username
- Session management

---

## Security Features

### 1. CSRF Protection

All forms include CSRF tokens:
```html
<form method="POST">
    {{ form.hidden_tag() }}  <!-- CSRF token -->
    <!-- form fields -->
</form>
```

### 2. Password Security

- Passwords hashed using `werkzeug.security`
- Never stored in plain text
- Secure password comparison

### 3. Session Security

- Secure session cookies
- HTTP-only cookies
- Session timeout enforcement
- Dynamic SECRET_KEY

### 4. Input Validation

- Server-side validation on all forms
- SQL injection prevention via ORM
- XSS protection via Jinja2 auto-escaping

---

## Design Patterns

### 1. Factory Pattern
Application creation in `create_app()`

### 2. Blueprint Pattern
Modular route organization

### 3. Repository Pattern
Database access through SQLAlchemy models

### 4. Decorator Pattern
Route protection with `@require_authentication`

### 5. Template Method Pattern
Base templates with blocks for inheritance

---

## Future Architecture Considerations

### 1. Service Layer
Move business logic from routes to dedicated service classes:
```python
# app/services/train_service.py
class TrainService:
    def search_trains(self, from_station, to_station, date):
        # Business logic here
        pass
```

### 2. Caching
Implement caching for:
- Frequently searched routes
- Station codes
- Train information

### 3. API Rate Limiting
Add rate limiting to prevent API quota exhaustion

### 4. Testing
- Unit tests for models and services
- Integration tests for routes
- API mocking for tests

### 5. Logging
Centralized logging system:
- Request logging
- Error tracking
- Performance monitoring

---

## Development Guidelines

### Adding New Features

1. **Create Model** (if needed) in `app/models.py`
2. **Create Forms** in `app/forms.py`
3. **Create Blueprint** in `app/routes.py`
4. **Create Template** in `app/templates/`
5. **Add Styles** in `app/static/css/`
6. **Register Blueprint** in `app/__init__.py`
7. **Create Migration**: `flask db migrate -m "Description"`
8. **Apply Migration**: `flask db upgrade`

### Code Organization Rules

- **Routes**: Only handle HTTP request/response
- **Models**: Database schema only
- **Business Logic**: Should be in service classes (future)
- **Templates**: Use template inheritance
- **Static Files**: Organized by type (css, js, images)

---

## Performance Considerations

### Database

- Use SQLite for development
- PostgreSQL recommended for production
- Add indexes on frequently queried fields:
  ```python
  __table_args__ = (
      db.Index('idx_user_email', 'email'),
  )
  ```

### API Calls

- Implement caching to reduce API calls
- Use background jobs for non-critical API requests
- Consider batch requests where possible

### Frontend

- Minify CSS/JS for production
- Use CDN for Bootstrap and icons
- Lazy load images

---

## Monitoring & Debugging

### Logging

Current logging setup:
```python
import logging
logging.basicConfig(level=logging.INFO)
```

**Recommendations**:
- Add file-based logging
- Use different log levels (DEBUG, INFO, WARNING, ERROR)
- Implement structured logging

### Error Tracking

- Custom error pages in `templates/error.html`
- Flash messages for user feedback
- Consider integrating Sentry for production

---

## Resources

- [Flask Documentation](https://flask.palletsprojects.com/)
- [SQLAlchemy ORM](https://docs.sqlalchemy.org/)
- [Flask-Migrate](https://flask-migrate.readthedocs.io/)
- [Blueprint Documentation](https://flask.palletsprojects.com/en/2.3.x/blueprints/)

---

**Last Updated**: October 2025

**Maintained By**: Rail-Saathi Development Team
