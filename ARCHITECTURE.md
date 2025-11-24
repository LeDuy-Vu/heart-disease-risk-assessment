# System Architecture

## 🏗️ Complete System Overview

```
┌─────────────────────────────────────────────────────────────────────────┐
│                          USER BROWSER                                    │
│                     http://localhost:5173                                │
└────────────────────────────┬────────────────────────────────────────────┘
                             │
                             │ HTTP Request
                             ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                     REACT FRONTEND (Vite + TypeScript)                   │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│  ┌─────────────┐   ┌──────────────────┐   ┌─────────────────┐          │
│  │   Home.tsx  │──▶│ SimpleAssessment │──▶│  Axios Client   │          │
│  │             │   │      .tsx        │   │  (API calls)    │          │
│  │  Landing    │   │                  │   │                 │          │
│  │  + Terms    │   │  • Form fields   │   │ POST /api/      │          │
│  └─────────────┘   │  • Validation    │   │      predict    │          │
│                    │  • Results UI    │   └────────┬────────┘          │
│                    └──────────────────┘            │                     │
│                                                     │                     │
└─────────────────────────────────────────────────────┼─────────────────────┘
                                                      │
                                    HTTP POST         │
                                    (JSON data)       │
                                                      ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                    FLASK API (Python Backend)                            │
│                     http://localhost:8000                                │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│  ┌──────────────────────────────────────────────────────────────────┐   │
│  │  POST /api/predict                                               │   │
│  │                                                                  │   │
│  │  1. Receive patient data (JSON)                                 │   │
│  │     {age, sex, cp, trestbps, chol, ...}                        │   │
│  │                                                                  │   │
│  │  2. Validate & transform input                                  │   │
│  │     • Check required fields                                     │   │
│  │     • Convert types (string → int/float)                        │   │
│  │     • Map categorical values                                    │   │
│  │                                                                  │   │
│  │  3. Preprocess with pipeline ────────────────────────┐         │   │
│  │                                                       │         │   │
│  └───────────────────────────────────────────────────────┼─────────┘   │
│                                                          │               │
│  ┌───────────────────────────────────────────────────────▼─────────┐   │
│  │  preprocess_input() function                                    │   │
│  │                                                                  │   │
│  │  a) Handle missing values                                       │   │
│  │     • Create missing indicators (ca_missing, etc.)              │   │
│  │     • Label encode categoricals                                 │   │
│  │     • Impute with median/mode                                   │   │
│  │                                                                  │   │
│  │  b) Feature engineering                                         │   │
│  │     • age_group (bins: 0-45, 45-60, 60-75, 75+)                │   │
│  │     • bp_category (AHA: <120, 120-130, 130-140, >140)          │   │
│  │     • chol_category (<200, 200-240, >240)                      │   │
│  │     • hr_reserve (220-age-thalch)                              │   │
│  │     • cv_risk_score (composite)                                │   │
│  │                                                                  │   │
│  │  c) Scale features                                              │   │
│  │     • StandardScaler (fitted on training data)                  │   │
│  │     • Output: (1, 18) numpy array                              │   │
│  └──────────────────────────────────────┬───────────────────────────┘   │
│                                         │                               │
│                                         ▼                               │
│  ┌─────────────────────────────────────────────────────────────────┐   │
│  │  ML Model Prediction                                            │   │
│  │                                                                  │   │
│  │  hierarchical_model.predict(X)                                  │   │
│  │  hierarchical_model.predict_proba(X)                           │   │
│  │                                                                  │   │
│  │  ┌───────────────────────────────────────────────────┐         │   │
│  │  │  HierarchicalClassifier                           │         │   │
│  │  │                                                    │         │   │
│  │  │  Stage 1: Binary (Disease vs No Disease)          │         │   │
│  │  │  ├─ Model: Voting Ensemble                        │         │   │
│  │  │  │  ├─ Random Forest (tuned)                      │         │   │
│  │  │  │  ├─ XGBoost (tuned)                            │         │   │
│  │  │  │  └─ Gradient Boosting                          │         │   │
│  │  │  └─ Soft voting (probability averaging)           │         │   │
│  │  │                                                    │         │   │
│  │  │  If prediction = 0 (no disease):                  │         │   │
│  │  │      → Return severity_level = 0                  │         │   │
│  │  │                                                    │         │   │
│  │  │  If prediction = 1 (disease):                     │         │   │
│  │  │      ▼                                             │         │   │
│  │  │  Stage 2: Multi-class (Severity 1-4)              │         │   │
│  │  │  └─ Model: Gradient Boosting (tuned)              │         │   │
│  │  │     └─ Returns severity: 1, 2, 3, or 4            │         │   │
│  │  │                                                    │         │   │
│  │  └────────────────────────────────────────────────────┘         │   │
│  │                                                                  │   │
│  │  Output:                                                         │   │
│  │  • severity_level: int (0-4)                                    │   │
│  │  • probabilities: array of 5 floats [P(0), P(1), ..., P(4)]   │   │
│  │  • confidence: float (probability of predicted class)           │   │
│  └──────────────────────────────────┬───────────────────────────────┘   │
│                                     │                                   │
│                                     ▼                                   │
│  ┌─────────────────────────────────────────────────────────────────┐   │
│  │  Enrich Response with UI Configuration                          │   │
│  │                                                                  │   │
│  │  Based on severity_level, add:                                  │   │
│  │                                                                  │   │
│  │  • display: {title, message, colors, icon}                      │   │
│  │  • recommendation: {action_items[], urgency}                    │   │
│  │  • probabilities: {chart_data with labels & colors}             │   │
│  │  • next_steps: {emergency_banner, notes}                        │   │
│  │  • metadata: {timestamp, report_id, model_version}              │   │
│  └──────────────────────────────────┬───────────────────────────────┘   │
│                                     │                                   │
│                                     ▼                                   │
│                          Return JSON Response                           │
│                                                                           │
└───────────────────────────────────┬───────────────────────────────────────┘
                                    │
                                    │ HTTP 200 OK
                                    │ (JSON response)
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                     REACT FRONTEND (Results Display)                     │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│  Parse API response:                                                     │
│  {                                                                        │
│    "success": true,                                                       │
│    "data": {                                                              │
│      "prediction": {                                                      │
│        "severity_level": 3,                                               │
│        "severity_label": "Severe Heart Disease",                          │
│        "confidence": 0.87                                                 │
│      },                                                                   │
│      "display": {                                                         │
│        "title": "High Risk Detected",                                     │
│        "severity_color": "#E91E63",                                       │
│        "background_color": "#FCE4EC"                                      │
│      },                                                                   │
│      "probabilities": {                                                   │
│        "chart_data": [                                                    │
│          {label: "No Disease", value: 2.1, color: "#4CAF50"},            │
│          {label: "Mild", value: 5.3, color: "#FFC107"},                  │
│          {label: "Moderate", value: 8.2, color: "#FF6B35"},              │
│          {label: "Severe", value: 87.0, color: "#E91E63"}, ← Predicted   │
│          {label: "Critical", value: 0.4, color: "#9C27B0"}               │
│        ]                                                                  │
│      },                                                                   │
│      "recommendation": {                                                  │
│        "action_items": [                                                  │
│          "Contact cardiologist immediately",                              │
│          "Do not delay - call within 24-48 hours",                       │
│          ...                                                              │
│        ],                                                                 │
│        "urgency": "high"                                                  │
│      }                                                                    │
│    }                                                                      │
│  }                                                                        │
│                                                                           │
│  ┌────────────────────────────────────────────────────────┐             │
│  │  Render Results UI:                                     │             │
│  │                                                          │             │
│  │  ┌────────────────────────────────────────────────┐    │             │
│  │  │  ⚠️  High Risk Detected                        │    │             │
│  │  │  Your assessment shows significant risk...     │    │             │
│  │  │  Severity: Severe (87% confidence)             │    │             │
│  │  └────────────────────────────────────────────────┘    │             │
│  │                                                          │             │
│  │  ┌────────────────────────────────────────────────┐    │             │
│  │  │  Probability Distribution (Bar Chart)          │    │             │
│  │  │  ▓▓ 2.1% No Disease                            │    │             │
│  │  │  ▓▓▓▓▓ 5.3% Mild                               │    │             │
│  │  │  ▓▓▓▓▓▓▓▓ 8.2% Moderate                        │    │             │
│  │  │  ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓ 87.0% Severe ✓      │    │             │
│  │  │  ▓ 0.4% Critical                               │    │             │
│  │  └────────────────────────────────────────────────┘    │             │
│  │                                                          │             │
│  │  ┌────────────────────────────────────────────────┐    │             │
│  │  │  Recommended Actions:                          │    │             │
│  │  │  → Contact cardiologist immediately            │    │             │
│  │  │  → Do not delay - call within 24-48 hours      │    │             │
│  │  │  → Avoid strenuous activity until evaluated    │    │             │
│  │  └────────────────────────────────────────────────┘    │             │
│  │                                                          │             │
│  │  [Take Another Assessment]  [Print Results]             │             │
│  └──────────────────────────────────────────────────────────┘             │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
```

---

## 📦 Data Models & Artifacts

### Saved Files (models/)

```
hierarchical_classifier.pkl (17 MB)
├─ binary_model: VotingClassifier
│  ├─ estimator_1: RandomForestClassifier (tuned)
│  ├─ estimator_2: XGBClassifier (tuned)
│  └─ estimator_3: GradientBoostingClassifier
│
└─ multiclass_model: GradientBoostingClassifier (tuned)

preprocessing_artifacts.pkl
├─ scaler: StandardScaler (fitted on 736 train samples)
├─ label_encoders: dict {
│     'sex': LabelEncoder,
│     'cp': LabelEncoder,
│     'fbs': LabelEncoder,
│     'restecg': LabelEncoder,
│     'exang': LabelEncoder,
│     'slope': LabelEncoder,
│     'ca': LabelEncoder,
│     'thal': LabelEncoder
│  }
├─ knn_imputer: KNNImputer (k=5)
├─ feature_names: list of 18 feature names
├─ numeric_features: ['age', 'trestbps', 'chol', 'thalch', 'oldpeak']
└─ categorical_features: ['sex', 'cp', 'fbs', 'restecg', 'exang', 'slope', 'ca', 'thal']

model_metadata.pkl
├─ best_binary_model_name: "Voting Ensemble"
├─ best_binary_f1: 0.8513
├─ best_multiclass_model_name: "Gradient Boosting"
├─ best_multiclass_f1: 0.5408
├─ hierarchical_f1: 0.5595
├─ random_state: 42
└─ smote_method_multiclass: "BorderlineSMOTE (kind=borderline-1)"
```

---

## 🔄 Data Flow Example

### Input (from form):
```javascript
{
  age: 65,
  sex: "male",
  cp: "typical angina",
  trestbps: 160,
  chol: 280,
  fbs: "true",
  restecg: "st-t abnormality",
  thalch: 120,
  exang: "true",
  oldpeak: 2.5,
  slope: "downsloping",
  ca: "2.0",
  thal: "reversable defect"
}
```

### After Preprocessing (18 features):
```
[1.234, -0.891, 0.456, ..., 0.234]  # Scaled numpy array
         ↓
    age (scaled)
         sex (encoded & scaled)
              cp (encoded & scaled)
                   ... etc
```

### Model Output:
```python
severity_level = 3  # Severe
probabilities = [0.021, 0.053, 0.082, 0.870, 0.004]
#                  0      1      2      3      4
#                              └─ 87% confident it's severity 3
```

### API Response (enriched):
```json
{
  "success": true,
  "data": {
    "prediction": {
      "severity_level": 3,
      "severity_label": "Severe Heart Disease",
      "risk_category": "Very High Risk",
      "confidence": 0.87
    },
    "display": {
      "title": "High Risk Detected",
      "message": "Your assessment shows significant heart disease risk factors. Urgent medical attention is recommended.",
      "severity_color": "#E91E63",
      "background_color": "#FCE4EC",
      "severity_icon": "error"
    },
    "recommendation": {
      "action_items": [
        "Contact a cardiologist immediately for urgent consultation",
        "Do not delay - call within 24-48 hours",
        "Avoid strenuous physical activity until evaluated"
      ],
      "urgency": "high"
    },
    "probabilities": {
      "chart_data": [
        {"label": "No Disease", "value": 2.1, "color": "#4CAF50"},
        {"label": "Mild", "value": 5.3, "color": "#FFC107"},
        {"label": "Moderate", "value": 8.2, "color": "#FF6B35"},
        {"label": "Severe", "value": 87.0, "color": "#E91E63"},
        {"label": "Very Severe", "value": 0.4, "color": "#9C27B0"}
      ]
    }
  }
}
```

---

## 🛠️ Technology Stack

### Frontend
```
React 19.2.0          # UI framework
TypeScript 5.9.3      # Type safety
Vite 7.2.2           # Build tool & dev server
TailwindCSS 3.4.18   # Styling
React Hook Form 7.66 # Form handling
Axios 1.13.2         # HTTP client
Lucide React 0.553   # Icons
Recharts 3.4.1       # Charts (unused in simple version)
React Router 7.9.6   # Navigation
```

### Backend
```
Python 3.8+
Flask 3.0+           # Web framework
Flask-CORS           # Cross-origin requests
scikit-learn 1.3+    # ML algorithms
XGBoost 2.0+         # Gradient boosting
imbalanced-learn     # SMOTE
pandas 2.0+          # Data manipulation
numpy 1.24+          # Numerical operations
```

### ML Pipeline
```
Preprocessing:
- KNNImputer (k=5)
- LabelEncoder (8 categorical features)
- StandardScaler

Models:
- RandomForestClassifier
- XGBClassifier
- GradientBoostingClassifier
- VotingClassifier (soft voting)

Evaluation:
- StratifiedKFold (k=5)
- F1-score (weighted)
- ROC-AUC
- Confusion matrices
```

---

## 🔐 Security Considerations

### Current State (Demo Version)
- ✅ CORS enabled (frontend can call API)
- ✅ Input validation (required fields, type checking)
- ⚠️ No authentication (anyone can use)
- ⚠️ No rate limiting (can be abused)
- ⚠️ HTTP only (no encryption in transit)

### Production Requirements
- 🔒 HTTPS/TLS encryption
- 🔒 JWT authentication
- 🔒 Rate limiting (Flask-Limiter)
- 🔒 Input sanitization (prevent XSS)
- 🔒 API key rotation
- 🔒 Audit logging
- 🔒 HIPAA compliance (if handling real patient data)

---

## 📊 Performance Characteristics

### API Response Time
```
Typical request:
├─ Data validation: ~5ms
├─ Preprocessing: ~15ms
│  ├─ Label encoding: 3ms
│  ├─ Feature engineering: 5ms
│  └─ Scaling: 7ms
├─ Model inference: ~10ms
│  ├─ Stage 1 (binary): 5ms
│  └─ Stage 2 (multi-class): 5ms
└─ Response formatting: ~5ms
Total: ~35ms (0.035 seconds)
```

### Model Sizes
```
hierarchical_classifier.pkl:    17 MB
preprocessing_artifacts.pkl:    48 KB
model_metadata.pkl:             648 bytes
Total model assets:             ~17.05 MB
```

### Memory Usage
```
Backend API:           ~200 MB (with models loaded)
Frontend (dev mode):   ~150 MB
Frontend (production): ~2 MB (after minification)
```

---

## 🌍 Deployment Architecture (Future)

```
┌────────────────────────────────────────────────────────┐
│                   Vercel (Frontend)                     │
│                   https://heart-risk.app                │
│  ┌──────────────────────────────────────────────────┐  │
│  │  Static React Build                               │  │
│  │  - index.html, main.js, main.css                 │  │
│  │  - Served via CDN (global edge network)          │  │
│  └──────────────────┬───────────────────────────────┘  │
└─────────────────────┼───────────────────────────────────┘
                      │
                      │ HTTPS API calls
                      ▼
┌────────────────────────────────────────────────────────┐
│              Railway/Render (Backend)                   │
│              https://api.heart-risk.app                 │
│  ┌──────────────────────────────────────────────────┐  │
│  │  Docker Container                                 │  │
│  │  ├─ Flask API (Gunicorn workers)                 │  │
│  │  ├─ ML models loaded in memory                   │  │
│  │  └─ Redis cache for predictions                  │  │
│  └──────────────────┬───────────────────────────────┘  │
└─────────────────────┼───────────────────────────────────┘
                      │
                      │ Database queries
                      ▼
┌────────────────────────────────────────────────────────┐
│              PostgreSQL (Supabase)                      │
│  ┌──────────────────────────────────────────────────┐  │
│  │  Tables:                                          │  │
│  │  - users (authentication)                         │  │
│  │  - assessments (patient data)                     │  │
│  │  - predictions (results + metadata)               │  │
│  │  - chat_sessions (AI conversations)               │  │
│  └───────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────┘
```

---

**This architecture provides a complete understanding of how your system works from user input to prediction display!** 🎯
