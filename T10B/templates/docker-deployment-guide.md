# Docker deployment guide - Workshops API

## What you are deploying
The Week 9 mock Workshops API, packaged in a container. It serves:
- `GET /health` (no key)
- `GET /api/v1/workshops` and `/api/v1/workshops/<id>` (need header `x-api-key`)

## Steps
1. Open your folder, The `Dockerfile` is already here.
2. Create `.env` from `.env.example` and paste icstars-w9-7Q4K2M8X.
3. Build the image:
       docker build -t workshops-api:v1 .
4. Run the container:
       docker run -d --name workshops-api -p 5050:5050 --env-file .env workshops-api:v1
5. Verify (paste your output below). Windows: `curl.exe`
       docker ps
       curl.exe http://localhost:5050/health
       curl.exe -H "x-api-key: YOURKEY" http://localhost:5050/api/v1/workshops/1
       docker logs workshops-api

## My /health output (paste here)
```
{
  "status": "ok",
  "endpoints": [
    "/health",
    "/api/v1/workshops",
    "/api/v1/workshops/<id>"
  ]
}
```

## The two lines that matter (explain in your own words)
- `ENV MOCK_API_HOST=0.0.0.0` -> tells the app what address to answer to. Since it's 0.0.0.0, it will answer to anyone, including requests from outside the container.
- `-p 5050:5050` (host:container) -> -p connects port 5050 on my computer (left) to port 5050 inside the container (right).

## Note
The health check runs INSIDE the container, so it targets 127.0.0.1:5050 - that works
because inside the box the app answers on localhost. You reach it from your laptop only
because the app binds to 0.0.0.0 AND you mapped the port with `-p`.
