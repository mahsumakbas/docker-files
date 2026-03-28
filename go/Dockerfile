# 1. Phase: Build
FROM golang:1.25-alpine AS builder
# Add some tools for debugging (optional)
#RUN apk add --no-cache git

# To add C dependencies, we need to enable cgo and install build tools
RUN apk add --no-cache build-base git

WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
# CGO_ENABLED=0 to disable cgo and create a statically linked binary, GOOS=linux to target Linux OS
#RUN CGO_ENABLED=0 GOOS=linux go build -o fundhub-be ./cmd/api/main.go

# CGO_ENABLED=1 to enable cgo and create a dynamically linked binary, GOOS=linux to target Linux OS
RUN CGO_ENABLED=1 GOOS=linux go build -o fundhub-be ./cmd/api/main.go


# 2. Phase: Run (Final Image)
FROM alpine:latest
# Install necessary runtime dependencies (e.g., ca-certificates for HTTPS, tzdata for timezone data)
RUN apk --no-cache add ca-certificates tzdata

# Create a clean directory for the application
WORKDIR /app

# SECURITY: Run as non-root user (optional but recommended)
#RUN adduser -D myuser
#USER myuser

COPY --from=builder /app/main  .

EXPOSE 8080
CMD ["./main"]