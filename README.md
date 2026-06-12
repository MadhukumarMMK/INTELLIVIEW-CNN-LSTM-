# IntelliView – Adaptive Interview Simulator using Multimodal Speech Emotion Recognition

## Overview

IntelliView is an AI-powered adaptive interview simulation platform that dynamically adjusts interview difficulty based on a candidate's performance and emotional state.

Unlike traditional interview preparation tools, IntelliView combines Speech Emotion Recognition, Facial Emotion Detection, Reinforcement Learning, and GPT-based evaluation to create a personalized interview experience.

---

## Problem Statement

Most existing interview preparation systems:

- Use fixed difficulty questions
- Ignore candidate emotions and confidence levels
- Evaluate only textual answers
- Do not provide adaptive learning experiences

As a result, candidates are often under-challenged or overwhelmed.

IntelliView addresses this gap by combining:

- Speech Emotion Recognition (SER)
- Facial Emotion Detection
- Reinforcement Learning
- GPT-based Answer Evaluation

to create a dynamic and adaptive interview simulator.

---

## Key Features

### Speech Emotion Recognition

- Audio preprocessing using Librosa
- MFCC feature extraction
- CNN + LSTM based emotion classification
- Detects 8 emotions:
  - Neutral
  - Calm
  - Happy
  - Sad
  - Angry
  - Fearful
  - Disgust
  - Surprised

### Facial Emotion Recognition

- Real-time webcam emotion detection
- Implemented using face-api.js
- Continuous emotion monitoring during interviews

### GPT-Based Answer Evaluation

- Evaluates candidate responses
- Scores answers from 0–100
- Considers:
  - Accuracy
  - Relevance
  - Completeness
  - Depth of understanding

### Adaptive Difficulty Engine

Q-Learning policy decides the next question difficulty:

- Easier
- Similar
- Harder

Based on:

- Answer Accuracy
- Confidence Score
- Emotion Recognition Results

---

## System Architecture

```text
Frontend (React + face-api.js)
            |
            v
Backend (Node.js + Express)
            |
            v
ML Service (Flask + TensorFlow)
            |
            v
MongoDB
```

### Frontend

- React 19
- face-api.js
- Web Speech API
- MediaRecorder API

### Backend

- Node.js
- Express.js
- JWT Authentication
- OpenAI API Integration

### ML Service

- Python
- Flask
- TensorFlow / Keras
- Librosa
- NumPy

### Database

- MongoDB

---

## Machine Learning Pipeline

### Sensory Layer

**sensory.py**

Responsibilities:

- Receive audio recording
- Remove silence
- Normalize audio
- Prepare waveform for processing

```text
Voice Input
     ↓
Librosa Processing
     ↓
Clean Audio
```

### Perception Layer

**perception.py**

Responsibilities:

- Extract MFCC features
- Predict emotion using CNN + LSTM

```text
Audio
  ↓
MFCC Extraction
  ↓
CNN + LSTM
  ↓
Emotion Prediction
```

Input Shape:

```text
40 MFCC Features × 100 Time Steps
```

### Cognition Layer

Responsibilities:

- Combine emotion confidence
- Combine answer accuracy
- Select next difficulty using Q-Learning

```text
Emotion + Accuracy
          ↓
      Q-Table
          ↓
Adaptive Action
```

Actions:

```text
EASIER
SIMILAR
HARDER
```

---

## CNN + LSTM Emotion Recognition Model

### Model Architecture

```text
Input MFCC
      ↓
Conv2D Layer
      ↓
Conv2D Layer
      ↓
LSTM Layer
      ↓
LSTM Layer
      ↓
Dense Output Layer
      ↓
8 Emotion Classes
```

### Output Emotions

- Neutral
- Calm
- Happy
- Sad
- Angry
- Fearful
- Disgust
- Surprised

---

## Reinforcement Learning Policy

### State Representation

Accuracy Levels:

- LOW
- MEDIUM
- HIGH

Confidence Levels:

- LOW
- MEDIUM
- HIGH

### Action Space

- EASIER
- SIMILAR
- HARDER

### Learned Policy

| Accuracy | Low Confidence | Medium Confidence | High Confidence |
|-----------|---------------|------------------|----------------|
| High | Similar | Similar | Harder |
| Medium | Easier | Similar | Similar |
| Low | Easier | Easier | Easier |

---

## Project Structure

```bash
IntelliView/
│
├── frontend/
│   ├── src/
│   ├── components/
│   ├── pages/
│   └── services/
│
├── backend/
│   ├── routes/
│   ├── controllers/
│   ├── middleware/
│   └── models/
│
├── mlservices/
│   ├── app.py
│   ├── sensory.py
│   ├── perception.py
│   ├── q_learning.py
│   ├── model/
│   └── qtable/
│
├── database/
│
└── README.md
```

---

## Interview Flow

```text
Candidate Login
        ↓
Create Interview
        ↓
Question Generated
        ↓
Answer Recorded
        ↓
Speech Emotion Analysis
        ↓
Face Emotion Analysis
        ↓
GPT Answer Evaluation
        ↓
Q-Learning Decision
        ↓
Next Question Generated
        ↓
Interview Completed
```

---

## Current Implementation Status

### Completed

- React Frontend
- Node.js Backend
- Flask ML Service
- JWT Authentication
- MongoDB Integration
- CNN + LSTM Emotion Model
- Q-Learning Policy
- GPT-Based Evaluation
- Speech-to-Text Integration
- Webcam Emotion Detection
- End-to-End Interview Pipeline

---

## Future Enhancements

### Interview Analytics Dashboard

- Detailed performance reports
- Emotion timeline visualization
- Improvement recommendations

### Real Audio Dataset Training

Planned datasets:

- RAVDESS
- TESS

### Multi-Language Support

- English
- Telugu
- Hindi
- Tamil

### Admin Dashboard

- User analytics
- Interview statistics
- Emotion trend analysis

### UI Improvements

- Better responsiveness
- Improved user experience
- Enhanced visualizations

---

## Research Contributions

This project integrates:

- Speech Emotion Recognition
- Facial Emotion Recognition
- Reinforcement Learning
- Large Language Models
- Adaptive Learning Systems

into a unified intelligent interview simulation platform.

---

## Author

**Madhukumar Munjuluri**

M.Tech (CSE - AI & ML)  
SRM University AP

**Guide:** Dr. Karthikeyan V

---

## License

This project is developed for academic and research purposes.
