
***** LINK HERE *****
[Intern follow-along guide](https://docs.google.com/document/d/1Z9_hKRQrhgysJwYXX8MmxZ5Rg7r1uUoUSogUZE8lSCY/edit?usp=sharing). 


Today you ship the Workshops API you built against in Week 9: you'll package it in a Docker container, prove it's working, break a new version on purpose, and roll it back. Work in pairs — both of you run every command. Raise your hand the moment something errors; that's expected, and it's how we all learn.
The one idea to hold onto: a container is a brand-new machine. Your code doesn't automatically run — or stay reachable — inside it. Two things make a deployment real: the app has to listen on 0.0.0.0, and you have to verify it's working, not assume it.
Step 0 — Set up (about 5 minutes)
1. Open your track folder. Open the T10B Deployment folder, then open your track folder (javascript or python).
2. Check Docker is ready. Run:
docker --version
docker run --rm hello-world
You should see a version number and a "Hello from Docker!" message. Red on either? Raise your hand — pair up with a neighbor while we fix it.
3. Create your secret file. Copy .env.example to a new file named .env, and paste your Week 9 API key after API_KEY=. Never commit .env — it holds a secret.
✅ Checkpoint: Docker runs, and you have a .env with your key in it.
Step 1 — Watch the build (follow along in your head)
Your facilitator will write the Dockerfile live, explain each line, then deploy it once, start to finish. Watch for the two lines that matter:
ENV MOCK_API_HOST=0.0.0.0 — the line that makes the app reachable from outside the container.
-p 5050:5050 — maps a door on your laptop (the left number) to a door inside the container (the right number).
Step 2 — Deploy your container
From inside your track folder, build the image, then run it:
docker build -t workshops-api:v1 .
docker run -d --name workshops-api -p 5050:5050 --env-file .env workshops-api:v1
What you should see: the build ends with something like naming to ... workshops-api:v1, and the run prints a long container ID.
Step 3 — Verify it's really working (all three)
"Running" is not the same as "working." Check all three:
docker ps
curl.exe http://localhost:5050/health
curl.exe -H "x-api-key: YOUR-KEY" http://localhost:5050/api/v1/workshops/1
What you should see:
docker ps → your container, Up, and (healthy) — give it a few seconds to turn healthy.
/health → {"status":"ok", ...}
the workshops call → a workshop title (for example, "Intro to Spreadsheets for Small Business").
Paste your /health output into your deployment guide (in templates/).
✅ Checkpoint: all three pass. If curl fails ("connection reset" or "empty reply") but docker ps shows it running — that's the classic one. See Quick help at the bottom.
Step 4 — Break it on purpose, then roll back
This part is supposed to fail — that's the lesson. A "change request" comes in and a teammate ships a v2 with a wrong setting.
1. Build and run v2:
docker build -t workshops-api:v2 -f Dockerfile.v2 .
docker rm -f workshops-api
docker run -d --name workshops-api -p 5050:5050 --env-file .env workshops-api:v2
2. Watch it fail:
docker ps                            # wait ~30 sec, then (unhealthy)
curl.exe http://localhost:5050/health    # this fails now
docker logs workshops-api            # read WHY — what port did it bind to?
3. Write your rollback trigger in your rollback template — the thing that told you to go back (for example: "health check went unhealthy and /health stopped responding").
4. Roll back to the version you know works:
docker rm -f workshops-api
docker run -d --name workshops-api -p 5050:5050 --env-file .env workshops-api:v1
curl.exe http://localhost:5050/health    # back to 200
✅ Checkpoint: you saw an unhealthy container, and you brought the service back with v1.
Step 5 — Write it down (the part that makes it real)
Fill in the templates in templates/:
deployment-checklist — tick what you did.
docker-deployment-guide — your build/run/verify steps and your /health output.
rollback-procedure — your trigger, the steps, and what you'd change so it can't happen again.
operations-runbook — how to start and stop it, the three monitoring signals, and one failure scenario with how you'd recover.
Rule for today: write these yourself. You can use AI to explain an error message, but the decisions and the write-up are yours.
Step 6 — Peer validation
Swap with another pair. Using only their checklist and guide — not their help — try to deploy and verify their service. If it works, sign their checklist. If it doesn't, tell them kindly what was missing. This is the real test: can someone else run your service from your docs?
You're done when…
Your container is (healthy) and /health returns 200.
A keyed request returns a workshop title.
You broke v2, you know exactly what told you it was broken, and you rolled back to v1.
Another pair deployed your service using only what you wrote.
Quick help (the ones you'll actually hit)
curl localhost:5050 fails (reset, empty reply, or refused), but docker ps says it's running → the app is listening only inside the container. Make sure your Dockerfile has ENV MOCK_API_HOST=0.0.0.0 and that you ran with -p 5050:5050. (This is #1 — expect it.)
docker ps shows (unhealthy) → the app is on a different internal port than the health check expects (that's the v2 bug). Roll back to :v1.
port is already allocated → something still holds 5050. Remove the old container (docker rm -f workshops-api), or stop a Week 9 server still running in another terminal (Ctrl + C).
docker: command not found / daemon error → Docker Desktop isn't running. Start it and wait for the whale icon.
401 from the workshops call → wrong or missing key. Make sure .env has your real key (not the placeholder text), then docker rm -f workshops-api and run it again.
name is already in use → run docker rm -f workshops-api, then try again.
Command cheat-sheet
docker build -t NAME:TAG .                                 # build an image
docker run -d --name N -p HOST:CONTAINER --env-file .env NAME:TAG   # run it
docker ps            # is it up? healthy?
docker logs N        # what is it saying?
docker stop N        # stop it
docker rm N          # remove it (stop it first)
Nice work — you just did what a lot of developers do every week: package an app, prove it works, and recover from a bad release.
