FROM node:18-slim

WORKDIR /app

COPY TypeScript-API/ .

RUN npm install && \
    npm run build && \
    npm prune --production && \
    useradd -m -s /bin/bash appuser && \
    chown -R appuser:appuser /app

USER appuser

EXPOSE 3000

CMD ["node", "build/index.js"]
