<div align="center">

# 🌿 Scrolln't

### AI-Powered Behavioral Stage Estimation for YouTube

**An Educational AI Capstone Project**

Real-time monitoring of YouTube viewing habits with machine learning-based behavioral stage estimation and calming Break Mode interventions.

---

**⚠️ IMPORTANT:** This is an **educational project** and **NOT** a medical or psychological diagnostic system. It estimates behavioral stages related to prolonged viewing patterns, not addiction or any medical condition.

---

</div>

## 📋 Table of Contents

- [✨ Features](#-features)
- [🏗️ Architecture](#️-architecture)
- [🤖 AI Pipeline](#-ai-pipeline)
- [🚀 Quick Start](#-quick-start)
- [📦 Installation](#-installation)
- [🎓 Training the Models](#-training-the-models)
- [▶️ Running the Application](#️-running-the-application)
- [🔌 Chrome Extension Setup](#-chrome-extension-setup)
- [⚙️ How It Works](#️-how-it-works)
- [🎯 Stage System](#-stage-system)
- [🧘 Break Mode](#-break-mode)
- [📊 Data Collection](#-data-collection)
- [🔧 Troubleshooting](#-troubleshooting)
- [🔒 Privacy](#-privacy)

---

## ✨ Features

- **🔍 Real-Time Tracking** - Monitors YouTube viewing behavior every second
- **🤖 Three-Model Ensemble** - Random Forest, Gradient Boosting, and Extra Trees
- **📈 Monotonic Stage Progression** - Stages only increase during sessions
- **🎨 Six Behavioral Stages** - From normal viewing to extreme doomscrolling
- **🧘 Break Mode** - Four calming themes with countdown timer and activities
- **🎵 Ambient Audio** - Theme-specific audio for each Break Mode theme
- **💾 Stage-Transition Data Saving** - Automatically saves data for model retraining
- **🎯 Visual Interventions** - Stage-specific popups with quotes and illustrations
- **🔊 Warning Sounds** - Audio alerts for higher stages
- **📊 Interactive Dashboard** - View metrics, model info, and confusion matrices

---

## 🏗️ Architecture

```
YouTube
    ↓
Chrome Extension (Content Script)
    ↓
Live Session Metrics (every second)
    ↓
Local Python API Server (Flask)
    ↓
3 Trained ML Models
    ↓
3 Stage Predictions
    ↓
Average Predictions
    ↓
Round Average (half-up rounding)
    ↓
Enforce Monotonic Stage Progression
    ↓
Final Current Stage
    ↓
Chrome Extension UI + Warnings
    ↓
Stage-Transition Data Saved to CSV
```

### Project Structure

```
scrolln't/
│
├── extension/
│   ├── manifest.json          # Chrome Extension manifest (V3)
│   ├── background.js          # Service worker for API communication
│   ├── content.js             # YouTube tracking and live counter
│   ├── popup.html             # Dashboard UI
│   ├── popup.js               # Dashboard logic
│   ├── styles.css             # All styling
│   ├── break-mode.html        # Break Mode interface
│   ├── break-mode.js          # Break Mode logic
│   ├── break-mode.css         # Break Mode styling
│   ├── assets/
│   │   ├── icons/             # SVG icons
│   │   ├── stages/            # Stage illustrations (stage0.svg - stage5.svg)
│   │   └── break-illustrations/ # Break Mode wallpapers (4 themes)
│   └── sounds/                # MP3 files
│       ├── stage2.mp3
│       ├── stage3.mp3
│       ├── stage4.mp3
│       ├── sagegarden.mp3
│       ├── lavendardream.mp3
│       ├── oceanbreeze.mp3
│       └── warmsand.mp3
│
├── server/
│   ├── app.py                 # Flask API server
│   ├── predictor.py           # ML prediction logic
│   └── session_manager.py     # Session state and CSV saving
│
├── ml/
│   ├── train.py               # Model training script
│   ├── preprocessing.py       # Data cleaning and preparation
│   ├── evaluate.py            # Model evaluation utilities
│   └── models/                # Saved trained models
│
├── data/
│   └── training_data.csv      # Training dataset (you provide this)
│
├── requirements.txt           # Python dependencies
└── README.md                  # This file
```

---

## 🤖 AI Pipeline

### 1. Problem Identification
Monitor YouTube viewing behavior to estimate scrolling intensity and provide behavioral interventions.

### 2. Data Collection
Training data includes:
- Session duration
- Number of unique videos watched
- Number of unique Shorts watched
- Scroll actions
- Late-night viewing flag
- Active viewing time
- Breaks taken
- Time since last break
- Derived rates (scroll rate, short rate)
- Behavioral stage (target variable)

### 3. Data Preprocessing
- Loading and validating CSV data
- Handling missing values
- Ensuring numeric types
- Removing invalid entries

### 4. Feature Engineering
**Features used for ML training:**
- Duration
- Videos
- Shorts
- Scrolls
- LateNight
- ActiveViewingTime
- Breaks
- TimeSinceLastBreak
- ScrollRate
- ShortRate

**Excluded features (to prevent data leakage):**
- SessionID (used only for grouped splitting)
- Timestamp (used only for tracking)
- RiskScore (if present, to avoid target leakage)

### 5. Supervised Machine Learning
Three classification models:
1. **Random Forest Classifier** - Ensemble of decision trees
2. **Gradient Boosting Classifier** - Sequential ensemble with boosting
3. **Extra Trees Classifier** - Extremely randomized trees

### 6. Model Training
- **Data Splitting:** Uses `GroupShuffleSplit` to split by SessionID
  - **CRITICAL:** Prevents data leakage by ensuring all rows from the same session stay in either train or test set
- **Training:** Each model trained on the training set
- **Evaluation:** Models evaluated on held-out test set

### 7. Model Evaluation
For each model:
- Accuracy
- Precision (weighted average)
- Recall (weighted average)
- F1 Score (weighted average)
- Confusion Matrix
- Training/test sample counts

### 8. Ensemble Prediction
1. All three models make independent predictions
2. Predictions are averaged
3. Average is rounded using half-up rounding
4. Example: (3 + 3 + 2) / 3 = 2.67 → Stage 3

### 9. Real-Time Inference
- Chrome extension collects metrics every second
- Metrics sent to Python API
- API returns ensemble prediction
- Current stage updated with monotonic progression

### 10. Behavioral Intervention
When stage increases, popup appears with:
- Stage number and title
- Original anti-doomscrolling quote
- Stage-specific illustration
- Explanation of the stage
- Action buttons

### 11. Stage-Transition Data Collection
- **CRITICAL:** Data saved ONLY when entering a NEW stage
- Stage 0 baseline saved at session start
- Subsequent stages save one row each
- Same stage for extended time does NOT create duplicate rows
- Data appended to training CSV for future retraining

---

## 🚀 Quick Start

### For Your Current System

```bash
# Start the Python server
cd C:\Users\santa\OneDrive\Pictures\scrolln't\server
python app.py

# Or use the batch file (easiest)
C:\Users\santa\OneDrive\Pictures\scrolln't\server\start_server.bat
```

Then load the extension from:
```
C:\Users\santa\OneDrive\Pictures\scrolln't\extension
```

### For a New System

1. Copy the entire `scrolln't` folder to the new system
2. Install Python dependencies
3. Train the ML models
4. Start the server
5. Load the Chrome extension

See [Installation](#-installation) for detailed steps.

---

## 📦 Installation

### Prerequisites

- Python 3.8 or higher
- Google Chrome browser
- Your training data CSV file
- MP3 sound files (optional - system works without them)

### Step 1: Install Python Dependencies

```bash
cd C:\Users\santa\OneDrive\Pictures\scrolln't
pip install -r requirements.txt
```

This installs:
- scikit-learn
- pandas
- numpy
- flask
- flask-cors
- joblib

### Step 2: Place Your Training Data

Place your `training_data.csv` in the `data/` directory:

```
C:\Users\santa\OneDrive\Pictures\scrolln't\data\training_data.csv
```

**Expected CSV columns:**
- SessionID
- Timestamp
- Duration
- Videos
- Shorts
- Scrolls
- LateNight
- ActiveViewingTime
- Breaks
- TimeSinceLastBreak
- ScrollRate
- ShortRate
- Stage

*(Optionally: RiskScore - will be preserved but not used as a feature)*

### Step 3: Add Sound Files (Optional)

**Sound files are completely optional.** The system works perfectly without them. You can skip this step entirely.

If you DO want audio support, you must add your own MP3 files with these specific filenames:

#### Warning Sounds (for Stage Popups)

Place these files in `extension/sounds/` (you can use any MP3 files and rename them to match):

```
extension/sounds/stage2.mp3
extension/sounds/stage3.mp3
extension/sounds/stage4.mp3
```

**Note:** Stages 2, 3, 4 and 5 use visual as well as audio warnings (if files are present).

#### Break Mode Ambient Audio

Place these files in `extension/sounds/` (you can use any MP3 files and rename them to match):

```
extension/sounds/sagegarden.mp3      # For Sage Garden theme
extension/sounds/lavendardream.mp3  # For Lavender Dream theme
extension/sounds/oceanbreeze.mp3    # For Ocean Breeze theme
extension/sounds/warmsand.mp3       # For Warm Sand theme
```

**Note:** Without these files, Break Mode will still work but will not have ambient audio.

**TL;DR:** Skip this step if you don't have MP3 files. If you do, rename them to match the filenames above.
---

## 🎓 Training the Models

### Train All Three Models

```bash
cd C:\Users\santa\OneDrive\Pictures\scrolln't\ml
python train.py
```

This will:
1. Load `training_data.csv`
2. Validate and clean the data
3. Split data by SessionID (preventing data leakage)
4. Train Random Forest, Gradient Boosting, and Extra Trees models
5. Evaluate all models
6. Save trained models to `ml/models/`
7. Save evaluation metrics to `ml/models/model_metrics.json`

### View Model Evaluation

```bash
cd C:\Users\santa\OneDrive\Pictures\scrolln't\ml
python evaluate.py
```

This displays:
- Accuracy, Precision, Recall, F1 for each model
- Confusion matrices
- Feature importance

---

## ▶️ Running the Application

### Start the Python ML Server

**Option A: With visible console (for debugging)**
```bash
cd C:\Users\santa\OneDrive\Pictures\scrolln't\server
python app.py
```

**Option B: Without visible console (background)**
```bash
cd C:\Users\santa\OneDrive\Pictures\scrolln't\server
pythonw app.py
```

**Option C: Double-click the batch file (easiest)**
```
C:\Users\santa\OneDrive\Pictures\scrolln't\server\start_server.bat
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

**Keep this running** while using the extension.

---

## 🔌 Chrome Extension Setup

### Step 1: Open Chrome Extensions

1. Open Google Chrome
2. Navigate to `chrome://extensions/`
3. Enable "Developer mode" (toggle in top-right corner)

### Step 2: Load the Extension

1. Click "Load unpacked"
2. Navigate to: `C:\Users\santa\OneDrive\Pictures\scrolln't\extension`
3. Click "Select Folder"

The extension should now appear in your extensions list.

### Step 3: Verify Installation

1. Click the extension icon in your Chrome toolbar
2. The dashboard should open
3. Check that "Server Online" is shown in green

If it shows "Server Offline", ensure the Python server is running.

---

## ⚙️ How It Works

### Real-Time Tracking

When you visit YouTube:

1. **Session Start:** Extension starts a new session and saves Stage 0 baseline
2. **Metric Collection:** Every second, the extension updates:
   - Duration (elapsed time)
   - Unique videos watched (using video IDs)
   - Unique Shorts watched (using short IDs)
   - Total scroll actions
   - Late-night flag (based on time of day)
   - Active viewing time (time with user activity)
   - Breaks (periods of inactivity > 30 seconds)
   - Time since last break
   - Scroll rate (scrolls per second)
   - Short rate (shorts per second)

3. **Prediction:** Metrics sent to Python API
4. **Ensemble:** Three models predict independently, results averaged
5. **Monotonic Progression:** Stage can only increase, never decrease during session
6. **Intervention:** If stage increases, popup appears with quote and image
7. **Data Saving:** New stage triggers one CSV row append

### Unique Content Counting

- **Videos:** Counted once per unique video ID
- **Shorts:** Counted once per unique short ID
- **Scrolls:** Every scroll action counts (even revisiting old content)

### Late Night Detection

Late night is defined as:
- 10 PM (22:00) to 6 AM (06:00)

### Break Detection

A break is counted when:
- User is inactive for > 30 seconds
- Mouse, keyboard, or scroll activity resets the timer

---

## 🎯 Stage System

There are 6 behavioral stages (0-5):

### Stage 0: Normal / Low-Intensity Viewing
- Balanced digital usage
- Quote: "Balance in digital life brings clarity to real life."
- Visual: Calm, balanced scene

### Stage 1: Mild Prolonged Scrolling
- Beginning of extended viewing
- Quote: "Awareness is the first step to mindful browsing."
- Visual: Person starting to spend more time

### Stage 2: Elevated Scrolling Behavior
- Increased engagement
- Quote: "Your attention deserves a break."
- Visual: More intense scrolling
- Warning: Visual only

### Stage 3: High Scrolling Intensity
- Very high screen engagement
- Quote: "Your attention deserves a pause."
- Visual: Strong engagement
- Warning: Visual + Sound (stage3.mp3)

### Stage 4: Very High/Prolonged Scrolling Intensity
- Extended viewing period
- Quote: "Step away. Your mind needs space to breathe."
- Visual: Strong intervention reminder
- Warning: Visual + Sound (stage4.mp3)

### Stage 5: Extreme Doomscrolling
- Critical stage requiring immediate action
- Quote: "The screen can wait. Your wellbeing cannot."
- Visual: Intense warning with "STOP NOW" message
- Warning: Visual + Sound (stage4.mp3)

### Monotonic Progression

**CRITICAL:** During a single session, the stage can ONLY increase.

Example:
- Previous Stage: 2, New prediction: 1 → **Current Stage remains: 2**
- Previous Stage: 2, New prediction: 3 → **Current Stage becomes: 3**

When a new session starts, stage resets to 0.

---

## 🧘 Break Mode

Break Mode is a dedicated calming interface with four selectable themes to help users take meaningful breaks from YouTube.

### Entry Points

Three ways to enter Break Mode:

1. **Live Counter Button** - Click "Take a Break" in the live counter overlay on YouTube
2. **Stage Popup Button** - Click "Take a Break" in any stage-warning popup
3. **Automatic Inactivity Prompt** - After 2 minutes of inactivity, a gentle prompt appears

### Four Calm Themes

1. **Sage Garden** 🌿
   - Sage green, muted botanical greens
   - Peaceful foliage and nature details
   - Audio: sagegarden.mp3

2. **Lavender Dream** 💜
   - Lavender, lilac, soft gradients
   - Delicate glowing particles
   - Audio: lavendardream.mp3

3. **Ocean Breeze** 🌊
   - Muted aqua, seafoam, soft teal
   - Subtle ocean waves
   - Audio: oceanbreeze.mp3

4. **Warm Sand** 🏜️
   - Cream, beige, muted sand
   - Gentle sunlight effects
   - Audio: warmsand.mp3

### Features

- **Countdown Timer** - Choose 1, 3, 5, or 10 minute breaks
- **Timer Controls** - Pause, resume, add time (+1 min, +5 min), end early
- **Circular Progress** - Visual progress ring around the timer
- **Activity Lounge** - 12 offline activities with descriptions
- **Surprise Me** - Randomly selects an activity
- **Ambient Audio** - Theme-specific MP3 audio (optional)
- **Theme Persistence** - Your chosen theme is remembered
- **Video-Aware Inactivity** - Prompt doesn't appear if video is playing

### Activities

1. Mindful Breathing
2. Eye Rest
3. Quick Stretch
4. Short Walk
5. Hydration Break
6. Read a Few Pages
7. Journal or Brain Dump
8. Doodle Something
9. Reset Your Space
10. Plan Your Next Task
11. Gratitude Moment
12. Quiet Moment

### Limitations

- Inactivity detection tracks user activity (mouse, keyboard, scroll) but may not account for passive video watching perfectly
- Break Mode does not block YouTube or force users to stay; users can exit at any time
- ML predictions are paused during Break Mode to prevent unnecessary updates

---

## 📊 Data Collection

### Stage-Transition Saving

**IMPORTANT:** Data is saved ONLY when entering a NEW stage.

Example session:
```
Session starts → Stage 0 → Save row
...time passes...
Stage 0 → Stage 1 → Save row
...time passes...
Stage 1 → Stage 2 → Save row
Stage remains 2 for 10 minutes → NO save
Stage 2 → Stage 3 → Save row
```

This results in exactly 4 rows for this session.

### CSV Structure

Each saved row contains:
- SessionID (unique UUID)
- Timestamp (ISO format)
- Duration
- Videos
- Shorts
- Scrolls
- LateNight
- ActiveViewingTime
- Breaks
- TimeSinceLastBreak
- ScrollRate
- ShortRate
- Stage

### Retraining

To retrain models with new data:

1. Ensure `training_data.csv` contains your latest data
2. Run: `python ml/train.py`
3. New models saved to `ml/models/`
4. Restart the Python server to load new models

---

## 🔧 Troubleshooting

### Server Shows "Offline"

**Problem:** Extension shows "Server Offline" in dashboard

**Solutions:**
1. Ensure Python server is running: `cd server && python app.py`
2. Check server shows "Starting Flask server on http://127.0.0.1:5000"
3. Verify no firewall is blocking localhost:5000
4. Try restarting the server

### Models Not Found

**Problem:** Server error "Model not found"

**Solutions:**
1. Train models first: `cd ml && python train.py`
2. Verify `ml/models/` contains:
   - `random_forest.joblib`
   - `gradient_boosting.joblib`
   - `extra_trees.joblib`
   - `model_metrics.json`

### Training Data Not Found

**Problem:** "Training data not found" error

**Solutions:**
1. Place `training_data.csv` in `data/` directory
2. Verify filename is exactly `training_data.csv`
3. Check CSV has required columns

### Extension Not Loading on YouTube

**Problem:** Live counter doesn't appear on YouTube

**Solutions:**
1. Refresh YouTube page after loading extension
2. Check extension is enabled in `chrome://extensions/`
3. Open browser console (F12) to check for errors
4. Verify extension has permission for YouTube URLs

### Break Mode Not Opening

**Problem:** "Take a Break" button doesn't work

**Solutions:**
1. Reload the extension in `chrome://extensions/`
2. Refresh YouTube
3. Check browser console for errors
4. Verify break-mode.html is in the extension directory

### Break Mode Audio Not Playing

**Problem:** Ambient audio doesn't play in Break Mode

**Solutions:**
1. Ensure MP3 files are in `extension/sounds/`
2. Verify filenames: `sagegarden.mp3`, `lavendardream.mp3`, etc.
3. Click "Enable Sound" button after opening Break Mode
4. Check browser console for audio loading errors

### Sound Not Playing

**Problem:** Warning sound doesn't play on stage 3/4

**Solutions:**
1. Place MP3 files in `extension/sounds/`
2. Verify filenames: `stage3.mp3`, `stage4.mp3`
3. Check browser console for audio loading errors
4. Ensure browser allows audio autoplay

---

## 🔒 Privacy

This application is designed with privacy in mind:

### Local Processing
- All ML inference happens on your local machine
- Python server runs on localhost (127.0.0.1)
- No data is sent to external servers

### Data Collection
- Only collects metrics required for behavioral estimation
- Does NOT collect:
  - Passwords
  - Unrelated browsing history
  - Personal information
  - Video titles or content
  - User account information

### YouTube-Specific
- Only tracks on YouTube pages
- Uses video IDs for uniqueness, not content
- Does not access watch history or personal data

### Training Data
- Training CSV is stored locally
- You control the data
- No cloud synchronization

---

## 🎓 Educational Purpose

This project is designed as a Class 12 AI Capstone Project to demonstrate:

- Supervised machine learning
- Ensemble methods
- Real-time inference
- Feature engineering
- Data preprocessing
- Model evaluation
- Chrome Extension development
- Full-stack application architecture

**This is NOT a medical or psychological diagnostic tool.** It estimates behavioral patterns for educational purposes only.

---

## 📄 License

This project is created for educational purposes.

---

## 👨‍💻 Credits

Created as an AI Capstone Project for demonstrating end-to-end machine learning application development. Prototype was created by Ritarko Dhara from Class XII A, DAV MODEL SCHOOL, IIT KGP (Session : 2026-27). Part of Capstone Project made by team of R Sai Keertana, Shivangi Sharma, Kaustav Naskar, Sreeti Ganguly, Ruchi Kumari, Sohana Adhikary, Soumya Sankar Mukhopadhyay, Ritarko Dhara, Uday Sharma, Shreyashree Das, Vandhana Agarwal, Tanay Roy 

---

<div align="center">

**Made with ❤️ for mindful digital habits**

</div>
