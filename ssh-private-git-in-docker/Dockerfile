# syntax=docker/dockerfile:experimental

FROM golang:1.14.7-alpine as builder

WORKDIR /app

COPY go.mod go.sum ./

RUN set -ex; \
    apk update; \
    apk add --no-cache \
        git \
        gcc \
        musl-dev \
        openssh-client \
        ca-certificates && \
    git config --system url."ssh://git@gitlab.com/".insteadOf "https://gitlab.com/" && \
    mkdir -p -m 0600 ~/.ssh && ssh-keyscan gitlab.com >> ~/.ssh/known_hosts

RUN --mount=type=ssh,id=git-library go mod download

COPY . .

RUN CGO_ENABLED=0 go build -o goapp main.go

##########################################

FROM alpine:3.12.0

WORKDIR /app

COPY --from=builder /app/goapp ./goapp

EXPOSE 1323

CMD ["./goapp"]
