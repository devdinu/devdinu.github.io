---
title: "Go Commands Cheatsheet"
date: 2021-05-12T08:24:58+05:30
tags: ["go", "cheatsheet"]
toc: true
---

# Go Commands Cheatsheet
List of commands we use around go ecosystem. considering for instance `revoir-service` as the repository

## Development

run small single file programs locally, without generating binary
```bash
go run main.go
# if there are multiple files you can do `go run *.go`
```

Fix formatting along with imports
```bash
goimports -l -w . # -w fixes the issues inline, -l list the files
```

build the go binary for production
```bash
# race flag enables data race detection
go build -race . -o ./bin/server

# if main.go is part of different directory say `cmd/server/main.go`
go build -race ./cmd/server

# inject value for `version` variables at runtime with golinker
go build -ldflags="-X 'main.version=1.1.0'"

go install .  # installs the binary in $GOPATH/bin directory and can run similar to other cmdline tools like `ls`
```

Build, source env vars and run in oneline
```bash
go build && source ./local.env && ./revoir-service
```

download a package as dependency
```bash
go get -v github.com/stretchr/testify@v1.7.0 # remove @vMajor.minor.patch to get latest version of gomodule

# if you've private go module dependencies
GOPRIVATE=org.github.io GOSUMDB=off GOPROXY=direct go build -x -v
```

## Go Modules
```bash
go mod init # create go.mod and go.sum file after creating go module
go mod tidy # adds missing and removes unused dependency
go mod why github.com/gorilla/mux # explain why packages or modules are needed
go mod vendor # downloads dependency to vendor directory, avoid this unless it's really necessary

```

## Debugging
debug gRPC based applications

```bash
GRPC_GO_LOG_VERBOSITY_LEVEL=99 GRPC_GO_LOG_SEVERITY_LEVEL=debug GRPC_TRACE=tcp,http,api ./revoir-service

# list all packages in this module
go list ./...
```

## Testing
```bash
go test -bench=. -v -benchtime=10s
go test -race ./... # runs tests on all packages
```

## Miscellaneous
### Package renaming
To rename a package from `revoir-service`  to `revoir-api` to different org `we-impact`
```bash
fgrep -rl '/cmd' . --include=*.go | xargs -L1 sed   -E  's!/github.com/weimpact/revoir-service!github.com/we-impact/revoir-api!g'
```

### Linting
Use [golangci](https://github.com/golangci/golangci-lint/) lint to run multiple linter tools.
```bash
curl -sSfL https://raw.githubusercontent.com/golangci/golangci-lint/master/install.sh | sh -s -- -b bin/ v1.30.0
        bin/golangci-lint run -v --deadline 5m0s
```

## Read more
* [gRPC env variables](https://github.com/grpc/grpc/blob/master/doc/environment_variables.md)
* [go tool link](https://golang.org/cmd/link/)
* [golangci lint](https://golangci-lint.run/)

