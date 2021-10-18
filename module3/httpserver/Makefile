export tag=v1.0
root:
	export ROOT=cncamp/cncamp/golang

build:
	echo "building httpserver binary"
	mkdir -p bin/amd64
	CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o bin/amd64 .

release: build
	echo "building httpserver image"
	docker build -t wuzb/httpserver:${tag} .

push: release
	echo "pushing image to repository"
	docker push wuzb/httpserver:${tag}