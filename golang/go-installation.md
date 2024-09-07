# Go installation

## Compiler

1) Google `golang isntall` and install it from `go.dev` for your OS.

    ```bash
    sudo apt-get remove -y golang golang-go

    GO_VERSION=1.23.0
    sudo -s
    mkdir -p /usr/local/go
    mkdir -p ~/go
    tar -C /usr/local/go/ -xzf go${GO_VERSION}.linux-amd64.tar.gz

    rm -rf /usr/local/go/go${GO_VERSION}
    mv /usr/local/go/go /usr/local/go/go${GO_VERSION}
    ls /usr/local/go
    ```

2) Add to `~/.bashrc` snippet:

    ```bash
    ### Go

    GO_VERSION=1.23.0

    # Go compiler (SDK)
    GOROOT="/usr/local/go/go${GO_VERSION}"
    export GOROOT

    # Go binaries
    GOPATH="${HOME}/go_bin"
    export GOPATH

    # Changes to $PATH
    export PATH="${PATH}:${GOROOT}/bin:${GOPATH}/bin"
    ```

3) Check:

    ```bash
    go help
    go env
    ```

4) Uninstall

    ```bash
    sudo rm -rf /usr/local/go/go1.22.6/
    ```

Usage:

```bash
# Из корня репозитория.
go test ./sum/...
```

## Linters

### golangci-lint

Repository: [github](https://github.com/golangci/golangci-lint#binary). Installation is just one `curl`.

Usage:

```bash
# Check code format
golangci-lint run ./sum/...

# Another way to format code
go fmt sum/sum.go
```
