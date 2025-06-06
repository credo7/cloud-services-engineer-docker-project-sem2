FROM golang:1.22.7-alpine3.20 AS builder

RUN apk update --no-cache && apk add --no-cache tzdata git curl

WORKDIR /build

RUN adduser -D -g '' appuser

ADD go.mod .
ADD go.sum .

RUN go mod download

COPY . .

RUN GOOS=linux CGO_ENABLED=0 go build -a -installsuffix cgo -ldflags="-s -w" -o /build/main ./cmd/api/*.go

FROM alpine:3.20

RUN addgroup -g 1001 -S appgroup && \
    adduser -u 1001 -S appuser -G appgroup

WORKDIR /app

COPY --from=builder /build/main .

USER appuser

EXPOSE 8081

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD wget -q --spider http://localhost:8081/health || exit 1

CMD ["./main"]
