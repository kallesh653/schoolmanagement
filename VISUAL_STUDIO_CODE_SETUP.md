# Visual Studio Code Setup Guide

Complete guide to set up and run the School Management System in Visual Studio Code.

## 📋 Prerequisites

### Required Software
1. **Visual Studio Code**: Download from `https://code.visualstudio.com/`
2. **Node.js** (v16+): Download from `https://nodejs.org/`
3. **Python** (v3.8+): Download from `https://python.org/`
4. **Git**: Download from `https://git-scm.com/`

### Recommended VS Code Extensions
Install these extensions for the best development experience:

#### Essential Extensions
- **Python** (Microsoft) - Python language support
- **Pylance** (Microsoft) - Python language server
- **ES7+ React/Redux/React-Native snippets** - React code snippets
- **Prettier - Code formatter** - Code formatting
- **ESLint** - JavaScript/TypeScript linting
- **Auto Rename Tag** - HTML/JSX tag renaming
- **Bracket Pair Colorizer** - Bracket highlighting
- **GitLens** - Enhanced Git capabilities

#### Optional but Helpful
- **Thunder Client** - API testing (alternative to Postman)
- **MongoDB for VS Code** - MongoDB integration
- **Tailwind CSS IntelliSense** - TailwindCSS support
- **vscode-icons** - File icons
- **Live Server** - Static file serving

## 🚀 Project Setup in VS Code

### 1. Open Project in VS Code

#### Method 1: Using VS Code Interface
1. Open Visual Studio Code
2. Click `File` → `Open Folder`
3. Navigate to and select the `school-management-system` folder
4. Click `Select Folder`

#### Method 2: Using Command Line
```bash
# Navigate to project directory
cd path/to/school-management-system

# Open in VS Code
code .
```

### 2. Configure VS Code Workspace

#### Create Workspace Settings
Create `.vscode/settings.json` in the project root:

```json
{
  "python.defaultInterpreterPath": "./school-backend/.venv/bin/python",
  "python.terminal.activateEnvironment": true,
  "python.linting.enabled": true,
  "python.linting.pylintEnabled": true,
  "python.formatting.provider": "black",
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.organizeImports": true
  },
  "files.associations": {
    "*.jsx": "javascriptreact",
    "*.tsx": "typescriptreact"
  },
  "emmet.includeLanguages": {
    "javascript": "javascriptreact",
    "typescript": "typescriptreact"
  },
  "tailwindCSS.includeLanguages": {
    "javascript": "javascript",
    "html": "html"
  },
  "editor.quickSuggestions": {
    "strings": true
  }
}
```

#### Create Launch Configuration
Create `.vscode/launch.json`:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "FastAPI Backend",
      "type": "python",
      "request": "launch",
      "program": "${workspaceFolder}/school-backend/.venv/bin/uvicorn",
      "args": ["app.main:app", "--reload", "--host", "0.0.0.0", "--port", "8000"],
      "cwd": "${workspaceFolder}/school-backend",
      "env": {
        "PYTHONPATH": "${workspaceFolder}/school-backend"
      },
      "console": "integratedTerminal"
    },
    {
      "name": "React Frontend",
      "type": "node",
      "request": "launch",
      "program": "${workspaceFolder}/school-frontend/node_modules/.bin/vite",
      "args": ["--host", "0.0.0.0"],
      "cwd": "${workspaceFolder}/school-frontend",
      "console": "integratedTerminal"
    }
  ]
}
```

#### Create Tasks Configuration
Create `.vscode/tasks.json`:

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Install Backend Dependencies",
      "type": "shell",
      "command": "poetry",
      "args": ["install"],
      "options": {
        "cwd": "${workspaceFolder}/school-backend"
      },
      "group": "build",
      "presentation": {
        "echo": true,
        "reveal": "always",
        "focus": false,
        "panel": "shared"
      }
    },
    {
      "label": "Install Frontend Dependencies",
      "type": "shell",
      "command": "npm",
      "args": ["install"],
      "options": {
        "cwd": "${workspaceFolder}/school-frontend"
      },
      "group": "build",
      "presentation": {
        "echo": true,
        "reveal": "always",
        "focus": false,
        "panel": "shared"
      }
    },
    {
      "label": "Start Backend Server",
      "type": "shell",
      "command": "poetry",
      "args": ["run", "uvicorn", "app.main:app", "--reload", "--host", "0.0.0.0", "--port", "8000"],
      "options": {
        "cwd": "${workspaceFolder}/school-backend"
      },
      "group": "build",
      "presentation": {
        "echo": true,
        "reveal": "always",
        "focus": false,
        "panel": "shared"
      },
      "isBackground": true
    },
    {
      "label": "Start Frontend Server",
      "type": "shell",
      "command": "npm",
      "args": ["run", "dev"],
      "options": {
        "cwd": "${workspaceFolder}/school-frontend"
      },
      "group": "build",
      "presentation": {
        "echo": true,
        "reveal": "always",
        "focus": false,
        "panel": "shared"
      },
      "isBackground": true
    },
    {
      "label": "Build Frontend",
      "type": "shell",
      "command": "npm",
      "args": ["run", "build"],
      "options": {
        "cwd": "${workspaceFolder}/school-frontend"
      },
      "group": "build"
    }
  ]
}
```

## 🔧 Development Setup

### 1. Install Poetry (Python Dependency Manager)

#### Windows (PowerShell)
```powershell
(Invoke-WebRequest -Uri https://install.python-poetry.org -UseBasicParsing).Content | python -
```

#### macOS/Linux
```bash
curl -sSL https://install.python-poetry.org | python3 -
```

### 2. Setup Backend Environment

#### Using VS Code Terminal
1. Open VS Code terminal: `Ctrl+`` (backtick)
2. Navigate to backend directory:
```bash
cd school-backend
```

3. Install dependencies:
```bash
poetry install
```

4. Create environment file:
```bash
# Copy example environment file
cp .env.example .env
```

5. Edit `.env` file with your configuration:
```env
MONGODB_URL=mongodb+srv://schoolm:Kallesh717%40@cluster0.fhcvqur.mongodb.net/school_management?retryWrites=true&w=majority&appName=Cluster0
JWT_SECRET_KEY=your-super-secret-jwt-key-change-this-in-production
JWT_ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30
FAST2SMS_API_KEY=your-fast2sms-api-key
```

### 3. Setup Frontend Environment

#### Using VS Code Terminal
1. Open new terminal tab: `Ctrl+Shift+`` 
2. Navigate to frontend directory:
```bash
cd school-frontend
```

3. Install dependencies:
```bash
npm install
```

4. Create environment file:
```bash
# Create .env file
touch .env
```

5. Edit `.env` file:
```env
VITE_API_URL=http://localhost:8000
```

## 🚀 Running the Application

### Method 1: Using VS Code Tasks (Recommended)

1. **Open Command Palette**: `Ctrl+Shift+P`
2. **Type**: `Tasks: Run Task`
3. **Select**: `Start Backend Server`
4. **Repeat for Frontend**: Select `Start Frontend Server`

### Method 2: Using Integrated Terminal

#### Start Backend Server
```bash
# Terminal 1 - Backend
cd school-backend
poetry run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

#### Start Frontend Server
```bash
# Terminal 2 - Frontend
cd school-frontend
npm run dev
```

### Method 3: Using Debug Configuration

1. **Open Run and Debug**: `Ctrl+Shift+D`
2. **Select**: `FastAPI Backend` from dropdown
3. **Click**: Play button (▶️)
4. **Repeat for Frontend**: Select `React Frontend`

## 🔍 Development Workflow

### 1. File Structure Overview
```
school-management-system/
├── .vscode/                 # VS Code configuration
│   ├── settings.json       # Workspace settings
│   ├── launch.json         # Debug configurations
│   └── tasks.json          # Task definitions
├── school-backend/          # FastAPI backend
│   ├── app/
│   │   ├── routers/        # API endpoints
│   │   ├── models.py       # Data models
│   │   ├── auth.py         # Authentication
│   │   ├── database.py     # Database connection
│   │   └── main.py         # FastAPI app
│   ├── pyproject.toml      # Poetry dependencies
│   └── .env                # Environment variables
├── school-frontend/         # React frontend
│   ├── src/
│   │   ├── components/     # Reusable components
│   │   ├── pages/          # Page components
│   │   ├── contexts/       # React contexts
│   │   ├── services/       # API services
│   │   └── App.jsx         # Main app
│   ├── package.json        # Dependencies
│   └── .env                # Environment variables
└── README.md               # Documentation
```

### 2. Key Development Features

#### Code Navigation
- **Go to Definition**: `F12`
- **Find All References**: `Shift+F12`
- **Quick Open**: `Ctrl+P`
- **Command Palette**: `Ctrl+Shift+P`

#### Debugging
- **Set Breakpoints**: Click left margin or `F9`
- **Start Debugging**: `F5`
- **Step Over**: `F10`
- **Step Into**: `F11`

#### Git Integration
- **Source Control**: `Ctrl+Shift+G`
- **Stage Changes**: Click `+` next to files
- **Commit**: `Ctrl+Enter` in commit message box
- **Push/Pull**: Use status bar or command palette

### 3. Testing in VS Code

#### Backend Testing
```bash
# Run tests
cd school-backend
poetry run pytest

# Run with coverage
poetry run pytest --cov=app
```

#### Frontend Testing
```bash
# Run tests
cd school-frontend
npm test

# Run tests with coverage
npm run test:coverage
```

## 🔧 Useful VS Code Shortcuts

### General
- `Ctrl+Shift+P` - Command Palette
- `Ctrl+P` - Quick Open
- `Ctrl+`` - Toggle Terminal
- `Ctrl+Shift+`` - New Terminal
- `Ctrl+B` - Toggle Sidebar

### Editing
- `Ctrl+D` - Select Next Occurrence
- `Ctrl+Shift+L` - Select All Occurrences
- `Alt+Up/Down` - Move Line Up/Down
- `Shift+Alt+Up/Down` - Copy Line Up/Down
- `Ctrl+/` - Toggle Comment

### Navigation
- `Ctrl+G` - Go to Line
- `Ctrl+Shift+O` - Go to Symbol
- `F12` - Go to Definition
- `Alt+Left/Right` - Navigate Back/Forward

### Search and Replace
- `Ctrl+F` - Find
- `Ctrl+H` - Replace
- `Ctrl+Shift+F` - Find in Files
- `Ctrl+Shift+H` - Replace in Files

## 🐛 Troubleshooting

### Common VS Code Issues

#### Python Interpreter Not Found
1. Open Command Palette (`Ctrl+Shift+P`)
2. Type "Python: Select Interpreter"
3. Choose the Poetry virtual environment interpreter

#### ESLint/Prettier Not Working
1. Install extensions: ESLint and Prettier
2. Reload VS Code window
3. Check workspace settings in `.vscode/settings.json`

#### Terminal Not Activating Virtual Environment
1. Check Python interpreter selection
2. Restart VS Code
3. Manually activate: `poetry shell` in backend directory

#### Hot Reload Not Working
1. Check if files are being watched correctly
2. Restart development servers
3. Check for file permission issues

### Performance Optimization

#### Large Project Performance
1. Exclude unnecessary folders in settings:
```json
{
  "files.exclude": {
    "**/node_modules": true,
    "**/.git": true,
    "**/.DS_Store": true,
    "**/dist": true,
    "**/__pycache__": true
  }
}
```

2. Disable unused extensions
3. Increase VS Code memory limit if needed

## 📚 Additional Resources

### Documentation Links
- **FastAPI**: https://fastapi.tiangolo.com/
- **React**: https://reactjs.org/docs/
- **TailwindCSS**: https://tailwindcss.com/docs
- **MongoDB**: https://docs.mongodb.com/
- **Poetry**: https://python-poetry.org/docs/

### VS Code Resources
- **VS Code Documentation**: https://code.visualstudio.com/docs
- **Python in VS Code**: https://code.visualstudio.com/docs/python/python-tutorial
- **JavaScript in VS Code**: https://code.visualstudio.com/docs/languages/javascript

### Debugging Resources
- **FastAPI Debugging**: https://fastapi.tiangolo.com/tutorial/debugging/
- **React DevTools**: https://react.dev/learn/react-developer-tools

---

**Happy Coding!** 🚀

This setup provides a complete development environment for the School Management System in Visual Studio Code with all necessary configurations, shortcuts, and troubleshooting guides.
