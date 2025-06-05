# Build stage
FROM golang:1.22.7-alpine3.20 AS builder

# Install build dependencies
RUN apk update --no-cache && \
    apk add --no-cache git ca-certificates tzdata curl

# Set working directory
WORKDIR /app

# Copy go mod and sum files for better caching
COPY go.mod go.sum ./

# Download dependencies
RUN go mod download && go mod verify

# Copy source code
COPY . .

# Build the application with optimizations
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build \
    -a -installsuffix cgo \
    -ldflags="-s -w -extldflags '-static'" \
    -o main ./cmd/api

# Final stage
FROM alpine:3.20

# Install security updates and required packages
RUN apk update --no-cache && \
    apk add --no-cache ca-certificates tzdata wget && \
    update-ca-certificates && \
    rm -rf /var/cache/apk/*

# Create non-root user with specific UID/GID for consistency
RUN addgroup -g 1001 -S appgroup && \
    adduser -u 1001 -S appuser -G appgroup

# Set working directory
WORKDIR /app

# Copy binary from builder with proper ownership
COPY --from=builder --chown=appuser:appgroup /app/main .

# Switch to non-root user
USER appuser

# Expose port
EXPOSE 8081

# Add health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD wget -q --spider http://localhost:8081/health || exit 1

# Run the application
CMD ["./main"]