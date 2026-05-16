# Stage 1: Build
FROM golang:1.26-alpine AS builder

# Install git if private/public modules require it
RUN apk add --no-cache git

WORKDIR /app

# Copy dependency files first for better caching
COPY go.mod go.sum ./

# Download dependencies
RUN go mod download


COPY . .
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 \
    go build -a -ldflags="-s -w" -o skillpulse .
# Stage 2: Run
FROM alpine:3.23

# Install CA certificates
RUN apk --no-cache add ca-certificates

# Create non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

# Copy binary from builder
COPY --from=builder /app/skillpulse .

# Change ownership
RUN chown appuser:appgroup /app/skillpulse

# Use non-root user
USER appuser

# Expose application port
EXPOSE 8080

# Start application
CMD ["./skillpulse"]
