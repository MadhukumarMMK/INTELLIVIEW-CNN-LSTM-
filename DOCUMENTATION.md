# IntelliView: Adaptive Interview Simulator Using Multimodal Speech Emotion Recognition

## Complete Project Documentation

**Prepared by:** Madhu Kumar  
**Version:** 2.0  
**Date:** April 2026

---

## Table of Contents

1. [Problem Statement](#1-problem-statement)
2. [Project Description](#2-project-description)
3. [Objectives](#3-objectives)
4. [System Architecture](#4-system-architecture)
5. [Tech Stack](#5-tech-stack)
6. [Database Design](#6-database-design)
7. [Feature Catalog](#7-feature-catalog)
8. [AI/ML Models](#8-aiml-models)
9. [API Documentation](#9-api-documentation)
10. [User Interface](#10-user-interface)
11. [Admin Panel](#11-admin-panel)
12. [Security & Proctoring](#12-security--proctoring)
13. [Deployment Architecture](#13-deployment-architecture)
14. [Future Scope](#14-future-scope)

---

## 1. Problem Statement

Traditional mock interview platforms rely on static question banks with no real-time feedback, making them ineffective for genuine interview preparation. Students face the following challenges:

- **No Adaptive Difficulty:** Questions remain at a fixed level regardless of the candidate's performance, leading to either boredom or frustration.
- **No Emotional Awareness:** Existing platforms ignore the candidate's stress levels, confidence, and emotional state during the interview.
- **No Verbal Communication Analysis:** Most platforms focus on written code tests, neglecting the critical skill of verbal technical communication.
- **No Real-Time Feedback Loop:** Candidates receive feedback only after the entire session, losing the opportunity for in-session course correction.
- **No Multimodal Assessment:** Current tools evaluate only text answers, missing the rich signals from facial expressions and voice patterns.

**IntelliView addresses these gaps** by creating an adaptive interview simulator that uses Reinforcement Learning combined with multimodal emotion recognition (facial + speech) to dynamically adjust interview difficulty and provide a hyper-realistic interview experience.

---

## 2. Project Description

IntelliView is an enterprise-grade Software-as-a-Service (SaaS) platform that provides adaptive AI-powered mock interviews. The system features:

- A **3D AI Avatar Interviewer** that speaks questions aloud and reacts to the candidate's responses
- **Real-time facial emotion detection** using Face-API.js (7 emotions: angry, disgusted, fearful, happy, sad, surprised, neutral)
- **Speech confidence analysis** using Librosa audio feature extraction
- **Reinforcement Learning (RL) Adaptive Brain** that fuses facial and audio confidence to dynamically adjust question difficulty (Easy/Medium/Hard)
- **Groq LLM (Llama 3.3-70B)** for intelligent question generation and answer evaluation
- **Three interview modes:** Resume-Based, Custom Technical, and HR Behavioral
- **Tab-switch proctoring** to prevent cheating during interviews
- **Comprehensive analytics** with downloadable PDF reports and social media sharing

---

## 3. Objectives

| # | Objective | Implementation |
|---|-----------|---------------|
| 1 | Resume-based question generation | Python NLP parser extracts skills from PDF/DOCX resumes; Groq generates questions based on extracted skills |
| 2 | Custom topic selection | Hierarchical MongoDB schema: Technology -> Module -> Topic; admin-managed CRUD |
| 3 | HR behavioral interviews | Groq LLM with HR-specific prompts focusing on conflict resolution, leadership, adaptability |
| 4 | Adaptive difficulty pacing | RL Brain fuses face confidence (60%) + audio confidence (40%); adjusts Easy/Medium/Hard |
| 5 | Real-time emotion detection | Face-API.js captures 7 facial expressions every 2 seconds via webcam |
| 6 | Speech emotion analysis | Librosa extracts spectral features from audio chunks streamed via WebSocket |
| 7 | Contextual follow-up questions | Each adaptive question references the candidate's previous answer for conversational flow |
| 8 | Anti-cheat proctoring | Tab-switch detection: 1st switch = warning, 2nd switch = auto-terminate interview |
| 9 | Comprehensive reporting | Per-question accuracy, confidence, difficulty transitions, emotion pie charts, PDF export |
| 10 | Admin oversight | Dynamic question counts, interview limits, user management, hierarchy CRUD, analytics dashboard |

---

## 4. System Architecture

```
+-------------------+        +--------------------+        +----------------------+
|                   |  REST  |                    | REST   |                      |
|   FRONTEND        +------->+   BACKEND          +------->+   PYTHON AI ENGINE   |
|   (React.js)      |        |   (Node.js)        |        |   (Flask)            |
|   Port: 3000      |  WS    |   Port: 5001       |        |   Port: 5002         |
|                   +<------>+                    |        |                      |
+-------------------+        +--------------------+        +----------------------+
        |                           |                              |
        |                           |                              |
   Face-API.js               MongoDB Atlas                   Groq LLM API
   Web Speech API             (Cloud DB)                    (Llama 3.3-70B)
   MediaRecorder                                            Librosa (Audio)
```

### Data Flow

#### Interview Session Flow:
```
1. User starts interview
2. Frontend requests 1 initial question via REST -> Backend -> Python (Groq)
3. TTS reads question aloud via Web Speech API
4. User clicks Record -> SpeechRecognition captures transcript
5. MediaRecorder streams audio chunks via WebSocket to Backend
6. Face-API.js streams facial confidence every 2s via WebSocket
7. User clicks Submit:
   a. Backend calculates avg facial confidence from buffer
   b. Backend encodes audio buffer as base64
   c. Backend sends to Python: answer + face confidence + audio data
8. Python:
   a. Groq evaluates answer accuracy (0-100)
   b. Librosa analyzes audio for speech confidence
   c. RL Brain fuses: (0.6 x face) + (0.4 x audio) = fused confidence
   d. Brain decides next difficulty: score >= 80 AND confidence >= 70 -> Level Up
   e. Groq generates contextual follow-up question based on previous answer
9. Backend emits next_step_ready via WebSocket to Frontend
10. Frontend loads next question -> TTS reads -> cycle repeats
11. After all questions -> results saved to MongoDB -> redirect to Report
```

#### Resume Parsing Flow:
```
1. User uploads PDF/DOCX on Onboarding page
2. Backend receives file via Multer
3. Backend forwards to Python /api/extract-resume
4. Python extracts: name, email, phone, skills, GitHub, LinkedIn, job role, sector
5. Backend saves extracted data to User model in MongoDB
6. Frontend updates localStorage and redirects to Dashboard
```

---

## 5. Tech Stack

### Frontend
| Technology | Version | Purpose |
|-----------|---------|---------|
| React.js | 19.1.1 | UI framework |
| React Router | 7.9.3 | Client-side routing |
| Socket.io Client | 4.8.3 | Real-time WebSocket communication |
| Face-API.js | 0.22.2 | Facial expression detection (7 emotions) |
| Web Speech API | Native | Speech-to-text transcription |
| SpeechSynthesis API | Native | Text-to-speech for AI interviewer |
| Chart.js + react-chartjs-2 | 4.5.1 | Analytics charts (bar, line, doughnut, pie) |
| html2canvas + jsPDF | Latest | PDF report generation |
| Axios | 1.12.2 | HTTP client |

### Backend
| Technology | Version | Purpose |
|-----------|---------|---------|
| Node.js | 22.x | Runtime environment |
| Express.js | 5.2.1 | REST API framework |
| Socket.io | 4.8.3 | WebSocket server for real-time data |
| Mongoose | 9.4.1 | MongoDB ODM |
| JWT | 9.0.3 | Authentication tokens |
| Bcrypt.js | 3.0.3 | Password hashing |
| Multer | 2.1.1 | File upload handling |
| XLSX | 0.18.5 | Bulk user Excel parsing |

### Python AI Engine
| Technology | Version | Purpose |
|-----------|---------|---------|
| Flask | 3.0.0 | REST API server |
| Groq SDK | Latest | LLM API (Llama 3.3-70B & 3.1-8B) |
| pdfminer.six | Latest | PDF text extraction |
| docx2txt | 0.8 | DOCX text extraction |
| Librosa | Latest | Audio spectral analysis |
| NumPy / SciPy | Latest | Numerical computation |

### Database
| Technology | Purpose |
|-----------|---------|
| MongoDB Atlas | Cloud-hosted document database |

### Deployment
| Service | Component |
|---------|-----------|
| Vercel | Frontend hosting |
| Render | Backend + Python engine hosting |
| MongoDB Atlas | Database (cloud) |

---

## 6. Database Design

### Collections

#### 6.1 Users Collection
```json
{
  "roll_no": "21A91A0501",
  "first_name": "Madhu Kumar",
  "password": "$2a$10$...(bcrypt hashed)",
  "email": "madhu@gmail.com",
  "gender": "Male",
  "college": "ABC University",
  "branch": "CSE",
  "passout_year": 2025,
  "role": "student | admin",
  "mobile_number": "9876543210",
  "skills": ["React", "Node.js", "Python", "MongoDB"],
  "certifications": [
    {
      "id": "1712345678",
      "name": "AWS Cloud Practitioner",
      "filename": "cert-123-aws.pdf",
      "url": "/uploads/certs/cert-123-aws.pdf",
      "type": "application/pdf",
      "size": 245000,
      "uploadedAt": "2026-04-11T06:00:00Z"
    }
  ],
  "profile_picture": "/uploads/avatars/avatar-123.jpg",
  "resume_path": "uploads/resumes/resume-123.pdf",
  "github_url": "https://github.com/madhukumar",
  "linkedin_url": "https://linkedin.com/in/madhukumar"
}
```

#### 6.2 Interviews Collection
```json
{
  "roll_no": "21A91A0501",
  "technology_name": "React.js",
  "level": "Medium",
  "questions_count": "3",
  "start_date_time": "2026-04-11T06:23:44Z",
  "status": 2,
  "overall_score": 72,
  "question_details": [
    {
      "question": "How does React's virtual DOM reconciliation work?",
      "answer": "React creates a lightweight copy of the real DOM...",
      "was_skipped": false,
      "accuracy": 80,
      "feedback": "Good explanation but missed fiber architecture.",
      "difficulty": "Medium",
      "new_difficulty": "Hard",
      "fused_confidence": 76,
      "audio_confidence": 70
    }
  ],
  "emotions": {
    "emotions": {
      "angry": 0.02,
      "disgust": 0.01,
      "fear": 0.05,
      "happy": 0.15,
      "sad": 0.03,
      "surprise": 0.04,
      "neutral": 0.70
    }
  },
  "overall_analysis": [{
    "overall_score": 72,
    "total_questions": 3,
    "answered": 2,
    "skipped": 1
  }]
}
```

#### 6.3 AdminSettings Collection
```json
{
  "max_interviews": 6,
  "default_password": "intelliview@123",
  "questions_resume": 10,
  "questions_custom": 10,
  "questions_hr": 8,
  "starting_difficulty": "Medium"
}
```

#### 6.4 Technologies / Modules / Topics Collections
```
Technology: { technology_name, technology_category, status }
Module: { technology (ref), module_name, status }
Topic: { technology (ref), module (ref), topic_name, status }
```

---

## 7. Feature Catalog

### 7.1 Authentication & User Management
- Roll number + password login with JWT authentication
- Role-based routing: admin -> /admin, student -> /dashboard
- Admin can create users individually or via Excel bulk upload
- Default password configurable by admin
- Users can change password from profile
- Password hashing with bcrypt

### 7.2 Resume Parsing & Onboarding
- Upload PDF or DOCX resume
- Python NLP engine extracts: name, email, phone, skills, GitHub, LinkedIn, job role, sector
- 150+ technology skills database for matching
- Smart name extraction (avoids city names, addresses, job titles)
- Extracted data auto-populates user profile
- Resume stored for later download

### 7.3 Interview Modes

#### Resume-Based Interview
- Questions generated from the candidate's extracted resume skills
- Groq LLM focuses on the candidate's strongest skill area
- Theoretical/architectural questions only — no code syntax

#### Custom Technical Interview
- User selects: Technology -> Module -> Topic
- Admin manages the hierarchy via CRUD interface
- Questions tailored to the specific topic selected

#### HR Behavioral Interview
- Situational and behavioral questions
- Focus: conflict resolution, leadership, teamwork, adaptability
- STAR method encouraged
- No technical or coding questions

### 7.4 Interview Arena (Core Feature)

#### 3D AI Avatar Interviewer
- SVG-based human bust with realistic proportions
- Animated features: eye blinks, lip sync, head sway, idle breathing
- Three states: Speaking (sound waves + lip animation), Thinking (pupils shift), Listening (idle)
- Sound wave rings radiate when speaking

#### Text-to-Speech (TTS)
- AI reads each question aloud via Web Speech API
- Record button disabled until TTS finishes (TTS-gated interaction)
- Chrome autoplay policy handled with silent utterance unlock

#### Speech Recognition
- Web Speech API continuous mode with interim results
- Transcript displayed in real-time in the Transcript Card
- transcriptRef ensures latest text is always sent (not stale React state)

#### Audio Streaming
- MediaRecorder captures audio-only stream
- 1-second chunks streamed via WebSocket to backend in real-time
- Backend buffers chunks, encodes as base64 on submit
- Python analyzes via Librosa spectral features

#### Facial Emotion Detection
- Face-API.js with TinyFaceDetector model
- Detects 7 emotions every 2 seconds
- Confidence = (neutral + happy) * 100
- Streamed to backend via WebSocket for RL brain

#### Live Analysis Panel
- Confidence bar (green/yellow/red)
- Accuracy bar (updated after each answer)
- Clarity bar (from audio analysis)
- Real-time visual feedback for the candidate

#### Progress Tracking
- Visual dot stepper: green (completed), blue (current), yellow (skipped)
- Question counter: "Q3/10"
- Session timer

### 7.5 Adaptive RL Brain (Reinforcement Learning)

#### Multimodal Confidence Fusion
```
fused_confidence = (0.6 x face_confidence) + (0.4 x audio_confidence)
```

#### Difficulty Policy
| Condition | Action |
|-----------|--------|
| accuracy >= 80 AND confidence >= 70 | Level UP (Easy -> Medium -> Hard) |
| accuracy <= 40 OR confidence <= 30 | Level DOWN (Hard -> Medium -> Easy) |
| Skipped question | STAY at current level |
| Otherwise | STAY at current level |

#### Contextual Question Generation
- Each adaptive question references the candidate's previous answer
- If candidate was vague, follow-up asks for clarification
- If candidate was wrong, follow-up gently redirects
- If candidate skipped, follow-up pivots to a related easier topic
- Questions kept concise: max 1-2 sentences, under 30 words

### 7.6 Pre-Interview Instructions
- 10-second countdown with animated circular progress ring
- Mode-specific instructions (10 points each for Resume/HR/Custom)
- Skip button available at any time
- Interview only starts after instructions are dismissed
- No camera/mic/WebSocket activated during instructions

### 7.7 Tab-Switch Proctoring (Anti-Cheat)
| Switch | Action |
|--------|--------|
| 1st tab switch | Warning banner: red-orange gradient, flashing animation. TTS resumes question when user returns. Counter shows "Warnings: 1/2" |
| 2nd tab switch | Interview auto-terminated. Interview record deleted from DB. Error notification displayed. Redirect to dashboard. |

### 7.8 Interview Limit System
- Admin sets maximum interviews per user (default: 6)
- Only completed interviews (status=2) count toward the limit
- Ending/abandoning an interview mid-way deletes the record — no deduction
- Stuck in-progress interviews older than 2 hours auto-cleaned
- Dashboard shows "X of Y interviews completed"
- MyReports shows "Delete worst to free an interview" when limit reached

### 7.9 Report & Analytics

#### Per-Interview Report
- Top stats: Accuracy %, Confidence %, Clarity %
- Accordion Q&A: each question has unique accent color from 10-color palette
  - Blue question block, purple answer block, green feedback block
  - Smooth pixel-perfect height animation (ref-based, not max-height hack)
- Emotion analysis pie chart
- Difficulty-wise breakdown bars
- PDF download: expands all accordions, hides buttons, multi-page support
- Social media sharing: LinkedIn, Twitter/X, WhatsApp, Copy Link

#### Dashboard Statistics
- Total completed interviews
- Average accuracy across all interviews
- Best score with interview details
- Average facial confidence

### 7.10 Profile Management
- Profile picture upload with click-to-change avatar
- Editable fields: name, email, mobile, college, branch, passout year
- GitHub and LinkedIn URL fields with clickable social buttons
- Best performance card with score and "View Report" link
- Resume download and re-upload
- Change password (current + new + confirm)
- Admin profile shows Quick Access panel + Role Permissions card

### 7.11 DigiLocker (Certificate Management)
- Dedicated page at /digilocker with Grid/List view toggle
- Upload certificates with optional custom name
- Supports PDF, JPG, PNG, WEBP
- Click to preview: images render inline, PDFs in iframe
- Download individual certificates
- Delete with confirmation modal
- File metadata: upload date, file size, type label
- Certificates are user-managed only — never overwritten by resume parsing

### 7.12 Theme System
| Theme | Background | Surface | Border | Text |
|-------|-----------|---------|--------|------|
| Deep Ocean (dark, default) | #0a0f1a | #121a2e | #1e2d4a | #e2e8f0 |
| Ocean Day (light) | #f0f4ff | #ffffff | #c7d7f5 | #0a1628 |

- Toggle in navbar with custom ocean wave SVG icons
- Persisted to localStorage
- Smooth 0.35s transition on all elements
- All pages use CSS variables — no hardcoded colors

### 7.13 Notification System
- Custom toast notifications: success (green), error (red), warning (yellow), info (blue)
- Animated slide-in from right with auto-dismiss
- Confirm modal with dark overlay, blur backdrop, Cancel/Confirm buttons
- Replaced all browser alert() and confirm() calls across the entire codebase

---

## 8. AI/ML Models

### 8.1 Groq LLM (Llama Models)

| Model | Purpose | Temperature |
|-------|---------|-------------|
| Llama 3.3-70B Versatile | Initial question generation, Answer evaluation | 0.7 (generation), 0.1 (evaluation) |
| Llama 3.1-8B Instant | Adaptive follow-up question generation | Default |

**Question Generation Constraints:**
- No code syntax or programming tasks
- Focus on "Why" and "How" — architecture, trade-offs, design decisions
- Max 1-2 sentences, under 30 words (conciseness rule)
- Must reference candidate's previous answer for conversational flow
- Returns structured JSON response

**Answer Evaluation:**
- Scores accuracy 0-100
- Provides single-sentence feedback
- Handles skipped/short answers (score = 0)
- Evaluates verbal explanation quality, not code correctness

### 8.2 Face-API.js (Facial Emotion Recognition)

| Component | Details |
|-----------|---------|
| Detector | TinyFaceDetector (lightweight, real-time capable) |
| Expressions | 7 emotions: angry, disgusted, fearful, happy, sad, surprised, neutral |
| Interval | Every 2 seconds |
| Confidence Formula | (neutral + happy) * 100 |
| Transport | WebSocket streaming to backend |

### 8.3 Librosa Audio Analysis

| Feature | Details |
|---------|---------|
| Input | Base64-encoded WebM audio from MediaRecorder |
| Analysis | Spectral contrast, RMS energy, zero crossing rate |
| Confidence Score | 0-100 based on energy (loudness), clarity (ZCR), contrast (articulation) |
| Fallback | File-size heuristic when Librosa/ffmpeg unavailable |

### 8.4 Adaptive Brain (RL Policy)

| Input | Weight | Source |
|-------|--------|--------|
| Face Confidence | 60% | Face-API.js via WebSocket |
| Audio Confidence | 40% | Librosa via base64 audio |
| Answer Accuracy | Binary threshold | Groq LLM evaluation |
| Skip Status | Override | Frontend payload |

**Output:** Next difficulty level (Easy/Medium/Hard) + fused confidence score

### 8.5 Resume Parser (NLP)

| Feature | Technique |
|---------|-----------|
| Text Extraction | pdfminer.six (PDF), docx2txt (DOCX) |
| Name Detection | Two-strategy: near-email heuristic + fallback scan with 200+ blocklist words |
| Skill Matching | 150+ skill database with regex word-boundary matching |
| Phone Extraction | Multi-format regex (Indian +91, international) |
| URL Extraction | Regex for LinkedIn and GitHub profile links |
| Role Detection | Keyword matching against 9 role categories |
| Sector Detection | Keyword matching against 5 sector categories |

---

## 9. API Documentation

### 9.1 User Routes (`/api/user`)

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/login` | Authenticate user, return JWT token |
| POST | `/upload-resume` | Upload resume, parse via Python, save skills |
| GET | `/profile/:roll_no` | Get user profile (excludes password) |
| PUT | `/profile/:roll_no` | Update profile details |
| POST | `/upload-avatar` | Upload profile picture |
| POST | `/upload-resume-file` | Upload/replace resume file |
| GET | `/download-resume/:roll_no` | Download stored resume |
| POST | `/upload-certification` | Upload certificate to DigiLocker |
| DELETE | `/certification/:roll_no/:cert_id` | Delete certificate |
| PUT | `/change-password` | Change user password |

### 9.2 Interview Routes (`/api/interviews`)

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/create` | Start new interview session |
| PUT | `/update/:id` | Save final results + mark completed |
| GET | `/report/:id` | Get single interview report |
| GET | `/history/:roll_no` | Get user's interview history |
| DELETE | `/delete/:id` | Delete interview record |
| POST | `/generate-questions` | Proxy to Python for initial question |

### 9.3 Admin Routes (`/api/admin`)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/settings` | Get admin configuration |
| PUT | `/settings` | Update admin configuration |
| GET | `/analytics/interviews` | Paginated interview analytics |
| GET | `/analytics/summary` | Lightweight summary for charts |
| GET | `/users` | List all users (paginated + search) |
| POST | `/users/create` | Create single user |
| POST | `/users/bulk-upload` | Bulk create users from Excel |
| DELETE | `/users/:roll_no` | Delete user + their data |
| PUT | `/users/:roll_no/reset-password` | Reset to default password |

### 9.4 General Routes (`/api/general`)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/technologies` | List all technologies |
| POST | `/modules-by-tech` | Get modules for a technology |
| POST | `/topics-by-module` | Get topics for a module |
| POST/PUT/DELETE | `/technologies/:id` | CRUD for technologies |
| POST/PUT/DELETE | `/modules/:id` | CRUD for modules |
| POST/PUT/DELETE | `/topics/:id` | CRUD for topics |

### 9.5 Python Engine Routes (Port 5002)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/` | Health check |
| POST | `/api/extract-resume` | Parse resume PDF/DOCX |
| POST | `/api/generate-questions` | Generate initial question |
| POST | `/api/generate-adaptive-step` | Evaluate answer + generate next question |

### 9.6 WebSocket Events

| Event | Direction | Payload |
|-------|-----------|---------|
| `start_session` | Client -> Server | { roll_no, mode } |
| `facial_data` | Client -> Server | { confidence, expressions } |
| `audio_chunk` | Client -> Server | Binary audio data |
| `submit_multimodal_answer` | Client -> Server | { lastQuestion, lastAnswer, difficulty, was_skipped, history, ... } |
| `next_step_ready` | Server -> Client | { next_question, new_difficulty, accuracy, feedback, fused_confidence } |
| `error` | Server -> Client | { message } |

---

## 10. User Interface

### 10.1 Login Page
- Roll number + password authentication
- Role-based redirect: admin -> /admin, student -> /dashboard or /onboarding

### 10.2 Onboarding Page
- Resume upload (PDF/DOCX)
- Real-time skill extraction display
- Auto-populates profile with extracted name, skills, email, phone

### 10.3 Dashboard
- Greeting with user's name
- Interview limit progress bar (X of Y completed)
- Stats: Total interviews, Avg accuracy, Best score, Confidence avg
- Three interview mode cards: Resume-Based, Custom Selection, HR Round

### 10.4 Interview Setup
- Mode-specific configuration
- Custom mode: Technology -> Module -> Topic dropdown hierarchy
- Hardware diagnostics: camera and microphone status check
- Difficulty selection

### 10.5 Instructions Screen
- Mode-specific 10-point instruction list
- 10-second countdown with circular progress ring
- Skip button available at any time
- Interview resources don't load until instructions are dismissed

### 10.6 Interview Arena
- Two-column layout: AI Interviewer (left) + Camera & Analysis (right)
- 3D human avatar with lip sync, eye blinks, head movement
- Question card with "Question X" label
- Transcript card showing real-time speech-to-text
- Camera preview with REC indicator
- Live analysis bars: Confidence, Accuracy, Clarity
- Adaptive engine status indicator
- Professional control buttons: Record, Submit, Skip, End
- Tab-switch warning banner

### 10.7 Report Page
- Accuracy, Confidence, Clarity top stats with color indicators
- Accordion Q&A with 10-color palette per question
- Emotion analysis pie chart
- Difficulty-wise breakdown
- PDF download button
- Social media share dropdown (LinkedIn, Twitter, WhatsApp, Copy Link)

### 10.8 My Reports
- Grid of completed interview cards
- Best interview ribbon badge (top-left corner)
- Worst interview delete badge
- Score circle with percentage
- "Delete worst to free an interview" action
- Dynamic limit display from admin settings

### 10.9 Profile Page
- Avatar with click-to-upload
- Editable personal details
- Social links (GitHub, LinkedIn)
- Best performance card
- Resume management (download/upload)
- DigiLocker preview with "View All" link
- Change password section
- Admin-specific: Quick Access panel + Role Permissions

### 10.10 DigiLocker Page
- Grid/List view toggle
- Upload with custom name
- Certificate cards with thumbnail, metadata, actions
- Split-view preview panel (image inline, PDF in iframe)
- Download and delete per certificate

---

## 11. Admin Panel

### 11.1 Overview Tab
- Stats cards: Total Users, Interviews Completed, Avg Score, Top Performers
- Time-based cards: Today, Yesterday, This Week, This Month, This Year
- Weekly activity bar chart (7 days)
- Monthly trend line chart (6 months) with dual axes
- Score distribution doughnut chart (Excellent/Good/Average/Poor)
- Quick settings summary

### 11.2 Users Tab
- Single user registration form (roll_no, name, email, college, branch, year)
- Bulk upload via Excel file (columns: roll_no, first_name, email, college, branch, passout_year)
- Default password applied from admin settings
- User list with search and pagination
- Delete user (removes user + all their interview data)
- Reset password to default

### 11.3 Question Limits Tab
- Resume-based question count (default: 10)
- Custom selection question count (default: 10)
- HR behavioral question count (default: 8)
- Default starting difficulty (Easy/Medium/Hard)

### 11.4 Interview Limits Tab
- Maximum interviews per user (default: 6)

### 11.5 Hierarchy Tab
- Three-column layout: Technologies | Modules | Topics
- Click technology -> loads its modules; click module -> loads its topics
- Add, Edit (inline), Delete for each level
- Technology has name + category fields

### 11.6 Statistics Tab
- Time-based summary cards
- Three charts: Daily bar, Monthly grouped bar, Score doughnut
- Paginated interview table with search
- Columns: Roll No, Tech/Level, Score, Confidence, Questions, Date & Time, Duration
- Server-side pagination with page navigation

---

## 12. Security & Proctoring

| Feature | Implementation |
|---------|---------------|
| Authentication | JWT tokens with configurable expiry |
| Password Storage | bcrypt hashing (10 salt rounds) |
| Role-Based Access | AdminRoute component blocks students from /admin |
| Tab-Switch Detection | visibilitychange API — 1st warning, 2nd terminate |
| Camera Monitoring | Face-API.js requires camera to remain on |
| Audio Monitoring | MediaRecorder streams audio for analysis |
| CORS Protection | Backend whitelist specific client origin |
| File Upload Validation | Extension + MIME type checking |
| Interview Integrity | End/abandon deletes record, only completed submissions count |

---

## 13. Deployment Architecture

```
                    +------------------+
                    |    VERCEL         |
                    |  (Frontend)      |
                    |  React Build     |
                    +--------+---------+
                             |
                    HTTPS API calls + WebSocket
                             |
                    +--------+---------+
                    |    RENDER #1     |
                    |  (Backend)       |
                    |  Node.js         |
                    |  Express + WS    |
                    +--------+---------+
                             |
                    REST API calls
                             |
                    +--------+---------+
                    |    RENDER #2     |
                    |  (Python Engine) |
                    |  Flask + Gunicorn|
                    +------------------+
                             |
                    +--------+---------+
                    |  MongoDB Atlas   |
                    |  (Cloud DB)      |
                    +------------------+
                             |
                    +--------+---------+
                    |  Groq Cloud API  |
                    |  (LLM)           |
                    +------------------+
```

### Environment Variables

#### Frontend (Vercel)
```
REACT_APP_API_URL=https://your-backend.onrender.com/api
REACT_APP_SERVER_URL=https://your-backend.onrender.com
REACT_APP_SOCKET_URL=https://your-backend.onrender.com
CI=false
```

#### Backend (Render)
```
PORT=5001
MONGO_URI=mongodb+srv://...
JWT_SECRET=your_secret
TOKEN_EXPIRY=1d
PYTHON_ENGINE_URL=https://your-python-engine.onrender.com
CLIENT_URL=https://your-frontend.vercel.app
```

#### Python Engine (Render)
```
GROQ_API_KEY=gsk_...
```

---

## 14. Future Scope

| Feature | Description |
|---------|-------------|
| CNN-LSTM Speech Emotion Model | Replace Librosa heuristic with trained deep learning model on RAVDESS/SAVEE datasets |
| Video Recording & Playback | Record interview video for self-review and sharing |
| AI Avatar with WebGL/Three.js | 3D animated human avatar with real-time lip sync and facial expressions |
| Multi-language Support | Interview in regional languages with translation |
| Company-Specific Question Banks | Train on company-specific interview patterns |
| Peer Comparison | Compare performance against cohort averages |
| Interview Scheduling | Calendar-based interview booking with reminders |
| API Rate Limiting | Throttle requests to prevent abuse |
| OAuth 2.0 | Google/GitHub social login integration |
| Mobile App | React Native or Flutter mobile application |

---

## Appendix

### A. Supported File Formats
- Resume: PDF, DOCX
- Certificates: PDF, JPG, JPEG, PNG, WEBP
- Bulk Upload: XLSX, XLS

### B. Browser Compatibility
- Chrome 90+ (recommended — full TTS + SpeechRecognition support)
- Edge 90+
- Firefox 100+ (limited SpeechRecognition)
- Safari 15+ (limited MediaRecorder)

### C. Hardware Requirements
- Webcam (required for facial detection)
- Microphone (required for speech recognition + audio analysis)
- Speakers/headphones (for TTS question reading)

---

*Document generated for IntelliView v2.0 — April 2026*
