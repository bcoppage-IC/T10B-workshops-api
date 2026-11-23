# Rollback procedure - Workshops API

**Service:** Workshops API    **Current good version:** `workshops-api:v1`

## Rollback trigger (decide this BEFORE you ship)
Roll back when: docker ps shows (unhealthy) or curl.exe http://localhost:5050/health returns an error or does not return "status": "ok" or the keyed workshops call returns an error or no title.


## Rollback steps
```
docker rm -f workshops-api                                                        # remove the bad version
docker run -d --name workshops-api -p 5050:5050 --env-file .env workshops-api:v1  # restore known-good
curl.exe http://localhost:5050/health                                            # confirm green (Mac: curl)
```

## After rollback
- Service restored at (time): 5:24 PM CST
- Root cause of the bad version: Dockerfile.v2 was set to port 5055 and -p was pointing to 5050, so the app was not listening to the correct port.
- What we'd change so it can't happen again: Set the port to only one place and test v2 before replacing v1.
