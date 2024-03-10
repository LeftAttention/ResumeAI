# Build stage
FROM node:18-slim as build-stage

WORKDIR /app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install all dependencies
RUN npm install
RUN npm install -g typescript

# Copy the rest of the backend application
COPY . .

RUN tsc

# Final stage
FROM node:18-slim

WORKDIR /app

# Copy built assets from the build stage
COPY --from=build-stage /app/dist ./dist
COPY --from=build-stage /app/node_modules ./node_modules
COPY --from=build-stage /app/package.json ./package.json

# Expose the port the app runs on
EXPOSE 4292

CMD ["npm", "run", "start"]