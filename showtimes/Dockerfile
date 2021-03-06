# golang alpine 1.15.6-alpine as base image
FROM golang:1.15.6-alpine AS builder
# create appuser.
RUN adduser -D -g '' elf
# create workspace
WORKDIR /opt/app/
COPY go.mod .
COPY go.sum .
# fetch dependancies
RUN go mod download
RUN go mod verify
# copy the source code as the last step
COPY . .
# build binary
RUN CGO_ENABLED=0 GOOS=linux go build -ldflags="-w -s" -a -installsuffix cgo -o /go/bin/cinema-showtimes .


# build a small image
FROM alpine:3.12.3
LABEL language="golang"
LABEL org.opencontainers.image.source https://github.com/mmorejon/microservices-docker-go-mongodb
# import the user and group files from the builder.
COPY --from=builder /etc/passwd /etc/passwd
# copy the static executable
COPY --from=builder --chown=elf:1000 /go/bin/cinema-showtimes /cinema-showtimes
# copy default config file
COPY --chown=elf:1000 common/config.json /common/config.json
# use an unprivileged user.
USER elf
# service listens on port 8080.
EXPOSE 8080
# run app
ENTRYPOINT ["./cinema-showtimes"]