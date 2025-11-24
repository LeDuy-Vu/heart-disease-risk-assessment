# Quick Start Guide - Working Demo

This guide will help you get the simplified, **working version** of the application running in under 5 minutes.

## What's Different in This Version?

✅ **Single-page assessment** - All questions on one page
✅ **Direct API integration** - Calls `/api/predict` endpoint directly
✅ **No database required** - Stateless, session-free
✅ **No authentication** - Anonymous usage
✅ **Instant results** - Predictions displayed immediately

This version is perfect for **demos** and **course presentations**.

---

## Prerequisites

- Python 3.8+ installed
- Node.js 18+ installed
- Terminal/Command Prompt

---

## Step 1: Start the Backend API (2 minutes)

```bash
# Navigate to project root
cd cmpe-257-ML-heart-disease-risk-assessment

# Install Python dependencies (if not already done)
pip install -r requirements.txt

# Start the Flask API
python src/api/app.py
```

You should see:
```
============================================================
Heart Disease Risk Assessment API
============================================================

Endpoints:
  POST /api/predict - Get heart disease prediction
  GET  /api/health  - Health check
  GET  /api/info    - Model information
  GET  /            - API documentation

============================================================
Starting server on http://0.0.0.0:8000
============================================================
```

✅ **Backend is running on http://localhost:8000**

**Test it:**
```bash
# In a new terminal:
curl http://localhost:8000/api/health
```

Expected response:
```json
{
  "status": "healthy",
  "model_loaded": true,
  "version": "1.0.0",
  "timestamp": "2025-11-23T..."
}
```

---

## Step 2: Start the Frontend (3 minutes)

```bash
# Open a NEW terminal window
cd cmpe-257-ML-heart-disease-risk-assessment/frontend

# Install dependencies (first time only)
npm install

# Start the development server
npm run dev
```

You should see:
```
  VITE v7.2.2  ready in 500 ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
  ➜  press h + enter to show help
```

✅ **Frontend is running on http://localhost:5173**

---

## Step 3: Use the Application

1. **Open your browser** to http://localhost:5173

2. **Click "Start Your Assessment"**

3. **Accept Terms & Conditions**

4. **Fill out the assessment form:**
   - Personal Information (age, sex)
   - Symptoms (chest pain type, exercise angina)
   - Vitals (blood pressure, cholesterol, blood sugar, heart rate)
   - Diagnostic Tests (ECG, ST depression, etc.)

5. **Click "Get My Risk Assessment"**

6. **View your results:**
   - Risk level with color-coded severity
   - Confidence score
   - Probability distribution chart
   - Personalized action items

---

## Example Test Data

Use this sample data to test the application:

### Low Risk Patient
```
Age: 45
Sex: Female
Chest Pain: Asymptomatic
Blood Pressure: 120
Cholesterol: 180
Fasting Blood Sugar: No
Max Heart Rate: 170
Exercise Angina: No
ECG: Normal
Oldpeak: 0.0
ST Slope: Upsloping
Major Vessels: 0
Thalassemia: Normal
```

### High Risk Patient
```
Age: 65
Sex: Male
Chest Pain: Typical Angina
Blood Pressure: 160
Cholesterol: 280
Fasting Blood Sugar: Yes
Max Heart Rate: 120
Exercise Angina: Yes
ECG: ST-T Abnormality
Oldpeak: 2.5
ST Slope: Downsloping
Major Vessels: 2
Thalassemia: Reversible Defect
```

---

## Troubleshooting

### Backend Won't Start
**Problem**: `ModuleNotFoundError: No module named 'flask'`
```bash
pip install -r requirements.txt
```

**Problem**: Port 8000 already in use
```bash
# Kill the process using port 8000
# On Mac/Linux:
lsof -ti:8000 | xargs kill -9
# On Windows:
netstat -ano | findstr :8000
taskkill /PID <PID> /F
```

### Frontend Won't Start
**Problem**: `npm: command not found`
- Install Node.js from https://nodejs.org/

**Problem**: Port 5173 already in use
```bash
# Vite will automatically try port 5174, 5175, etc.
# Or you can specify a different port:
npm run dev -- --port 3000
```

### API Connection Error
**Problem**: Frontend shows "Failed to get prediction"

**Check:**
1. Backend is running on http://localhost:8000
   ```bash
   curl http://localhost:8000/api/health
   ```

2. Frontend `.env` file (if exists) has correct API URL:
   ```bash
   # frontend/.env.local
   VITE_API_URL=http://localhost:8000
   ```

3. CORS is enabled (already set in app.py):
   ```python
   CORS(app)  # Line 17 in src/api/app.py
   ```

---

## Architecture Overview

```
┌─────────────────┐         HTTP POST          ┌─────────────────┐
│                 │  /api/predict (JSON data)  │                 │
│  React Frontend │ ───────────────────────────▶│   Flask API     │
│  (Port 5173)    │                             │  (Port 8000)    │
│                 │◀─────────────────────────── │                 │
└─────────────────┘   JSON response with        └────────┬────────┘
                      prediction & UI config             │
                                                          │
                                                          ▼
                                              ┌──────────────────────┐
                                              │  ML Models (Pickle)  │
                                              │  - Hierarchical      │
                                              │  - Scaler            │
                                              │  - Label Encoders    │
                                              └──────────────────────┘
```

**Data Flow:**
1. User fills form in React app
2. Frontend transforms form data to API format
3. POST request to `/api/predict` with patient data
4. Backend preprocesses data (imputation, encoding, scaling)
5. ML model predicts severity level (0-4)
6. Backend enriches response with UI configuration
7. Frontend displays results with colors, charts, recommendations

---

## What's Next?

### For Course Demo
- ✅ You're ready! The app works end-to-end
- 📹 Record a screen demo showing the workflow
- 📊 Take screenshots for your report
- 📝 Update your project report with actual results

### For Better ML Performance
See [IMPROVEMENTS.md](IMPROVEMENTS.md) for:
- Ordinal classification to improve multi-class F1
- SHAP explanations for predictions
- Cost-sensitive learning
- Confusion matrix analysis

### For Production Deployment
- Implement full backend with database (PostgreSQL)
- Add user authentication (JWT)
- Add chat AI integration
- Deploy to cloud (Vercel + Railway)
- See [IMPROVEMENTS.md](IMPROVEMENTS.md) Section 11

---

## Demo Script

**For 5-minute presentation:**

1. **Introduction (30 seconds)**
   - "Heart disease is the #1 cause of death globally"
   - "Our ML system predicts severity on a 0-4 scale"

2. **Show the Assessment (2 minutes)**
   - Walk through form sections
   - Explain each clinical parameter
   - Use sample high-risk patient data

3. **Show Results (1.5 minutes)**
   - Highlight risk level with color coding
   - Explain confidence score
   - Show probability distribution
   - Read action items

4. **Explain ML Pipeline (1 minute)**
   - "We tested 5 algorithms: RF, XGBoost, SVM, LR, GB"
   - "Binary classification: 85% F1-score"
   - "Multi-class: 58% F1-score"
   - "Used SMOTE for class imbalance"
   - "Hierarchical approach: 2-stage prediction"

5. **Q&A / Close**

---

## File Structure (Simplified Version)

```
cmpe-257-ML-heart-disease-risk-assessment/
├── frontend/
│   ├── src/
│   │   ├── pages/
│   │   │   ├── Home.tsx                 # Landing page
│   │   │   └── SimpleAssessment.tsx     # ⭐ Main assessment form
│   │   ├── App.tsx                      # Router setup
│   │   └── index.css                    # Tailwind styles
│   ├── package.json
│   └── vite.config.ts
│
├── src/
│   └── api/
│       └── app.py                       # ⭐ Flask API with /api/predict
│
├── models/
│   ├── hierarchical_classifier.pkl      # Trained model
│   ├── preprocessing_artifacts.pkl      # Scaler & encoders
│   └── model_metadata.pkl               # Model info
│
├── notebooks/
│   ├── data_preprocessing.ipynb         # EDA & preprocessing
│   └── model_training.ipynb             # Model training
│
├── QUICKSTART.md                        # ⭐ This file
├── IMPROVEMENTS.md                      # Detailed recommendations
└── requirements.txt
```

---

## Need Help?

1. **Check logs:**
   - Backend: Terminal where `python src/api/app.py` is running
   - Frontend: Browser console (F12 → Console tab)

2. **Verify setup:**
   ```bash
   # Backend health check
   curl http://localhost:8000/api/health

   # Frontend running
   curl http://localhost:5173
   ```

3. **Common issues:**
   - Models not found → Check `models/` directory has .pkl files
   - CORS error → Restart backend with `python src/api/app.py`
   - Form validation errors → Check all required fields filled

---

**You're all set! 🎉 The application is fully functional for your demo.**
