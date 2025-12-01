# Heart Disease Risk Assessment - Demo Test Cases

This document provides 6 test cases for demonstrating the UI and model predictions. Each test case has been validated against the actual Flask API to ensure accurate predictions.

---

## Test Case 1: No Disease - Healthy Young Female

**Patient Profile:** 35-year-old active female with excellent cardiovascular health

### UI Input Fields

| Field Name | Value |
|-----------|-------|
| **Age** * | 35 |
| **Sex** * | Female |
| **Chest Pain Type** * | Asymptomatic (no chest pain) |
| **Exercise-Induced Angina** * | No - I do not experience chest pain during exercise |
| **Resting Blood Pressure** | 110 |
| **Cholesterol Level** | 180 |
| **Fasting Blood Sugar** | Normal (≤120 mg/dl) |
| **Resting ECG** | Normal |
| **Max Heart Rate** | 180 |
| **ST Depression (Oldpeak)** | 0.0 |
| **ST Slope** | Upsloping (normal) |
| **Major Vessels** | 0 vessels |
| **Thalassemia** | Normal |

### API Request
```json
{
  "age": 35,
  "sex": "female",
  "cp": "asymptomatic",
  "exang": false,
  "trestbps": 110,
  "chol": 180,
  "fbs": false,
  "restecg": "normal",
  "thalch": 180,
  "oldpeak": 0.0,
  "slope": "upsloping",
  "ca": "0.0",
  "thal": "normal"
}
```

### Expected Result
- **Prediction:** Class 0 (No Disease)
- **Confidence:** 80.13%
- **Risk Category:** No Disease
- **Risk Color:** Green (#4CAF50)
- **Probabilities:**
  - Class 0 (No Disease): 80.13%
  - Class 1 (Mild-Moderate): 18.21%
  - Class 2 (Severe-Critical): 1.66%

---

## Test Case 2: No Disease - Healthy Middle-Aged Male

**Patient Profile:** 42-year-old male with minor, non-cardiac chest discomfort but otherwise healthy

### UI Input Fields

| Field Name | Value |
|-----------|-------|
| **Age** * | 42 |
| **Sex** * | Male |
| **Chest Pain Type** * | Non-Anginal Pain (not related to heart) |
| **Exercise-Induced Angina** * | No - I do not experience chest pain during exercise |
| **Resting Blood Pressure** | 118 |
| **Cholesterol Level** | 195 |
| **Fasting Blood Sugar** | Normal (≤120 mg/dl) |
| **Resting ECG** | Normal |
| **Max Heart Rate** | 175 |
| **ST Depression (Oldpeak)** | 0.3 |
| **ST Slope** | Upsloping (normal) |
| **Major Vessels** | 0 vessels |
| **Thalassemia** | Normal |

### API Request
```json
{
  "age": 42,
  "sex": "male",
  "cp": "non-anginal",
  "exang": false,
  "trestbps": 118,
  "chol": 195,
  "fbs": false,
  "restecg": "normal",
  "thalch": 175,
  "oldpeak": 0.3,
  "slope": "upsloping",
  "ca": "0.0",
  "thal": "normal"
}
```

### Expected Result
- **Prediction:** Class 0 (No Disease)
- **Confidence:** 96.10%
- **Risk Category:** No Disease
- **Risk Color:** Green (#4CAF50)
- **Probabilities:**
  - Class 0 (No Disease): 96.10%
  - Class 1 (Mild-Moderate): 3.90%
  - Class 2 (Severe-Critical): 0.00%

---

## Test Case 3: Mild-Moderate - Moderate Risk Profile

**Patient Profile:** 58-year-old male with atypical chest pain, elevated cholesterol, and moderate stress test abnormalities

### UI Input Fields

| Field Name | Value |
|-----------|-------|
| **Age** * | 58 |
| **Sex** * | Male |
| **Chest Pain Type** * | Atypical Angina (unusual heart-related pain) |
| **Exercise-Induced Angina** * | Yes - I experience chest pain during exercise |
| **Resting Blood Pressure** | 145 |
| **Cholesterol Level** | 240 |
| **Fasting Blood Sugar** | Normal (≤120 mg/dl) |
| **Resting ECG** | ST-T Wave Abnormality |
| **Max Heart Rate** | 140 |
| **ST Depression (Oldpeak)** | 1.8 |
| **ST Slope** | Flat |
| **Major Vessels** | 1 vessel |
| **Thalassemia** | Reversible Defect |

### API Request
```json
{
  "age": 58,
  "sex": "male",
  "cp": "atypical angina",
  "exang": true,
  "trestbps": 145,
  "chol": 240,
  "fbs": false,
  "restecg": "st-t abnormality",
  "thalch": 140,
  "oldpeak": 1.8,
  "slope": "flat",
  "ca": "1.0",
  "thal": "reversable defect"
}
```

### Expected Result
- **Prediction:** Class 1 (Mild-Moderate)
- **Confidence:** 62.51%
- **Risk Category:** Mild-Moderate
- **Risk Color:** Orange (#FF9800)
- **Probabilities:**
  - Class 0 (No Disease): 20.07%
  - Class 1 (Mild-Moderate): 62.51%
  - Class 2 (Severe-Critical): 17.42%

---

## Test Case 4: Mild-Moderate - High Confidence

**Patient Profile:** 54-year-old male with asymptomatic presentation, poor exercise capacity, and moderate stress test abnormalities

### UI Input Fields

| Field Name | Value |
|-----------|-------|
| **Age** * | 54 |
| **Sex** * | Male |
| **Chest Pain Type** * | Asymptomatic (no chest pain) |
| **Exercise-Induced Angina** * | Yes - I experience chest pain during exercise |
| **Resting Blood Pressure** | 124 |
| **Cholesterol Level** | 266 |
| **Fasting Blood Sugar** | Normal (≤120 mg/dl) |
| **Resting ECG** | Normal |
| **Max Heart Rate** | 109 |
| **ST Depression (Oldpeak)** | 2.2 |
| **ST Slope** | Flat |
| **Major Vessels** | 1 vessel |
| **Thalassemia** | Reversible Defect |

### API Request
```json
{
  "age": 54,
  "sex": "male",
  "cp": "asymptomatic",
  "exang": true,
  "trestbps": 124,
  "chol": 266,
  "fbs": false,
  "restecg": "normal",
  "thalch": 109,
  "oldpeak": 2.2,
  "slope": "flat",
  "ca": "1.0",
  "thal": "reversable defect"
}
```

### Expected Result
- **Prediction:** Class 1 (Mild-Moderate)
- **Confidence:** 70.11%
- **Risk Category:** Mild-Moderate
- **Risk Color:** Orange (#FF9800)
- **Probabilities:**
  - Class 0 (No Disease): 7.85%
  - Class 1 (Mild-Moderate): 70.11%
  - Class 2 (Severe-Critical): 22.04%

**Key Risk Indicators:** Very low max heart rate (109 bpm at age 54), significant ST depression (2.2 mm), and exercise-induced angina despite being asymptomatic at rest.

---

## Test Case 5: Severe-Critical - High Confidence

**Patient Profile:** 65-year-old female with asymptomatic presentation, very low exercise capacity, and 3 blocked vessels

### UI Input Fields

| Field Name | Value |
|-----------|-------|
| **Age** * | 65 |
| **Sex** * | Female |
| **Chest Pain Type** * | Asymptomatic (no chest pain) |
| **Exercise-Induced Angina** * | No - I do not experience chest pain during exercise |
| **Resting Blood Pressure** | 150 |
| **Cholesterol Level** | 225 |
| **Fasting Blood Sugar** | Normal (≤120 mg/dl) |
| **Resting ECG** | Left Ventricular Hypertrophy |
| **Max Heart Rate** | 114 |
| **ST Depression (Oldpeak)** | 1.0 |
| **ST Slope** | Flat |
| **Major Vessels** | 3 vessels |
| **Thalassemia** | Reversible Defect |

### API Request
```json
{
  "age": 65,
  "sex": "female",
  "cp": "asymptomatic",
  "exang": false,
  "trestbps": 150,
  "chol": 225,
  "fbs": false,
  "restecg": "lv hypertrophy",
  "thalch": 114,
  "oldpeak": 1.0,
  "slope": "flat",
  "ca": "3.0",
  "thal": "reversable defect"
}
```

### Expected Result
- **Prediction:** Class 2 (Severe-Critical)
- **Confidence:** 68.94%
- **Risk Category:** Severe-Critical
- **Risk Color:** Red-Pink (#E91E63)
- **Probabilities:**
  - Class 0 (No Disease): 14.51%
  - Class 1 (Mild-Moderate): 16.55%
  - Class 2 (Severe-Critical): 68.94%

**Key Risk Indicators:** Very low max heart rate (114 bpm at age 65), 3 major vessels with blockages, and LV hypertrophy.

---

## Test Case 6: Severe-Critical - Very High Confidence

**Patient Profile:** 62-year-old female with severe ST depression on stress test, downsloping pattern, and 2 blocked vessels

### UI Input Fields

| Field Name | Value |
|-----------|-------|
| **Age** * | 62 |
| **Sex** * | Female |
| **Chest Pain Type** * | Asymptomatic (no chest pain) |
| **Exercise-Induced Angina** * | No - I do not experience chest pain during exercise |
| **Resting Blood Pressure** | 140 |
| **Cholesterol Level** | 268 |
| **Fasting Blood Sugar** | Normal (≤120 mg/dl) |
| **Resting ECG** | Left Ventricular Hypertrophy |
| **Max Heart Rate** | 160 |
| **ST Depression (Oldpeak)** | 3.6 |
| **ST Slope** | Downsloping |
| **Major Vessels** | 2 vessels |
| **Thalassemia** | Normal |

### API Request
```json
{
  "age": 62,
  "sex": "female",
  "cp": "asymptomatic",
  "exang": false,
  "trestbps": 140,
  "chol": 268,
  "fbs": false,
  "restecg": "lv hypertrophy",
  "thalch": 160,
  "oldpeak": 3.6,
  "slope": "downsloping",
  "ca": "2.0",
  "thal": "normal"
}
```

### Expected Result
- **Prediction:** Class 2 (Severe-Critical)
- **Confidence:** 79.18%
- **Risk Category:** Severe-Critical
- **Risk Color:** Red-Pink (#E91E63)
- **Probabilities:**
  - Class 0 (No Disease): 14.52%
  - Class 1 (Mild-Moderate): 6.30%
  - Class 2 (Severe-Critical): 79.18%

**Critical Risk Indicators:** Extremely high ST depression (3.6 mm), downsloping ST pattern (worst type), 2 blocked vessels, and LV hypertrophy. This case demonstrates the model's ability to identify severe disease with high confidence.

---

## Using These Test Cases

### For UI Demo:
1. Start the Flask backend: `cd src/api && python app.py`
2. Start the frontend: `cd frontendRedesign && npm run dev`
3. Navigate to the Assessment page
4. Enter the values from the "UI Input Fields" tables above
5. Click "Show Optional Fields" to access all input fields
6. Submit the form and view the results

### For API Testing:
```bash
# Example: Test Case 1
curl -X POST http://localhost:8000/api/predict \
  -H "Content-Type: application/json" \
  -d '{"age": 35, "sex": "female", "cp": "asymptomatic", "exang": false, "trestbps": 110, "chol": 180, "fbs": false, "restecg": "normal", "thalch": 180, "oldpeak": 0.0, "slope": "upsloping", "ca": "0.0", "thal": "normal"}'
```

---

## Notes

- **Required Fields (*)**: Only 4 fields are required - age, sex, chest pain type, and exercise-induced angina
- **Optional Fields**: All other fields are optional. If left blank, the API will use sensible defaults (see API README for default values)
- **Model Type**: These predictions use the Hierarchical Classifier (binary + multi-class)
- **Confidence Interpretation**:
  - 60%+ is considered reliable
  - 70%+ is high confidence
  - 80%+ is very high confidence
- **Risk Categories**:
  - Class 0: No Disease (Green)
  - Class 1: Mild-Moderate (Orange)
  - Class 2: Severe-Critical (Red-Pink)

---

## Demo Flow Recommendation

For an effective demo, present test cases in this order:

1. **Test Case 2** (Healthy male, 96% confidence) - Show the "good news" scenario
2. **Test Case 3** (Mild-Moderate) - Demonstrate moderate risk detection
3. **Test Case 6** (Severe, 79% confidence) - Show high-risk detection with clear indicators
4. **Test Case 1** (Optional minimal input) - Demonstrate that only 4 fields are required
5. **Test Case 4** (Optional borderline case) - Show how the model handles uncertain cases

This order demonstrates the range of predictions while highlighting the model's ability to identify both healthy individuals and those requiring urgent care.
