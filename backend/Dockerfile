# Stage 1: Build
FROM node:16-alpine as builder

WORKDIR /app
COPY backend/package*.json ./
RUN npm ci
COPY backend/ .

# Stage 2: Run
FROM node:16-alpine

WORKDIR /app
COPY --from=builder /app .

# Create logs directory
RUN mkdir -p /app/logs/combined /app/logs/error

ENV NODE_ENV=production
EXPOSE 3000

CMD ["node", "app.js"]