---
title: Phyto Lab
emoji: 🌿
colorFrom: green
colorTo: blue
sdk: docker
app_port: 7860
---

# Plant-Disease-Recognition-System (Phyto Lab)

Flask-based plant disease recognition app with:
- Single image diagnosis
- Batch processing via Celery
- History tracking
- Auth (register/login)
- Multilingual UI

## Quick Start (Local)

1. Create and activate a virtual environment.
2. Install dependencies:
```bash
pip install -r requirements.txt
```
3. Run the app:
```bash
python run.py
```
4. Open:
`http://127.0.0.1:5000/`

## Model and Metadata Files

By default, the app uses:
- `app/assets/models/plant_disease_recog_model_pwp.keras`
- `app/assets/plant_disease.json`

You can override these with environment variables:
- `MODEL_PATH`
- `PLANT_DISEASE_JSON`

## Docker

Build:
```bash
docker build -t phyto-lab .
```

Run:
```bash
docker run --rm -p 7860:7860 phyto-lab
```

Then open:
`http://127.0.0.1:7860/`

## Notes

- Uploaded files are stored in `uploadimages/`.
- Max upload size is 16MB.
- Supported file types: PNG, JPG, JPEG, GIF.
