FROM node:18-slim AS builder

WORKDIR /app

COPY TypeScript-API/package*.json ./

RUN npm install

COPY TypeScript-API/ .

RUN npm run build

FROM node:18-slim AS production

WORKDIR /app

COPY --from=builder /app/build /app/build
COPY --from=builder /app/package*.json /app/

RUN npm install --production

RUN useradd -m -s /bin/bash appuser && \
    chown -R appuser:appuser /app

USER appuser

EXPOSE 3000

CMD ["node", "build/index.js"]
