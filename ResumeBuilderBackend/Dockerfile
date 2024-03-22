# Build stage
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install --force
COPY . .
RUN npm install typescript -g
RUN tsc --build

# Deployment stage
FROM node:18
WORKDIR /app
COPY --from=builder /app ./
EXPOSE 4292

RUN echo "JWT_SECRET_KEY=${JWT_SECRET_KEY}\nMONGO_URL=${MONGO_URL}\nFRONT_END=${FRONT_END}\nOPENAI_KEY=${OPENAI_KEY}\nGMAIL_USER=${GMAIL_USER}\nGMAIL_PASS=${GMAIL_PASS}" > .env
CMD ["npm", "run", "start"]