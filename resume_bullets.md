# Redacto — Resume Bullets

## Full set (pick 3-4 depending on role)

- Architected and shipped **Redacto**, a full-stack privacy-compliance platform (React + FastAPI + Claude API) that detects 30+ PII categories and applies permanent black-box redactions to PDF and DOCX documents
- Designed a Claude API detection pipeline with structured JSON output across 30+ sensitive data categories including medical, financial, and demographic PII — with per-category toggles and named redaction profiles (Medical, HR, Financial, Maximum)
- Built a Celery + Redis batch processing queue supporting 100+ document jobs with real-time status polling, per-file error handling, and partial-completion reporting
- Shipped a Manual Studio post-redaction editor allowing reviewers to toggle individual redactions, review PII by category, and re-download adjusted outputs without re-running the full pipeline
- Containerized the full stack (FastAPI + React + Redis) via Docker Compose with environment-variable-driven configuration and gitignored storage directories to prevent sensitive file exposure

---

## By role

### TPM / Program roles — lead with delivery and systems:
- Architected and shipped Redacto — a full-stack document redaction platform (React, FastAPI, Claude API, Redis) that detects 30+ PII/sensitive data categories and applies permanent redactions to PDF and DOCX files
- Owned end-to-end technical delivery across 5 backend services, 4 frontend pages, and a Celery + Redis batch queue — from API design through Docker containerization and deployment

### AI / PM roles — lead with the product:
- Built Redacto, a privacy-compliance document intelligence tool powered by the Claude API — supports 30+ PII categories, batch processing of 100+ documents, named redaction profiles, and a manual review studio
- Designed the Claude API detection prompt and JSON output schema to return exact text matches with surrounding context across 30+ sensitive data types — used by downstream redaction engines for PDF and DOCX

### Compliance / regulated industry roles — lead with the use case:
- Built a HIPAA-aligned document redaction platform using Claude AI to detect medical, financial, and demographic PII across 30+ categories — with permanent black-box redactions, audit metadata per job, and a manual review workflow for compliance teams
