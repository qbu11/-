# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**真心话大冒险生成器** - A Truth or Dare generator web application that creates engaging party game questions using AI models. The project combines a Python Flask backend with HTML/CSS/JavaScript frontend to provide an entertaining social gaming experience with animated result presentation.

## Architecture

### Frontend
- **Technology**: HTML + CSS + JavaScript (with Anime.js for animations)
- **Core Components**:
  - Mode selection interface (Truth/Dare/Mixed)
  - Optional scene context input
  - Interactive animation area (spinning wheel or card flip)
  - Result display section
  - Generate/Try Again buttons

### Backend
- **Technology**: Flask (Python web framework)
- **API Endpoints**:
  - `/generate` - Accepts mode and scene parameters, returns AI-generated question
  - Integration with AI models (OpenAI API or similar) for content generation

## Development Commands

```bash
# Setup virtual environment
python -m venv venv
venv\Scripts\activate  # On Windows
# source venv/bin/activate  # On macOS/Linux

# Install dependencies
pip install flask flask-cors openai python-dotenv
pip install -r requirements.txt  # when created

# Environment setup
# Create .env file with your OpenAI API key:
# OPENAI_API_KEY=your_api_key_here

# Run development server
python app.py

# Run with debug mode (auto-reload on changes)
set FLASK_ENV=development  # On Windows
# export FLASK_ENV=development  # On macOS/Linux
python app.py

# Test the application
# (Testing framework to be determined)
```

## Auto-Debugging & Development Tools

### Flask Debug Mode
- **Enable debug mode** in `app.py`: `app.run(debug=True)`
- **Auto-reload**: Code changes automatically restart the server
- **Error pages**: Detailed error information with stack traces
- **Interactive debugger**: Click on stack trace lines for interactive debugging

### Frontend Debugging
```bash
# Serve static files locally for testing
python -m http.server 8000  # Navigate to localhost:8000

# Check JavaScript console for errors
# Use browser developer tools (F12)
```

### API Testing & Debugging
```bash
# Test generate API endpoint
curl -X POST http://localhost:8080/generate \
  -H "Content-Type: application/json" \
  -d '{"mode": "truth", "scene": "dorm"}'

# Or use Python requests for testing
python -c "
import requests
response = requests.post('http://localhost:8080/generate',
                        json={'mode': 'dare', 'scene': 'party'})
print(response.json())
"
```

### Common Debugging Scenarios

1. **CORS Issues**: Add `flask-cors` and enable CORS in Flask app
2. **API Key Issues**: Check .env file configuration and OpenAI API key validity
3. **JSON Parsing**: Check request content-type and data format
4. **Static File Loading**: Verify Flask static folder configuration
5. **Animation Timing**: Use browser dev tools performance tab
6. **Mobile Testing**: Use browser device simulation or ngrok for real device testing
7. **AI Model Response**: Monitor API rate limits and response formatting

### Debugging Commands
```bash
# Check if Flask app is running
curl http://localhost:8080/

# View Flask logs with timestamps
python app.py 2>&1 | while read line; do echo "$(date): $line"; done

# Test frontend without backend (mock data)
# Add mock responses in main.js for standalone testing

# Check OpenAI API connectivity
python -c "
import openai
from dotenv import load_dotenv
import os
load_dotenv()
client = openai.OpenAI(api_key=os.getenv('OPENAI_API_KEY'))
print('API connection test...')
"
```

## Key Features to Implement

1. **Mode Selection System** - User can choose between Truth, Dare, or Mixed mode
2. **Scene Context Input** - Optional scene keyword input (dorm, bar, office team building)
3. **AI Question Generation** - Backend integration with OpenAI API for creative content
4. **Interactive Animation** - Spinning wheel or card flip animation for engaging presentation
5. **Result Display** - Clear question presentation with "Try Again" functionality
6. **Optional Enhancements**:
   - Difficulty levels (Normal/Challenge/Extreme)
   - Question history storage
   - Social sharing functionality

## File Structure (Planned)

```
/
├── app.py              # Flask backend main file
├── .env                # Environment variables (API keys)
├── static/
│   ├── css/
│   │   └── style.css   # Main stylesheet
│   ├── js/
│   │   └── main.js     # Frontend logic and animations
│   └── assets/         # Images, fonts, etc.
├── templates/
│   └── index.html      # Main page template
├── requirements.txt    # Python dependencies
├── PRD.md             # Product requirements document
└── CLAUDE.md          # This development guide
```

## UI Flow

1. User selects mode (Truth/Dare/Mixed) → optionally enters scene context
2. User clicks "Generate" button → frontend calls `/generate` API
3. Animation plays (spinning wheel or card flip)
4. AI-generated question displays with "Try Again" option

## AI Integration Notes

### Prompt Design
- Base prompt template for consistent AI responses
- Mode-specific prompts for Truth vs Dare questions
- Scene context integration for appropriate content
- Content safety filters to avoid inappropriate material

### API Configuration
```python
# Sample prompt structure
prompt = f"""
You are a party game host. Generate a {mode} question for a {scene} setting.
Requirements:
- Keep it fun and entertaining
- Avoid offensive or inappropriate content
- Return only the question, no extra explanation

Mode: {mode}
Scene: {scene}
"""
```

## Development Notes

- Focus on smooth animations and user experience
- Ensure mobile-responsive design
- Keep the interface simple and intuitive (zero learning curve)
- Response time targets: <3s for AI-generated questions
- Implement proper error handling for API failures
- Add loading states during question generation

## Deployment

### Production Server
- **Target Server**: 192.144.142.60
- **Deployment Method**: Manual deployment or automated CI/CD

### Deployment Commands
```bash
# Connect to production server
ssh user@192.144.142.60

# Clone/update repository
git clone https://github.com/username/truth-dare-generator.git
cd truth-dare-generator

# Setup production environment
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# Configure environment variables
cp .env.example .env
# Edit .env with production API keys and settings

# Run production server
# Option 1: Direct Flask (development)
python app.py

# Option 2: Using Gunicorn (recommended for production)
pip install gunicorn
gunicorn -w 4 -b 0.0.0.0:8080 app:app

# Option 3: Using systemd service (persistent)
sudo systemctl start truth-dare-app
sudo systemctl enable truth-dare-app
```

### Production Considerations
- Configure firewall to allow access on port 8080 (or configured port)
- Set up reverse proxy with Nginx if needed
- Configure SSL/HTTPS for secure API key transmission
- Monitor server logs and API usage
- Implement rate limiting to prevent API abuse
- Set up automated backups if storing user data

### Server Configuration Example
```nginx
# Nginx reverse proxy configuration
server {
    listen 80;
    server_name 192.144.142.60;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```