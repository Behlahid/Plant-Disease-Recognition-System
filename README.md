---
title: Phyto Lab
emoji: 🌿
colorFrom: green
colorTo: blue
sdk: docker
app_port: 7860
---

# Phyto Lab

![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-Web%20App-000000?style=for-the-badge&logo=flask&logoColor=white)
![TensorFlow](https://img.shields.io/badge/TensorFlow-Deep%20Learning-FF6F00?style=for-the-badge&logo=tensorflow&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Ready-2496ED?style=for-the-badge&logo=docker&logoColor=white)

**Phyto Lab is a full-stack plant disease recognition system that helps users identify plant diseases from leaf images, review diagnosis history, process images in batches, and explore disease care information through a clean Flask web interface.**

The project combines a TensorFlow image classification model with a production-minded Flask architecture: authentication, upload validation, confidence thresholds, optional AI fallback analysis, Redis-backed background jobs, Cloudinary storage support, and Docker deployment.

## Table of Contents

- [Why This Project](#why-this-project)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Configuration](#configuration)
- [Running Batch Processing](#running-batch-processing)
- [Docker Deployment](#docker-deployment)
- [Testing](#testing)
- [Model Assets](#model-assets)
- [Roadmap](#roadmap)
- [Repository Topics](#repository-topics)

## Why This Project

Plant disease detection can be slow, inconsistent, or hard to access for growers and students who need quick guidance. Phyto Lab turns a trained image model into a usable web application: users upload a plant image, receive a prediction with confidence scoring, and can save results for later review.

This is more than a model demo. It is designed like an application someone could keep improving: user accounts, scan history, batch processing, settings, internationalization, and deployment support are already part of the codebase.

## Features

- **Single image diagnosis**: Upload a leaf image and receive a disease prediction, confidence score, cause, and recommended care.
- **Batch upload workflow**: Process multiple images asynchronously with Celery and Redis.
- **User accounts**: Register, log in, and keep diagnosis history tied to each user.
- **History tracking**: Review past scans, confidence values, notes, and batch results.
- **Disease encyclopedia**: Browse known crop diseases and treatment guidance.
- **Configurable confidence thresholds**: Tune high, medium, low, and uncertain diagnosis bands.
- **AI fallback support**: Optionally use Gemini vision analysis for uncertain predictions.
- **Cloud storage support**: Optionally upload scan images to Cloudinary.
- **Multilingual UI support**: Includes locale files for English, Spanish, French, Hindi, and Marathi.
- **Security-minded upload handling**: Extension checks, content sniffing, size limits, secure filenames, login-protected diagnosis routes, and rate limiting.
- **Deployment ready**: Includes Dockerfile and Hugging Face Spaces metadata.

## Tech Stack

| Layer | Tools |
| --- | --- |
| Backend | Flask, Flask-SQLAlchemy, Flask-Login |
| ML | TensorFlow, NumPy, Pillow |
| Background Jobs | Celery, Redis |
| Storage | Local filesystem, optional Cloudinary |
| Security | Flask-Talisman, Flask-Limiter, filetype validation |
| Frontend | Jinja templates, Bootstrap assets, custom CSS and JS |
| Testing | pytest, pytest-cov |
| Deployment | Docker, Gunicorn, Hugging Face Spaces compatible config |

## Project Structure

```text
Plant-Disease-Recognition-System/
|-- app/
|   |-- assets/
|   |   |-- models/
|   |   |   `-- plant_disease_recog_model_pwp.keras
|   |   `-- plant_disease.json
|   |-- controllers/
|   |   |-- auth_controller.py
|   |   |-- batch_controller.py
|   |   |-- history_controller.py
|   |   |-- main_controller.py
|   |   |-- settings_controller.py
|   |   `-- upload_controller.py
|   |-- services/
|   |   |-- ml_service.py
|   |   |-- ml_utils.py
|   |   |-- task_service.py
|   |   `-- translation_service.py
|   |-- static/
|   |-- templates/
|   |-- config.py
|   |-- models.py
|   `-- utils.py
|-- scripts/
|   |-- debug_routes.py
|   `-- seed_diseases.py
|-- tests/
|-- Dockerfile
|-- requirements.txt
`-- run.py
```

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/Plant-Disease-Recognition-System.git
cd Plant-Disease-Recognition-System
```

### 2. Create a Virtual Environment

Windows PowerShell:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

macOS or Linux:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure Environment Variables

Windows PowerShell:

```powershell
Copy-Item .env.example .env
```

macOS or Linux:

```bash
cp .env.example .env
```

Then update `.env` with your local values.

### 5. Run the Web App

```bash
python run.py
```

Open the app at:

```text
http://127.0.0.1:5000/
```

## Configuration

The app reads configuration from environment variables using `python-dotenv`.

| Variable | Required | Default | Purpose |
| --- | --- | --- | --- |
| `FLASK_SECRET_KEY` | Recommended | Random local fallback | Persistent secret for sessions and auth |
| `DATABASE_URL` | No | `sqlite:///data.sqlite` | Database connection string |
| `MODEL_PATH` | No | `app/assets/models/plant_disease_recog_model_pwp.keras` | TensorFlow model path |
| `PLANT_DISEASE_JSON` | No | `app/assets/plant_disease.json` | Disease metadata path |
| `REDIS_URL` | For batch jobs | `redis://localhost:6379/1` | Redis URL for rate limiting/cache fallback |
| `CELERY_BROKER_URL` | For batch jobs | `redis://localhost:6379/0` | Celery broker |
| `CELERY_RESULT_BACKEND` | For batch jobs | `redis://localhost:6379/0` | Celery result backend |
| `GEMINI_API_KEY` | Optional | Empty | Enables AI fallback and enrichment |
| `CLOUD_STORAGE_ENABLED` | Optional | `False` | Enables Cloudinary uploads |
| `CLOUD_NAME` | If Cloudinary enabled | Empty | Cloudinary cloud name |
| `CLOUD_API_KEY` | If Cloudinary enabled | Empty | Cloudinary API key |
| `CLOUD_API_SECRET` | If Cloudinary enabled | Empty | Cloudinary API secret |
| `CONFIDENCE_HIGH` | No | `0.8` | High confidence threshold |
| `CONFIDENCE_MEDIUM` | No | `0.6` | Medium confidence threshold |
| `CONFIDENCE_LOW` | No | `0.4` | Low confidence threshold |

## Running Batch Processing

Batch uploads use Celery and Redis. Start Redis first, then run the Flask app and a Celery worker in separate terminals.

Start the Flask app:

```bash
python run.py
```

Start the Celery worker:

```bash
celery -A app.services.task_service.celery worker --loglevel=info
```

On Windows, use the solo pool:

```powershell
celery -A app.services.task_service.celery worker --loglevel=info --pool=solo
```

## Docker Deployment

Build the image:

```bash
docker build -t phyto-lab .
```

Run the container:

```bash
docker run --rm -p 7860:7860 phyto-lab
```

Open:

```text
http://127.0.0.1:7860/
```

The Dockerfile runs the app with Gunicorn:

```bash
gunicorn --bind 0.0.0.0:7860 --workers 1 --timeout 120 run:app
```

## Testing

Run the test suite:

```bash
python -m pytest -q
```

Run with coverage:

```bash
python -m pytest --cov=app --cov-report=term-missing
```

Current tests cover app factory behavior, basic routes, ML service singleton behavior, file validation, and settings defaults.

## Model Assets

Phyto Lab expects two local ML assets:

```text
app/assets/models/plant_disease_recog_model_pwp.keras
app/assets/plant_disease.json
```

The model file contains the trained TensorFlow/Keras classifier. The JSON file maps model classes to disease metadata such as name, cause, and cure.

If you store these files somewhere else, set:

```bash
MODEL_PATH=/absolute/path/to/model.keras
PLANT_DISEASE_JSON=/absolute/path/to/plant_disease.json
```

## Optional Disease Seeding

The project includes a seed script:

```bash
python scripts/seed_diseases.py
```

Important: this script drops and recreates the database before seeding disease records. It can also use Gemini to generate additional India-focused crop disease entries when `GEMINI_API_KEY` is configured.

## Roadmap

- Add model evaluation metrics and sample prediction screenshots.
- Add integration tests for upload, authentication, batch status polling, and history notes.
- Add database migrations with Flask-Migrate or Alembic.
- Add an admin review workflow for uncertain predictions.
- Add structured API responses for mobile or external clients.
- Add CI checks for tests, formatting, and dependency health.

## Repository Topics

Recommended GitHub topics:

```text
flask
python
machine-learning
plant-disease-detection
image-classification
computer-vision
tensorflow
deep-learning
agriculture
crop-disease
celery
redis
web-app
ai
flask-app
```

## Acknowledgements

Phyto Lab is built as a learning-focused applied AI project: part plant pathology assistant, part full-stack Flask application, and part deployment-ready ML workflow.

Predictions should be treated as decision support, not a replacement for expert agricultural advice in high-risk crop or commercial farming decisions.
