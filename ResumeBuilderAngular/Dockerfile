# Build stage
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install --force
RUN npm i -g @angular/cli
RUN npm install -g angular-http-server

COPY . .
RUN npm run build

# Deployment stage
FROM nginx:alpine
COPY --from=builder /app/dist/resumebuilder /usr/share/nginx/html