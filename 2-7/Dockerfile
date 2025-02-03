FROM golang:alpine AS builder

WORKDIR /app
COPY main.go .
RUN go build -o myapp main.go

FROM alpine
COPY --from=builder /app/myapp /myapp
CMD ["/myapp"]
