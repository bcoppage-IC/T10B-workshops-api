# Deployment checklist - Workshops API

Tick each box. A teammate should be able to deploy from this alone.
(Windows: type `curl.exe`. Mac: `curl`.)

## Before you deploy
- [X] Docker Desktop is running (`docker --version`, whale icon green)
- [X] I'm in my folder ('C:\Users\<your_name&&PATH>\Documents\T10B Deployment\T10B>' the one with the `Dockerfile`)
- [X] `.env` exists with my real `API_KEY` - only the key (and `.env` is NOT committed)
- [X] Port 5050 is free (`docker ps` shows nothing mapped to 5050)

## Deploy
- [X] Build: `docker build -t workshops-api:v1 .`  (ends with `naming to ... workshops-api:v1`)
- [X] Run: `docker run -d --name workshops-api -p 5050:5050 --env-file .env workshops-api:v1`

## Verify (all three must pass)
- [X] `docker ps` shows the container **Up** and **(healthy)** (it says "health: starting" for ~10 sec first)
- [X] `curl.exe http://localhost:5050/health`  ->  `{"status":"ok",...}`
- [X] `curl.exe -H "x-api-key: MYKEY" http://localhost:5050/api/v1/workshops/1`  ->  a workshop **title**

If any check fails, see Quick help in START-HERE.md before changing anything.

## Record
- Image tag deployed: workshops-api:v1
- Time verified green: 3:31 PM CST
- Verified by (peer): Pending
