FROM ubuntu:20.10
ENV MY_SERVICE_PORT=8080
LABEL Author="wuzhibin05@163.com"
ADD bin/amd64/httpserver /httpserver
EXPOSE 8080
ENTRYPOINT /httpserver