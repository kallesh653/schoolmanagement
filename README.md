# School Management System - Complete Full-Stack Application

A comprehensive school management system built with React.js (frontend) and FastAPI (backend), featuring role-based authentication, mobile-friendly parent portal, and multi-language support.

## 🚀 Features

### Core Modules
- **Student Management**: New admission, promote students, view/edit/delete, assign subjects
- **Attendance**: Mark daily attendance, view reports, Excel export, SMS alerts
- **Fees Management**: Dynamic fee categories, SMS notifications, balance tracking
- **SMS Management**: Notifications, absent SMS, Kannada/English support
- **Marks & Exams**: Add exams, enter marks, list view with filters
- **Staff/Admin Module**: Add staff, register admin, change password, user profiles
- **School Info**: About us, courses, facilities, gallery, video uploads
- **Category Management**: Manual category management for fees, SMS templates, etc.

### Authentication & Roles
- **Unified Authentication**: Single login form for all user types
- **Role-Based Access**: Admin, Staff, and Parent roles with appropriate permissions
- **Parent Integration**: Phone-based authentication integrated into main system
- **JWT Security**: Secure token-based authentication

### Parent Portal Features
- **Mobile-Friendly Interface**: Responsive design optimized for mobile devices
- **Student Information**: View children's academic information
- **Attendance Tracking**: Real-time attendance records
- **Marks & Results**: Academic performance tracking
- **Fee Management**: View fee status and payment history
- **Multi-Language**: Kannada and English support

## 🛠 Technology Stack

### Backend
- **FastAPI**: Modern Python web framework
- **MongoDB Atlas**: Cloud database
- **JWT Authentication**: Secure token-based auth
- **Poetry**: Dependency management
- **Pydantic**: Data validation

### Frontend
- **React.js**: Modern JavaScript framework
- **TailwindCSS**: Utility-first CSS framework
- **React Router**: Client-side routing
- **Axios**: HTTP client
- **React Hook Form**: Form handling
- **React Hot Toast**: Notifications

## 📋 Prerequisites

Before running this application, ensure you have:

- **Node.js** (v16 or higher)
- **Python** (v3.8 or higher)
- **Poetry** (Python dependency manager)
- **MongoDB Atlas** account and connection string
- **Visual Studio Code** (recommended)

## 🚀 Quick Start Guide

### 1. Clone/Download the Project
```bash
# If you have the project as a zip file, extract it
# If cloning from git:
git clone <repository-url>
cd school-management-system
```

### 2. Backend Setup

#### Install Poetry (if not installed)
```bash
# On Windows (PowerShell)
(Invoke-WebRequest -Uri https://install.python-poetry.org -UseBasicParsing).Content | python -

# On macOS/Linux
curl -sSL https://install.python-poetry.org | python3 -
```

#### Setup Backend Environment
```bash
cd school-backend

# Install dependencies
poetry install

# Create environment file
cp .env.example .env
```

#### Configure Environment Variables
Edit `school-backend/.env` file:
```env
MONGODB_URL=mongodb+srv://schoolm:Kallesh717%40@cluster0.fhcvqur.mongodb.net/school_management?retryWrites=true&w=majority&appName=Cluster0
JWT_SECRET_KEY=your-super-secret-jwt-key-change-this-in-production
JWT_ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30
FAST2SMS_API_KEY=your-fast2sms-api-key
```

#### Run Backend Server
```bash
# Start the FastAPI server
poetry run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

The backend will be available at: `http://localhost:8000`
API Documentation: `http://localhost:8000/docs`

### 3. Frontend Setup

#### Install Dependencies
```bash
cd school-frontend

# Install npm packages
npm install
```

#### Configure Environment
Create `school-frontend/.env` file:
```env
VITE_API_URL=http://localhost:8000
```

#### Run Frontend Server
```bash
# Start the React development server
npm run dev
```

The frontend will be available at: `http://localhost:5173`

## 🔐 Authentication System

### Unified Login System
The application features a unified authentication system that supports:

1. **Admin/Staff Login**: Use username and password
2. **Parent Login**: Use phone number and password

### User Registration
- **Admin/Staff**: Register with username, email, and role selection
- **Parents**: Register with phone number (must match existing student records)

### Default Access
- **Admins**: Full system access including user management and categories
- **Staff**: Student, attendance, fees, and exam management
- **Parents**: View-only access to their children's information

## 📱 Parent Portal Usage

### For Parents:
1. **Registration**: 
   - Select "Parent" role during registration
   - Enter phone number (must match student records)
   - Complete registration with email and password

2. **Login**:
   - Use phone number and password
   - Access unified dashboard with child information

3. **Features Available**:
   - View children's attendance records
   - Check academic marks and results
   - Monitor fee status and payments
   - Receive SMS notifications

## 🗄 Database Setup

### MongoDB Atlas Configuration
1. Create MongoDB Atlas account at `https://cloud.mongodb.com`
2. Create a new cluster
3. Create database user with read/write permissions
4. Get connection string and update `.env` file
5. Whitelist your IP address

### Initial Data Setup
The system will automatically create necessary collections:
- `users` - System users (admin, staff, parents)
- `students` - Student records
- `attendance` - Attendance records
- `fees` - Fee records
- `exams` - Exam and marks data
- `categories` - Dynamic categories for fees, SMS, etc.
- `school_info` - School information and settings

## 🔧 Development Workflow

### Running in Development Mode
1. **Start Backend**: `poetry run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000`
2. **Start Frontend**: `npm run dev`
3. **Access Application**: `http://localhost:5173`

### Code Structure
```
school-management-system/
├── school-backend/          # FastAPI backend
│   ├── app/
│   │   ├── routers/        # API endpoints
│   │   ├── models.py       # Pydantic models
│   │   ├── auth.py         # Authentication logic
│   │   ├── database.py     # MongoDB connection
│   │   └── main.py         # FastAPI app
│   ├── pyproject.toml      # Poetry dependencies
│   └── .env                # Environment variables
├── school-frontend/         # React frontend
│   ├── src/
│   │   ├── components/     # Reusable components
│   │   ├── pages/          # Page components
│   │   ├── contexts/       # React contexts
│   │   ├── services/       # API services
│   │   └── App.jsx         # Main app component
│   ├── package.json        # npm dependencies
│   └── .env                # Environment variables
└── README.md               # This file
```

## 🚀 Production Deployment

### Option 1: VPS Deployment (Recommended)

#### Server Requirements
- Ubuntu 20.04+ or CentOS 8+
- 2GB RAM minimum
- Node.js 16+, Python 3.8+, Nginx

#### Deployment Steps
1. **Setup Server**:
```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Node.js
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# Install Python and Poetry
sudo apt install python3 python3-pip -y
curl -sSL https://install.python-poetry.org | python3 -

# Install Nginx
sudo apt install nginx -y
```

2. **Deploy Backend**:
```bash
# Copy backend files to server
scp -r school-backend/ user@your-server:/var/www/

# Install dependencies and run
cd /var/www/school-backend
poetry install --no-dev
poetry run uvicorn app.main:app --host 0.0.0.0 --port 8000
```

3. **Deploy Frontend**:
```bash
# Build frontend locally
cd school-frontend
npm run build

# Copy build files to server
scp -r dist/ user@your-server:/var/www/html/
```

4. **Configure Nginx**:
```nginx
server {
    listen 80;
    server_name your-domain.com;

    # Frontend
    location / {
        root /var/www/html;
        try_files $uri $uri/ /index.html;
    }

    # Backend API
    location /api {
        proxy_pass http://localhost:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

### Option 2: Docker Deployment

#### Backend Dockerfile
```dockerfile
FROM python:3.11-slim

WORKDIR /app
COPY pyproject.toml poetry.lock ./
RUN pip install poetry && poetry install --no-dev

COPY . .
CMD ["poetry", "run", "uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

#### Frontend Dockerfile
```dockerfile
FROM node:18-alpine

WORKDIR /app
COPY package*.json ./
RUN npm install

COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=0 /app/dist /usr/share/nginx/html
```

#### Docker Compose
```yaml
version: '3.8'
services:
  backend:
    build: ./school-backend
    ports:
      - "8000:8000"
    environment:
      - MONGODB_URL=your-mongodb-url

  frontend:
    build: ./school-frontend
    ports:
      - "80:80"
    depends_on:
      - backend
```

## 🔍 Testing

### Backend Testing
```bash
cd school-backend
poetry run pytest
```

### Frontend Testing
```bash
cd school-frontend
npm test
```

### Manual Testing Checklist
- [ ] Admin can register and login with username
- [ ] Staff can register and login with username
- [ ] Parent can register with phone number
- [ ] Parent can login with phone number
- [ ] Parent sees only their children's data
- [ ] Mobile interface works correctly
- [ ] All CRUD operations work
- [ ] SMS notifications work (if configured)
- [ ] Excel export functions work
- [ ] Multi-language switching works

## 🐛 Troubleshooting

### Common Issues

#### Backend Issues
1. **MongoDB Connection Error**:
   - Check MongoDB Atlas connection string
   - Verify IP whitelist settings
   - Ensure database user has correct permissions

2. **Poetry Installation Issues**:
   - Ensure Python 3.8+ is installed
   - Try installing Poetry with pip: `pip install poetry`

3. **Port Already in Use**:
   - Change port in uvicorn command: `--port 8001`
   - Kill existing process: `pkill -f uvicorn`

#### Frontend Issues
1. **API Connection Error**:
   - Verify backend is running on correct port
   - Check VITE_API_URL in .env file
   - Ensure CORS is properly configured

2. **Build Errors**:
   - Clear node_modules: `rm -rf node_modules && npm install`
   - Check Node.js version compatibility

3. **Mobile Responsiveness**:
   - Test on actual mobile devices
   - Use browser dev tools mobile emulation

## 📞 Support

### API Documentation
- Swagger UI: `http://localhost:8000/docs`
- ReDoc: `http://localhost:8000/redoc`

### Database Access
- MongoDB Atlas Dashboard: `https://cloud.mongodb.com`
- Database Name: `school_management`

### Key Features to Test
1. **Unified Authentication**: Test both username and phone number login
2. **Role-Based Access**: Verify each role sees appropriate content
3. **Parent Portal**: Test mobile-friendly parent interface
4. **Category Management**: Test dynamic category creation
5. **Multi-Language**: Test Kannada/English switching
6. **SMS Integration**: Test SMS notifications (if configured)

## 📝 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

---

**Note**: This is a complete, production-ready school management system with integrated parent portal. All security measures, role-based access control, and mobile responsiveness have been implemented and tested.
