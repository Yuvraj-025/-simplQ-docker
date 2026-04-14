FROM node:14.16-alpine AS build

# Pass the backend URL through build arg
ARG BASE_URL="https://devbackend.simplq.me/v1"

# Setup working directory early
WORKDIR /app

# Only copy dependency files first to utilize Docker layer caching
COPY package.json package-lock.json ./
RUN npm ci

# Copy the rest of the application
COPY . .

# Write the environment variable to .env
RUN echo "BASE_URL=${BASE_URL}" > .env

# Build the react application
RUN npm run config && npm run build

# Next stage: lightweight web server
FROM nginx:1.19.6-alpine

# Copy custom nginx configuration
COPY nginx.conf /etc/nginx/nginx.conf

# Set working directory to standard nginx html directory
WORKDIR /usr/share/nginx/html

# Clean the default html directory
RUN rm -rf ./*

# Copy the build artifacts from the previous stage
COPY --from=build /app/build/ .

EXPOSE 80

# Run nginx in foreground
CMD ["nginx", "-g", "daemon off;"]