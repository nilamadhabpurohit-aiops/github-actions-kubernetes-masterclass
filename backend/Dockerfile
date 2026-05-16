# Stage 1: Build
FROM golang:1.26-alpine AS builder

RUN apk add --no-cache git

WORKDIR /app

COPY go.mod ./

RUN go mod download

COPY . .

RUN go mod tidy

RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 \
    go build -a -ldflags="-s -w" -o skillpulse .

    
# Stage 2: Run
FROM alpine:3.23

RUN apk --no-cache add ca-certificates

RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

COPY --from=builder /app/skillpulse .

RUN chown appuser:appgroup /app/skillpulse

USER appuser

EXPOSE 8080

CMD ["./skillpulse"]
