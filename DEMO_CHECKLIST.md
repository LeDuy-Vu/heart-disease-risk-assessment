# Demo Checklist - Heart Disease Risk Assessment

## ✅ Pre-Demo Setup (5 minutes before presentation)

### 1. Start Backend API
```bash
cd cmpe-257-ML-heart-disease-risk-assessment
python src/api/app.py
```
- [ ] Server shows "Starting server on http://0.0.0.0:8000"
- [ ] Test: `curl http://localhost:8000/api/health` returns `{"status": "healthy"}`

### 2. Start Frontend
```bash
cd frontend
npm run dev
```
- [ ] Vite shows "Local: http://localhost:5173"
- [ ] Browser opens to landing page

### 3. Test End-to-End Flow
- [ ] Click "Start Your Assessment"
- [ ] Accept terms
- [ ] Fill sample data (use test data below)
- [ ] Get results successfully

---

## 📊 Sample Test Data for Demo

### **Low Risk Patient** (Expected: Level 0 or 1)
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

### **High Risk Patient** (Expected: Level 3 or 4)
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

## 🎤 Demo Script (5 minutes)

### **Slide 1: Problem Statement** (30 seconds)
*"Heart disease causes 32% of global deaths. Traditional diagnostics are expensive and inaccessible in resource-limited areas. We built an AI system that predicts severity levels 0-4, helping doctors prioritize treatment."*

### **Slide 2: Dataset & Methods** (45 seconds)
*"We used the UCI Heart Disease dataset - 920 patients, 14 clinical features from 4 medical centers. We implemented comprehensive preprocessing with KNN imputation, feature engineering, and addressed class imbalance using SMOTE."*

### **Slide 3: Model Performance** (45 seconds)
*"We compared 5 algorithms: Random Forest, XGBoost, SVM, Logistic Regression, and Gradient Boosting. Our hierarchical approach achieves:"*
- Binary classification: **85.1% F1-score** ✅ (exceeds 75% target)
- Multi-class: **57.9% F1-score** (working to improve to 75%)
- Best models: Voting Ensemble + Gradient Boosting

### **Slide 4: Live Demo** (2 minutes)
1. **Show Landing Page** (5 sec)
   - *"User-friendly interface with clear explanation of severity levels"*

2. **Start Assessment** (10 sec)
   - Accept terms & conditions

3. **Fill Form - Low Risk Patient** (30 sec)
   - Walk through each section quickly
   - *"45-year-old female, no symptoms, normal vitals"*

4. **Show Results** (30 sec)
   - *"Low risk detected with 87% confidence"*
   - Point out: color coding, probability chart, action items

5. **Repeat with High Risk** (30 sec) [Optional if time]
   - Use autofill or pre-filled form
   - *"65-year-old male with typical angina, high BP"*
   - *"Severe risk - recommends urgent medical attention"*

### **Slide 5: Technical Highlights** (30 seconds)
*"Full-stack implementation: React TypeScript frontend, Flask API backend, production-ready preprocessing pipeline. All code and models available on GitHub."*

### **Slide 6: Conclusions & Future Work** (30 seconds)
*"We exceeded binary classification targets and built a working clinical decision support tool. Future improvements include ordinal classification for better multi-class performance, SHAP explanations, and deployment to cloud."*

---

## 🎬 Demo Tips

### DO:
✅ Have both test cases ready in a text file for copy-paste
✅ Practice the workflow 2-3 times before presenting
✅ Keep browser at 100% zoom (or 110% for visibility)
✅ Close unnecessary tabs and applications
✅ Have a backup plan (screenshots/video) if live demo fails
✅ Smile and make eye contact with audience

### DON'T:
❌ Don't type every field slowly - copy-paste is fine
❌ Don't apologize for UI design choices
❌ Don't mention bugs or limitations unless asked
❌ Don't spend time explaining TypeScript errors in IDE
❌ Don't refresh the page unnecessarily

---

## 🚨 Backup Plan (If Live Demo Fails)

### Option A: Use Screenshots
Prepare 5 key screenshots:
1. Landing page
2. Assessment form (partially filled)
3. Low risk result
4. High risk result with probability chart
5. Architecture diagram

### Option B: Screen Recording
Record a 1-2 minute video showing the full workflow:
```bash
# Mac: QuickTime Player → File → New Screen Recording
# Windows: Windows + G → Record
```

---

## 📸 Screenshots to Prepare

Before demo, take screenshots of:
1. **Home page** - Clean landing with "Start Assessment" button
2. **Assessment form** - All 4 sections visible
3. **Low risk result** - Green color, low confidence
4. **High risk result** - Red color, high confidence, action items
5. **Probability distribution** - Bar chart showing all 5 classes
6. **Notebook results** - Model training summary from Jupyter
7. **Architecture diagram** - Frontend + API + ML pipeline

Save to: `screenshots/` folder

---

## ❓ Common Questions & Answers

### "Why is multi-class F1 below target (58% vs 75%)?"
*"Multi-class is genuinely challenging due to severe class imbalance - only 28 patients in severity level 4. We're implementing ordinal classification and cost-sensitive learning to improve this. Binary classification exceeds target at 85%."*

### "How accurate is this compared to doctors?"
*"Our model achieves comparable performance to published studies (80-85% accuracy). However, this is a screening tool to assist doctors, not replace them. The hierarchical approach mimics clinical decision-making."*

### "Can you explain the preprocessing?"
*"We handled missing values with KNN imputation, encoded categorical variables, engineered 5 domain-informed features like age groups and BP categories, and used standard scaling. All preprocessing artifacts are saved for deployment."*

### "What about deployment?"
*"We built a production-ready Flask API and React frontend. Next steps include Docker containerization and cloud deployment to Vercel (frontend) + Railway (backend)."*

### "How did you handle class imbalance?"
*"We used SMOTE for binary classification and BorderlineSMOTE for multi-class, which focuses on borderline cases. We also tried cost-sensitive learning and ensemble methods."*

---

## 📋 Technical Specs to Memorize

- **Dataset**: 920 patients, 14 features, 5 severity levels (0-4)
- **Train/Test Split**: 80/20 stratified
- **Features**: 18 after engineering (13 original + 5 engineered)
- **Best Binary Model**: Voting Ensemble (RF + XGBoost + GB)
- **Best Multi-class**: Gradient Boosting (tuned)
- **Preprocessing**: KNN imputation, label encoding, standard scaling
- **Tech Stack**: Python, scikit-learn, XGBoost, React, TypeScript, Flask
- **Class Imbalance**: SMOTE (binary), BorderlineSMOTE (multi-class)

---

## ⏱️ Time Management

| Activity | Time | Notes |
|----------|------|-------|
| Setup | -5 min | Start servers before presentation |
| Introduction | 0:30 | Problem + motivation |
| Methods | 0:45 | Dataset + preprocessing |
| Results | 0:45 | Model performance metrics |
| **Live Demo** | 2:00 | **Core of presentation** |
| Discussion | 0:30 | Future work + conclusions |
| **Total** | **5:00** | Leave buffer for questions |

---

## 🎯 Success Criteria

By end of demo, audience should understand:
1. ✅ The problem you're solving (heart disease severity prediction)
2. ✅ Your approach (hierarchical ML with preprocessing pipeline)
3. ✅ Your results (85% binary F1, working multi-class)
4. ✅ The practical application (working web app for risk assessment)

---

## 📞 Emergency Contacts (Team)

If you need help during demo:
- **Lam Nguyen** - Data/Preprocessing expert
- **James Pham** - ML/Model expert
- **Le Duy Vu** - Frontend/API expert
- **Vi Thi Tuong Nguyen** - Evaluation/Docs expert

Have team members ready to answer technical questions!

---

**Good luck with your demo! 🚀 You've built something impressive - be confident!**
