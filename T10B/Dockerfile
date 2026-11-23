FROM node:18-alpine
WORKDIR /app

COPY mock-api/server.js mock-api/server.js

ENV MOCK_API_PORT=5050
ENV MOCK_API_HOST=0.0.0.0

EXPOSE 5050

HEALTHCHECK --interval=10s --timeout=3s --retries=3 CMD node -e "require('http').get('http://127.0.0.1:5050/health',r=>process.exit(r.statusCode===200?0:1)).on('error',()=>process.exit(1))"

CMD ["node", "mock-api/server.js"]