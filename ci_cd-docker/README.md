# 🚀 FastAPI + Docker + CI/CD Demo

A hands-on project to understand **Docker** and **CI/CD pipelines** using FastAPI and GitHub Actions — with a real **AI Sentiment Analyzer**!

---

## 🎯 What This Project Does

- A real **FastAPI app** with a working AI Sentiment Analyzer endpoint
- **Docker** — packages the app so it runs identically everywhere
- **GitHub Actions CI/CD** — automatically tests and builds Docker on every push

---

## 📁 Project Structure

```
docker-demo/
├── .github/
│   └── workflows/
│       └── ci.yml          ← GitHub Actions pipeline
├── main.py                 ← FastAPI app with Sentiment Analyzer
├── test_main.py            ← pytest tests
├── Dockerfile              ← Docker instructions
└── requirements.txt        ← dependencies
```

---

## 🧠 Key Concepts Learned

### What is CI/CD?
```
You push code to GitHub
        ↓
GitHub Actions runs automatically
        ↓
❌ Tests fail → stops, tells you the bug → app stays safe
✅ Tests pass → builds Docker image → ready to deploy
```

### What is Docker?
```
WITHOUT DOCKER
Your Windows PC  →  Cloud Server (Linux) = ❌ breaks
Different Python versions, different libraries

WITH DOCKER
Pack everything into one box 📦
Your code + Python 3.11 + all libraries
Same box runs on ANY machine ✅
```

### How They Work Together
```
You write code
      ↓
git push  ← only thing YOU do
      ↓
CI/CD automatically:
  ✅ Runs tests
  ✅ Builds Docker image
  ✅ Deploys to cloud (when configured)
      ↓
🌍 Live API — zero manual work
```

---

## 🔗 API Endpoints

| Endpoint | What it does | Example |
|----------|-------------|---------|
| `GET /` | Returns welcome message | `{"message": "Welcome to Sentiment Analyzer API!"}` |
| `GET /analyze?text=your text` | Analyzes sentiment of text | `{"sentiment": "positive", "emoji": "😊"}` |

---

## 😊 Sentiment Analyzer in Action

```
Input  → "I love coding!"
Output → {"sentiment": "positive", "confidence": "95%", "emoji": "😊"}

Input  → "This is terrible!"
Output → {"sentiment": "negative", "confidence": "89%", "emoji": "😞"}

Input  → "Today is okay"
Output → {"sentiment": "neutral", "confidence": "0%", "emoji": "😐"}
```

---

## ⚙️ Files Explained

### `main.py` — FastAPI + AI Sentiment Analyzer
```python
from fastapi import FastAPI
from textblob import TextBlob

app = FastAPI()

@app.get("/")
def home():
    return {"message": "Welcome to Sentiment Analyzer API!"}

@app.get("/analyze")
def analyze(text: str):
    blob = TextBlob(text)
    polarity = blob.sentiment.polarity

    if polarity > 0:
        sentiment = "positive"
        emoji = "😊"
    elif polarity < 0:
        sentiment = "negative"
        emoji = "😞"
    else:
        sentiment = "neutral"
        emoji = "😐"

    confidence = str(round(abs(polarity) * 100)) + "%"

    return {
        "text": text,
        "sentiment": sentiment,
        "confidence": confidence,
        "emoji": emoji
    }
```

### `test_main.py` — Automated Tests
```python
from fastapi.testclient import TestClient
from main import app

client = TestClient(app)

def test_home():
    response = client.get("/")
    assert response.status_code == 200

def test_positive_sentiment():
    response = client.get("/analyze?text=I love this!")
    assert response.json()["sentiment"] == "positive"

def test_negative_sentiment():
    response = client.get("/analyze?text=I hate this!")
    assert response.json()["sentiment"] == "negative"
```

### `Dockerfile` — Docker Instructions
```dockerfile
FROM python:3.11        # use Python 3.11 as base
WORKDIR /app            # set working folder inside container
COPY requirements.txt . # copy requirements first
RUN pip install -r requirements.txt  # install all dependencies
COPY . .                # copy all your code
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### `requirements.txt` — Dependencies
```
fastapi      ← web framework
uvicorn      ← server to run FastAPI
httpx        ← for API testing
pytest       ← automated testing
textblob     ← AI library for sentiment analysis
```

### `ci.yml` — GitHub Actions Pipeline
```yaml
name: CI Pipeline - FastAPI Docker

on:
  push:
    branches: [main]

jobs:
  test-and-build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: "3.11"

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run tests
        run: pytest

      - name: Build Docker image
        run: docker build -t docker-demo .
```

---

## 💻 All Commands Reference

### Git Commands
```bash
git add .                        # stage all files
git commit -m "your message"    # save changes
git push                         # push to GitHub → triggers CI/CD
git pull                         # sync GitHub changes to your PC
git remote -v                    # check GitHub connection
git status                       # check current status
```

### Run FastAPI Locally (without Docker)
```bash
pip install -r requirements.txt  # install dependencies
uvicorn main:app --reload        # start the app
# Open → http://localhost:8000
```

### Run Tests Locally
```bash
pytest       # run all tests
pytest -v    # run with details
```

### Docker Commands
```bash
# Build Docker image
docker build --network=host -t docker-demo .

# Run the container
docker run -p 8000:8000 docker-demo

# See running containers
docker ps

# Stop a container
docker stop <container_id>
```

### Test Sentiment Analyzer in Browser
```
http://localhost:8000
http://localhost:8000/docs                              ← interactive API docs
http://localhost:8000/analyze?text=I love coding!
http://localhost:8000/analyze?text=This is awful!
http://localhost:8000/analyze?text=Today is okay
```

---

## ✅ How to Test the CI/CD Pipeline

### Break it — see red ❌
```python
# In test_main.py — change to wrong value
def test_positive_sentiment():
    assert response.json()["sentiment"] == "negative"  # wrong!
```
```bash
git add . && git commit -m "break test" && git push
# Actions tab → ❌ red failure → Docker never builds
```

### Fix it — see green ✅
```python
def test_positive_sentiment():
    assert response.json()["sentiment"] == "positive"  # correct!
```
```bash
git add . && git commit -m "fix test" && git push
# Actions tab → ✅ green success → Docker builds
```

---

## 🔄 What Happens on Every Push

```
git push
   ↓
GitHub Actions starts automatically
   ↓
✅ Checkout code
✅ Setup Python 3.11
✅ Install dependencies (fastapi, textblob, pytest...)
✅ Run pytest (3 tests)
   ↓
❌ Any test fails → pipeline stops → Docker never builds → app stays safe
✅ All tests pass → Docker image builds → ready for deployment
```

---

## 🧱 What's Next

| Step | What to learn | Tool |
|------|--------------|------|
| ✅ Done | CI + Docker build | GitHub Actions + Docker |
| 🔜 Next | Auto deploy to cloud | Render / Railway |
| 🔜 Later | Secrets management | GitHub Secrets |
| 🔜 Later | Real AI model API | HuggingFace / OpenAI |

---

## 🛠️ Tech Used

- **Python** — programming language
- **FastAPI** — modern Python web framework for building APIs
- **TextBlob** — AI library for sentiment analysis
- **pytest** — automated testing
- **Docker** — containerization
- **GitHub Actions** — CI/CD automation

---

## 💡 Real World Value

| Without CI/CD + Docker | With CI/CD + Docker |
|-----------------------|---------------------|
| Manual testing every time | Automatic on every push |
| "Works on my machine" problem | Runs same everywhere |
| Bugs reach production | Bugs caught before deploy |
| Manual deployment | Auto deploy on green tests |
| Hours of setup for new team member | 5 minutes with Docker |
