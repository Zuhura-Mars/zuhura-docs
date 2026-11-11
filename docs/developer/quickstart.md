# Developer Quickstart Guide

This guide describes how to run and test the complete Zuhura Mars Digital Twin locally.

## Prerequisites
- **Docker & Docker Compose** (v2.20+)
- **Python 3.12+**
- **Node.js 20+**
- **PROJ 9+** (for local geospatial coordinate development)

## 1. Local Environment Setup
```bash
# Clone or navigate to the Zuhura organization directory
cd /path/to/Zuhura

# Run the automated development setup script
./zuhura/scripts/dev-setup.sh
```

## 2. Launching Supporting Services (PostgreSQL + PostGIS, Redis, MinIO)
```bash
cd zuhura/docker
docker compose up -d postgres redis minio minio-init
```
Verify connectivity:
```bash
../scripts/healthcheck.sh
```

## 3. Running the Backend Service
```bash
cd zuhura-backend
python -m venv .venv && source .venv/bin/activate
pip install -e ".[dev]"
uvicorn zuhura.api.main:app --reload --port 8000
```
API Documentation will be accessible at: `http://localhost:8000/docs`

## 4. Running the Frontend Application
```bash
cd zuhura-frontend
npm install
npm run dev
```
Open `http://localhost:3000` to interact with the Mars globe.

## 5. Running the Test Suite
```bash
# Backend Geodesy & API integration tests
cd zuhura-backend
pytest -v

# Frontend Geodesy unit tests
cd zuhura-frontend
npm run test:run
```
