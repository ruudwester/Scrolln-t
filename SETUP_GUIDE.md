<div align="center">

# 🚀 Scrolln't Setup Guide

### Complete Installation and Configuration Instructions

---

</div>

## 📋 Prerequisites

Before you begin, ensure you have:

- ✅ Python 3.8 or higher installed
- ✅ Google Chrome browser
- ✅ Your training data CSV file
- ✅ MP3 warning sound files (optional)

---

## 📦 Step 1: Copy the Project

Copy the entire `scrolln't` folder to your desired location.

**Example locations:**
- `C:\Users\YourName\Documents\scrolln't`
- `C:\Users\YourName\Desktop\scrolln't`
- Any location you prefer

---

## 🔧 Step 2: Install Python Dependencies

1. Open Command Prompt (or PowerShell)
2. Navigate to the project directory:

```bash
cd C:\Users\santa\OneDrive\Pictures\scrolln't
```

3. Install required packages:

```bash
pip install -r requirements.txt
```

This will install:
- scikit-learn
- pandas
- numpy
- flask
- flask-cors
- joblib

---

## 📊 Step 3: Prepare Training Data

Place your `training_data.csv` file in the `data/` directory:

```
C:\Users\santa\OneDrive\Pictures\scrolln't\data\training_data.csv
```

### Required CSV Columns

Your CSV must contain these columns:

| Column | Description |
|--------|-------------|
| SessionID | Unique identifier for each session |
| Timestamp | When the data was recorded |
| Duration | Total session duration in seconds |
| Videos | Number of unique videos watched |
| Shorts | Number of unique Shorts watched |
| Scrolls | Total scroll actions |
| LateNight | 1 if late night (10 PM - 6 AM), 0 otherwise |
| ActiveViewingTime | Time with user activity in seconds |
| Breaks | Number of breaks taken |
| TimeSinceLastBreak | Time since last break in seconds |
| ScrollRate | Scrolls per second |
| ShortRate | Shorts per second |
| Stage | Behavioral stage (0-5) - target variable |

**Optional column:**
- RiskScore (will be preserved but not used as a feature)

---

## 🎵 Step 4: Add Sound Files (Optional)

Place MP3 files in the `extension/sounds/` directory:

### Warning Sounds (for Stage Popups)
```
extension/sounds/stage2.mp3
extension/sounds/stage3.mp3
extension/sounds/stage4.mp3
```

### Break Mode Ambient Audio
```
extension/sounds/sagegarden.mp3
extension/sounds/lavendardream.mp3
extension/sounds/oceanbreeze.mp3
extension/sounds/warmsand.mp3
```

**Note:** Sounds are optional - the system works without them.

---

## 🎓 Step 5: Train the ML Models

1. Navigate to the ML directory:

```bash
cd C:\Users\santa\OneDrive\Pictures\scrolln't\ml
```

2. Run the training script:

```bash
python train.py
```

This will:
- Load and validate your training data
- Split data by SessionID (preventing data leakage)
- Train three models:
  - Random Forest
  - Gradient Boosting
  - Extra Trees
- Evaluate all models
- Save trained models to `ml/models/`
- Save metrics to `ml/models/model_metrics.json`

**Expected output:**
```
Loading training data...
Data shape: (X rows, Y columns)
Training Random Forest...
Training Gradient Boosting...
Training Extra Trees...
Model evaluation complete
Models saved to ml/models/
```

---

## ▶️ Step 6: Start the Python Server

### Option A: With Visible Console (Recommended for First Time)

1. Navigate to the server directory:

```bash
cd C:\Users\santa\OneDrive\Pictures\scrolln't\server
```

2. Start the server:

```bash
python app.py
```

You should see:
```
============================================================
SCROLLN'T - ML API SERVER
============================================================
Loading ML models...
  Loaded random_forest
  Loaded gradient_boosting
  Loaded extra_trees
  Loaded feature names: [...]
All models loaded successfully!
Starting Flask server on http://127.0.0.1:5000
```

**Keep this terminal open** while using the extension.

### Option B: Without Visible Console (Background)

```bash
cd C:\Users\santa\OneDrive\Pictures\scrolln't\server
pythonw app.py
```

The server runs in the background without a console window.

### Option C: Using Batch File (Easiest)

Double-click this file:
```
C:\Users\santa\OneDrive\Pictures\scrolln't\server\start_server.bat
```

---

## 🔌 Step 7: Load the Chrome Extension

1. Open Google Chrome
2. Navigate to: `chrome://extensions/`
3. Enable **"Developer mode"** (toggle in top-right corner)
4. Click **"Load unpacked"**
5. Navigate to and select: `C:\Users\santa\OneDrive\Pictures\scrolln't\extension`
6. Click **"Select Folder"**

The extension should now appear in your extensions list.

---

## ✅ Step 8: Verify Installation

1. Open YouTube (youtube.com)
2. You should see a live counter in the top-right corner
3. Click the extension icon in your Chrome toolbar
4. The dashboard should open
5. Check that **"Server Online"** is shown in green

If it shows "Server Offline":
- Ensure the Python server is running
- Check that the server shows "Starting Flask server on http://127.0.0.1:5000"
- Try reloading the extension

---

## 🎯 Step 9: Test the Extension

### Test Live Counter
- Open YouTube
- Scroll on Shorts
- Watch the counter update in real-time

### Test Stage Popups
- Watch enough content to trigger stage increases
- Verify popups appear with quotes and images
- Check that warning sounds play (if you added them)

### Test Break Mode
- Click "Take a Break" in the live counter
- Verify Break Mode opens with the timer
- Test theme switching
- Test the timer controls
- Test the activity lounge

### Test Dashboard
- Click the extension icon
- Verify metrics are updating
- Click on metric cards to see descriptions
- Click on model cards to see confusion matrices

---

## 🔄 Daily Usage

### Starting the Server

**Quick Method:** Double-click the batch file
```
C:\Users\santa\OneDrive\Pictures\scrolln't\server\start_server.bat
```

**Manual Method:**
```bash
cd C:\Users\santa\OneDrive\Pictures\scrolln't\server
python app.py
```

### Using the Extension

1. Start the server (if not already running)
2. Open YouTube
3. The extension automatically starts tracking
4. Open the dashboard anytime to view metrics

---

## 🆘 Troubleshooting

### "python: can't open file 'app.py'"

**Problem:** You're not in the correct directory.

**Solution:**
```bash
cd C:\Users\santa\OneDrive\Pictures\scrolln't\server
python app.py
```

### "pip is not recognized"

**Problem:** Python is not in your PATH.

**Solution:**
1. Reinstall Python and check "Add Python to PATH"
2. Or use the full path to pip:
```bash
C:\Python39\Scripts\pip install -r requirements.txt
```

### Extension Won't Load

**Problem:** Chrome can't load the extension.

**Solution:**
1. Ensure you're loading from: `C:\Users\santa\OneDrive\Pictures\scrolln't\extension`
2. NOT from: `C:\Users\santa\OneDrive\Pictures\doomscroll-guard\extension` (old path)
3. Refresh the extension in `chrome://extensions/`

### Models Not Found

**Problem:** Server error "Model not found"

**Solution:**
1. Train the models first: `cd ml && python train.py`
2. Verify files exist in `ml/models/`

### Server Already Running

**Problem:** "Address already in use" error

**Solution:**
1. Close the existing server window
2. Or kill the process using Task Manager
3. Then start the server again

---

## 📝 Project Structure Reference

```
scrolln't/
│
├── extension/           ← Load this in Chrome
│   ├── manifest.json
│   ├── background.js
│   ├── content.js
│   ├── popup.html
│   ├── popup.js
│   ├── styles.css
│   ├── break-mode.html
│   ├── break-mode.js
│   ├── break-mode.css
│   ├── assets/
│   │   ├── icons/
│   │   ├── stages/
│   │   └── break-illustrations/
│   └── sounds/
│
├── server/              ← Start app.py here
│   ├── app.py
│   ├── predictor.py
│   └── session_manager.py
│
├── ml/                  ← Train models here
│   ├── train.py
│   ├── preprocessing.py
│   ├── evaluate.py
│   └── models/
│
├── data/                ← Place training_data.csv here
│   └── training_data.csv
│
├── requirements.txt
└── README.md
```

---

## 🎓 Quick Reference Commands

### Install Dependencies
```bash
cd C:\Users\santa\OneDrive\Pictures\scrolln't
pip install -r requirements.txt
```

### Train Models
```bash
cd C:\Users\santa\OneDrive\Pictures\scrolln't\ml
python train.py
```

### Start Server
```bash
cd C:\Users\santa\OneDrive\Pictures\scrolln't\server
python app.py
```

### Evaluate Models
```bash
cd C:\Users\santa\OneDrive\Pictures\scrolln't\ml
python evaluate.py
```

---

## ✨ You're All Set!

The Scrolln't extension is now fully configured and ready to use.

**For more information, see [README.md](README.md)**

---

<div align="center">

**Happy mindful browsing! 🌿**

</div>
