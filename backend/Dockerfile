FROM golang:1.24-alpine AS build

WORKDIR /app

COPY go.mod go.sum ./

RUN go mod download

COPY . .

RUN CGO_ENABLED=0 GOOS=linux go build -o backend ./cmd/api

FROM alpine:3.21

WORKDIR /app

COPY --from=build /app/backend .

RUN apk add --no-cache curl && adduser -D backend

USER backend

EXPOSE 8081

HEALTHCHECK CMD curl --fail http://localhost:8081/health || exit 1

ENTRYPOINT ["/app/backend"]