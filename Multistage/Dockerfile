# --- Stage 1: The Builder ---
# We name this stage "builder" so we can reference it later
FROM golang:1.21 AS builder

WORKDIR /app
COPY . .

# Compile the binary
RUN CGO_ENABLED=0 GOOS=linux go build -o myapp main.go


# --- Stage 2: The Final Image ---
# Start fresh with a tiny OS (Alpine is ~5MB)

FROM alpine:latest  

WORKDIR /root/

# Copy ONLY the binary from the "builder" stage
COPY --from=builder /app/myapp .

# Command to run the binary
CMD ["./myapp"]
