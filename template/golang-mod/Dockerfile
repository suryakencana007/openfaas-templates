FROM golang:1.12.4-alpine as build

RUN apk --no-cache add curl git gcc \
        g++ \
        openssh \
    && echo "Pulling watchdog binary from Github." \
    && curl -sSLf https://github.com/openfaas-incubator/of-watchdog/releases/download/0.4.6/of-watchdog > /usr/bin/fwatchdog \
    && chmod +x /usr/bin/fwatchdog \
    && apk del curl --no-cache

RUN mkdir -p /app/src/handler
WORKDIR /app/src/handler
COPY . .

RUN export GO111MODULE=on
RUN go mod init handler
RUN go mod download

# Run a gofmt and exclude all vendored code.
RUN test -z "$(gofmt -l $(find . -type f -name '*.go' -not -path "./vendor/*" -not -path "./function/vendor/*"))" || { echo "Run \"gofmt -s -w\" on your Golang code"; exit 1; }

RUN GOOS=linux \
    go build -ldflags="-s -w" -o handler . && \
    go test $(go list ./... | grep -v /vendor/) -cover

FROM alpine:3.8
# Add non root user and certs
RUN apk --no-cache add ca-certificates \
    && addgroup -S app && adduser -S -g app app \
    && mkdir -p /home/app \
    && chown app /home/app

WORKDIR /home/app

COPY --from=build /app/src/handler/handler    .
COPY --from=build /usr/bin/fwatchdog         .
COPY --from=build /app/src/handler/function/  .

RUN chown -R app /home/app

USER app

ENV fprocess="./handler"
ENV mode="http"
ENV upstream_url="http://127.0.0.1:8081"

CMD ["./fwatchdog"]
