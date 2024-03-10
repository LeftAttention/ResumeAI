# Build stage
FROM node:18-slim AS build-stage

WORKDIR /app

COPY . .

# Install dependencies
RUN npm install --force
RUN npm install -g @angular/cli
RUN npm install -g angular-http-server
RUN ng build

# Serve stage
FROM nginx:alpine AS serve-stage

# Copy the built app from the build stage to the nginx serve directory
COPY --from=build-stage /app/dist/resumebuilder /usr/share/nginx/html

# Prepare and set a new nginx configuration for routing and proxying
RUN echo 'server {\
  listen 80;\
  location / {\
    root   /usr/share/nginx/html;\
    index  index.html index.htm;\
    try_files $uri $uri/ /index.html =404;\
  }\
  location /api/ {\
    proxy_pass http://localhost:4292;\
  }\
}' > /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]

# RUN echo "module.exports = {" > proxy.js && \
#     echo "    proxy: [" >> proxy.js && \
#     echo "        {" >> proxy.js && \
#     echo "            forward: ['api/']," >> proxy.js && \
#     echo "            target: 'http://localhost:4292'," >> proxy.js && \
#     echo "            protocol: 'http'," >> proxy.js && \
#     echo "        }" >> proxy.js && \
#     echo "    ]," >> proxy.js && \
#     echo "};" >> proxy.js

# EXPOSE 80

# CMD ["angular-http-server", "-p", "80", "--config", "proxy.js"]