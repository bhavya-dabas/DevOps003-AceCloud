# Stage 1: Build
#FROM node:16-alpine as builder

#WORKDIR /app
#COPY package.json package-lock.json ./
#RUN npm ci
#COPY . .
#RUN npm run build

# Stage 2: Run
#FROM nginx:alpine
#COPY --from=builder /app/build /usr/share/nginx/html
#COPY nginx.conf /etc/nginx/conf.d/default.conf
#EXPOSE 80
#CMD ["nginx", "-g", "daemon off;"]





# Stage 1: Builder - Install dependencies and build
FROM node:16-alpine AS builder

WORKDIR /app
COPY package.json package-lock.json ./
# Install only production dependencies
ENV NODE_ENV=production
RUN npm ci --only=production
COPY . .
RUN npm run build

# Stage 2: Production - Use nginx alpine
FROM nginx:alpine

WORKDIR /usr/share/nginx/html
# Copy built assets and nginx config
COPY --from=builder /app/build .
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
