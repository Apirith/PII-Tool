# 🔒 Redacto

> A privacy-compliance document intelligence platform that detects and permanently removes PII from PDF and DOCX files using the Claude AI API.

---

## What It Does

Redacto eliminates the manual work of document redaction. Upload a file, select which PII categories to remove, and download a clean version — with true black-box redactions, not blurs. The original data is permanently gone from the output file.

Built for compliance teams, legal reviewers, HR departments, and anyone handling sensitive documents at scale.

---

## Features

- **30+ PII categories** — names, SSNs, addresses, medical records, financial data, and more
- **True redaction** — black-box or labeled replacements, not blurs; data is permanently removed
- **PDF and DOCX support** — full text extraction and redaction for both formats
- **Manual Studio** — post-redaction editor to toggle individual redactions before final download
- **Redaction profiles** — save category sets as named profiles (Medical, HR, Financial, Maximum)
- **Batch mode** — drop 100+ documents, process in background, download all results
- **Template output** — upload a sample doc to define the desired output structure
- **Custom instructions** — add freeform guidance to the Claude detection prompt per job

---

## PII Categories Supported

| Category | Examples |
|---|---|
| Person name | Bob, Dr. Kay Martinez |
| Email address | support@company.com |
| Phone number | (919) 555-1234 |
| Location | 145 Windsor St., Raleigh NC 27601 |
| Date of birth | March 7, 1961 |
| SSN | 123-45-6789 |
| Driver's license | DL# 356933-540 |
| Passport number | PA4568332 |
| Credit card | 4111 1111 1111 1111 |
| Banking information | Account + routing numbers |
| Medical condition | chronic fatigue syndrome |
| Medical process | heart surgery, CT scan |
| Blood type | O-, AB positive |
| Drug / medication | Advil, Acetaminophen |
| IP address | 192.168.0.1 |
| Password | temp1234 |
| Gender / sexuality | female, bisexual |
| Nationality / race | American, Asian |
| Religion | Hindu, Catholic |
| Political affiliation | Republican, Liberal |
| + 15 more | occupation, organization, vehicle ID, images, and others |

Each category is individually toggleable per job or saveable in a named profile.

---

## Architecture

```
React Frontend
  Upload Zone · Category Picker · Profile Manager
  Manual Studio Editor · Batch Dashboard · Download
          │
          │ HTTP / WebSocket
          ▼
FastAPI Backend
  /upload  /redact  /batch  /profiles  /jobs  /download
          │
    ┌─────┼──────────┐
    ▼     ▼          ▼
Claude   PyMuPDF   python-docx
(detect)  (PDF)     (DOCX)
          │
    Celery + Redis
    Batch queue · Job tracking · Progress reporting
```

---

## Tech Stack

| Layer | Tool |
|---|---|
| Frontend | React 18, Vite |
| Backend | FastAPI, Uvicorn |
| AI / Detection | Claude API (Anthropic) |
| PDF Redaction | PyMuPDF |
| DOCX Redaction | python-docx |
| Batch Queue | Celery + Redis |
| Containerization | Docker, Docker Compose |

---

## Setup

### Prerequisites

- Python 3.11+
- Node.js 20+
- Redis (`brew install redis` or Docker)
- Anthropic API key — get one at [platform.anthropic.com](https://platform.anthropic.com)

---

### Backend

```bash
cd backend
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt

cp .env.example .env
# Open .env and set ANTHROPIC_API_KEY

mkdir -p storage/uploads storage/outputs
uvicorn main:app --reload --port 8000
```

---

### Frontend

```bash
cd frontend
npm install
npm run dev                     # Starts at http://localhost:5173
```

---

### Redis (required for batch mode)

```bash
# macOS
brew services start redis

# Docker
docker run -d -p 6379:6379 redis:alpine
```

---

### Docker Compose (all services at once)

```bash
docker-compose up --build
```

Then open [http://localhost:5173](http://localhost:5173).

---

## Quick Start

1. Open [http://localhost:5173](http://localhost:5173)
2. Upload a PDF or DOCX file
3. Select PII categories to redact (or load a saved profile)
4. Choose redaction style — black box or labeled replacement
5. Click **Redact**
6. Review in Manual Studio if needed
7. Download the clean file

---

## Redaction Profiles

Four presets are included out of the box:

| Profile | Use case |
|---|---|
| Medical Records | HIPAA-aligned — health IDs, conditions, medications, names, dates |
| HR / Employment | Anonymize HR documents — names, DOB, SSN, demographics, images |
| Financial Statements | Financial PII — account numbers, cards, routing, SSN |
| Maximum Redaction | All 30+ categories + embedded images |

Custom profiles can be created and saved from the Profiles page.

---

## API Cost Estimates

| Scenario | Approx. tokens | Approx. cost |
|---|---|---|
| 1-page resume | ~2,000 | ~$0.006 |
| 10-page contract | ~15,000 | ~$0.045 |
| 50-page medical record | ~75,000 | ~$0.225 |
| Batch of 100 resumes | ~200,000 | ~$0.60 |

> Tip: Use the Anthropic Batch API (`/v1/messages/batches`) for 50% cost reduction on large batch jobs.

---

## Project Structure

```
redacto/
├── backend/
│   ├── main.py                    # FastAPI entry point
│   ├── requirements.txt
│   ├── .env.example
│   ├── routers/
│   │   ├── upload.py
│   │   ├── redact.py              # Single-doc redaction
│   │   ├── batch.py               # Batch processing
│   │   ├── profiles.py            # Profile CRUD
│   │   └── jobs.py                # Job status + download
│   ├── services/
│   │   ├── claude_detector.py     # Claude API PII detection
│   │   ├── pdf_redactor.py        # PyMuPDF redaction engine
│   │   ├── docx_redactor.py       # python-docx redaction engine
│   │   ├── image_redactor.py      # Face + image removal
│   │   └── batch_processor.py    # Background job processor
│   ├── models/
│   │   ├── schemas.py
│   │   └── profiles.py
│   └── storage/                   # gitignored — created at runtime
│       ├── uploads/
│       └── outputs/
│
├── frontend/
│   └── src/
│       ├── pages/
│       │   ├── Home.jsx           # Upload + configure
│       │   ├── Studio.jsx         # Manual editor
│       │   ├── Batch.jsx          # Batch dashboard
│       │   └── Profiles.jsx       # Profile manager
│       ├── components/
│       └── api/client.js
│
├── docker-compose.yml
├── .gitignore
└── README.md
```

---

## Environment Variables

| Variable | Description | Default |
|---|---|---|
| `ANTHROPIC_API_KEY` | Your Claude API key | required |
| `REDIS_URL` | Redis connection string | `redis://localhost:6379/0` |
| `UPLOAD_DIR` | Incoming file storage path | `./storage/uploads` |
| `OUTPUT_DIR` | Redacted file output path | `./storage/outputs` |
| `MAX_FILE_SIZE_MB` | Upload size limit | `50` |

---

## Roadmap

- [ ] Redis-backed job status (replace in-memory store)
- [ ] Celery async batch processing for high-volume jobs
- [ ] JWT authentication middleware
- [ ] Audit log — timestamps, file hashes, redaction counts per job
- [ ] Rate limiting on Claude API calls via `slowapi`
- [ ] Auto-delete output files after 24 hours

---
