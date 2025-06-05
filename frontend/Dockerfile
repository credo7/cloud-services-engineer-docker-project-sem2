# Build stage
FROM node:20-alpine AS builder

# Install build dependencies in a single layer
RUN apk update && apk upgrade && \
    apk add --no-cache make python3 g++ && \
    rm -rf /var/cache/apk/*

# Set working directory
WORKDIR /app

# Set Node options for legacy OpenSSL provider
ENV NODE_OPTIONS=--openssl-legacy-provider

# Copy package files and install ALL dependencies (including dev dependencies)
COPY package*.json ./
RUN npm ci

# Copy source code
COPY . .

# Set production environment and build
ENV NODE_ENV=production
ENV VUE_APP_API_URL=/api
RUN npm run build

# Production stage
FROM nginx:1.27-alpine

# Install security updates and required packages in a single layer
RUN apk update && apk upgrade && \
    apk add --no-cache curl tzdata && \
    rm -rf /var/cache/apk/*

# Create non-root user with proper groups
RUN addgroup -g 1001 -S appgroup && \
    adduser -u 1001 -S appuser -G appgroup

# Copy nginx configuration with proper ownership
COPY --chown=appuser:appgroup nginx.conf /etc/nginx/conf.d/default.conf

# Copy built assets from builder stage with proper ownership
COPY --from=builder --chown=appuser:appgroup /app/dist /usr/share/nginx/html

# Set proper permissions for nginx runtime
RUN touch /var/run/nginx.pid && \
    chown -R appuser:appgroup /var/cache/nginx /var/log/nginx /etc/nginx/conf.d /var/run/nginx.pid /usr/share/nginx/html

# Switch to non-root user
USER appuser

# Expose port
EXPOSE 80

# Add health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD curl -f http://localhost || exit 1

# Start nginx
CMD ["nginx", "-g", "daemon off;"]