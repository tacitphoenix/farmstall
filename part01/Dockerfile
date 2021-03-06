FROM golang:alpine as builder
RUN apk update && apk add --no-cache \
  git
RUN mkdir /build

# Download dependencies
ADD ./go.mod /build/
ADD ./go.sum /build/
WORKDIR /build
RUN CGO_ENABLED=0 GOOS=linux go mod download

# Build
ADD . /build/
RUN CGO_ENABLED=0 \
  GOOS=linux \
  go build \
  -a \
  -installsuffix cgo \
  -ldflags '-extldflags "-static"' \
  -o farmstall .

# Copy over artifacts
FROM alpine
COPY --from=builder /build/farmstall /app/

# Assets
COPY ./openapi.yaml  /app/
COPY ./img           /app/img
COPY ./site/build    /app/site/build

WORKDIR /app
ENV PORT 80
EXPOSE 80
CMD ["./farmstall"]
