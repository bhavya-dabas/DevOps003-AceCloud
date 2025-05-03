# Stage 1: Install dependencies
#FROM node:16-alpine as installer

#WORKDIR /app
#COPY package.json package-lock.json ./
#RUN npm ci
#COPY . .

# Stage 2: Run
#FROM node:16-alpine

#WORKDIR /app
#COPY --from=installer /app .

# Create logs directory
#RUN mkdir -p /app/logs/combined /app/logs/error

#ENV NODE_ENV=production
#EXPOSE 3000

#CMD ["node", "app.js"]



# Stage 1: Builder - Install dependencies
FROM node:16-alpine AS builder

WORKDIR /app
COPY package.json package-lock.json ./
# Install only production dependencies
ENV NODE_ENV=production
RUN npm ci --only=production
COPY . .
# Create logs directories
RUN mkdir -p /app/logs/combined /app/logs/error

# Stage 2: Production - Use distroless image
FROM gcr.io/distroless/nodejs:16

WORKDIR /app
# Copy only essential files
COPY --from=builder /app/package.json /app/package-lock.json ./
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/app.js ./
COPY --from=builder /app/config ./config
COPY --from=builder /app/models ./models
COPY --from=builder /app/routes ./routes
COPY --from=builder /app/logs ./logs

ENV NODE_ENV=production
EXPOSE 3000

CMD ["app.js"]
