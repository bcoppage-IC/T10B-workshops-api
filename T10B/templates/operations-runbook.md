# Operations runbook - Workshops API

**Service:** Workshops API   **Owner / on-call:** Ben Coppage   **Runtime:** Node 18 (in container)

**Start:** `docker run -d --name workshops-api -p 5050:5050 --env-file .env workshops-api:v1`
**Stop:**  `docker stop workshops-api`
**Listens:** http://localhost:5050 (host)  ->  0.0.0.0:5050 (container)

## Monitoring signals (what "healthy" looks like)
- `docker ps` -> container Up, status (healthy)
- `GET /health` -> 200 with `{"status":"ok"}`
- `docker logs workshops-api` -> steady `... -> 200` lines (including Docker's health check every 10 sec), no repeated ERROR

## Incident I planned for (required: at least one)
| Symptom | Likely cause | Check | Recovery |
|---|---|---|---|
| `docker ps` shows (unhealthy) and curl returns "Empty reply from server" | app listening on a different port (5055) than the health check and `-p` expect (5050) | `docker logs workshops-api` shows which port the app started on | `docker rm -f workshops-api`, then run `:v1` again and confirm `/health` returns ok |
| curl fails (reset / empty reply) but `docker ps` shows "running" | app bound to 127.0.0.1 inside the container, or port not mapped | does the Dockerfile have `ENV MOCK_API_HOST=0.0.0.0`, and did the run use `-p 5050:5050`? | fix and redeploy, or roll back to `:v1` |

## Escalation
If two rollback attempts don't restore a green `/health`, stop and raise it to the facilitator
with the last 20 log lines.