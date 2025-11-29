# Deployment Guide: GitHub & HuggingFace Spaces

## Part 1: Push to GitHub

### Prerequisites
- A GitHub account (create one at https://github.com if you don't have one)
- Git installed on your system

### Step 1: Install Git (if not already installed)
1. Download Git from: https://git-scm.com/download/win
2. Run the installer with default settings
3. Restart your terminal/PowerShell

### Step 2: Configure Git (First time only)
Open PowerShell and run:
```powershell
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### Step 3: Create a GitHub Repository
1. Go to https://github.com/new
2. Repository name: `TDS_Project_2` (or any name you prefer)
3. Description: "LLM-based Autonomous Quiz Solver Agent"
4. Choose **Public** (required for HuggingFace Spaces)
5. **DO NOT** check "Add a README file" (we already have one)
6. Click "Create repository"

### Step 4: Initialize Git and Push
In your project directory (`C:\Users\cv\Desktop\TDS_Project_2`), run:

```powershell
# Initialize git repository
git init

# Add all files (respects .gitignore)
git add .

# Create first commit
git commit -m "Initial commit: LLM Quiz Solver Agent"

# Add your GitHub repository as remote (replace YOUR_USERNAME)
git remote add origin https://github.com/YOUR_USERNAME/TDS_Project_2.git

# Push to GitHub
git push -u origin main
```

**Note:** Replace `YOUR_USERNAME` with your actual GitHub username.

If the push fails with "main" branch, try:
```powershell
git branch -M main
git push -u origin main
```

---

## Part 2: Deploy to HuggingFace Spaces

### Step 1: Create a HuggingFace Account
1. Go to https://huggingface.co/join
2. Sign up (free account is sufficient)
3. Verify your email

### Step 2: Create a New Space
1. Go to https://huggingface.co/new-space
2. Fill in the details:
   - **Owner:** Your username
   - **Space name:** `llm-quiz-solver` (or your preferred name)
   - **License:** MIT
   - **Select SDK:** Choose **Docker**
   - **Space hardware:** CPU basic (free tier)
   - **Visibility:** Public
3. Click **"Create Space"**

### Step 3: Configure Space Secrets
1. In your new Space, click on **"Settings"** (top right)
2. Scroll down to **"Repository secrets"**
3. Add the following secrets (click "Add a secret" for each):
   - **Name:** `GOOGLE_API_KEY`, **Value:** Your Gemini API key
   - **Name:** `EMAIL`, **Value:** Your email from the form
   - **Name:** `SECRET`, **Value:** Your secret string

### Step 4: Push Your Code to HuggingFace

#### Option A: Using Git (Recommended)
In your project directory, run:

```powershell
# Add HuggingFace as a remote (replace YOUR_USERNAME and SPACE_NAME)
git remote add hf https://huggingface.co/spaces/YOUR_USERNAME/SPACE_NAME

# Push to HuggingFace
git push hf main
```

When prompted for credentials:
- **Username:** Your HuggingFace username
- **Password:** Use a HuggingFace **Access Token** (not your password)
  - Get token from: https://huggingface.co/settings/tokens
  - Click "New token" → "Write" access → Copy the token

#### Option B: Using HuggingFace Web Interface
1. In your Space, click **"Files"** tab
2. Click **"Add file"** → **"Upload files"**
3. Upload all files from your project **EXCEPT**:
   - `.venv/` folder
   - `__pycache__/` folders
   - `.env` file
   - `LLMFiles/` folder
4. Click **"Commit changes to main"**

### Step 5: Verify Deployment
1. Go to your Space URL: `https://huggingface.co/spaces/YOUR_USERNAME/SPACE_NAME`
2. Wait for the build to complete (5-10 minutes)
3. Check the **"Logs"** tab for any errors
4. Once running, you should see: `Application startup complete`

### Step 6: Test Your Deployed Space
Use curl or Postman to test:

```bash
curl -X POST https://YOUR_USERNAME-SPACE_NAME.hf.space/solve \
  -H "Content-Type: application/json" \
  -d '{
    "secret": "your_secret_string",
    "url": "https://tds-llm-analysis.s-anand.net/demo"
  }'
```

Expected response:
```json
{
  "status": "ok"
}
```

---

## Troubleshooting

### Git Issues
- **"git is not recognized"**: Restart terminal after installing Git
- **Authentication failed**: Use a Personal Access Token instead of password
  - GitHub: https://github.com/settings/tokens
  - HuggingFace: https://huggingface.co/settings/tokens

### Docker Build Issues
- Check the **"Logs"** tab in your HuggingFace Space
- Common issues:
  - Missing dependencies → Check `pyproject.toml`
  - Port mismatch → Ensure `app_port: 7860` in README.md header
  - Secrets not set → Verify in Space Settings

### Runtime Issues
- **"Missing key inputs argument"**: Secrets not configured properly
- **Timeout errors**: Increase Space hardware (may require paid tier)

---

## Quick Reference

### Important Files for Deployment
- ✅ `Dockerfile` - Container configuration
- ✅ `pyproject.toml` - Python dependencies
- ✅ `README.md` - Must have HuggingFace metadata header
- ✅ `.gitignore` - Prevents pushing unnecessary files
- ❌ `.env` - Never commit this (use Secrets instead)

### HuggingFace README Header (Already in your README.md)
```yaml
---
title: LLM Analysis Quiz Solver
emoji: 🏃
colorFrom: red
colorTo: blue
sdk: docker
pinned: false
app_port: 7860
---
```

---

## Next Steps After Deployment

1. **Test thoroughly** with different quiz URLs
2. **Monitor logs** for errors or performance issues
3. **Update README** with your Space URL
4. **Share your project** with the course instructors

Good luck! 🚀
