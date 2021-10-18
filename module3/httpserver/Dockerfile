# 基础镜像
FROM golang:1.17.2 AS build
RUN apk add --no-cache git
RUN go get github.com/Wuzhibin05/cncamp
COPY Gopkg.lock Gopkg.toml /go/src/project/
WORKDIR /go/src/project/
RUN dep ensure -vendor-only
COPY . /go/src/project/
RUN go build -o /bin/project

# FROM scratch
# COPY --from=build /bin/project /bin/project
# ENTRYPOINT ["/bin/project"]
# CMD ["--help"]

FROM ubuntu:20.10
ENV MY_SERVICE_PORT=8080
LABEL Author="wuzhibin05@163.com"
COPY --from=build /bin/amd64/httpserver/bin/project
ADD bin/amd64/httpserver /httpserver
EXPOSE 8080
ENTRYPOINT /httpserver