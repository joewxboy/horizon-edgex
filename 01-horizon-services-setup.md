# Horizon Dev Services Setup

## Pre-requisites

### Horizon Services
+ OS: Ubuntu server, latest build recommended.  Instructions assume this.
+ VM: 4Gb RAM, 20Gb storage, 1vCPU, root access

### Horizon Agent (Anax)
+ OS: Ubuntu server or desktop (latest build recommended), or OSX.  
+ VM: 1Gb RAM, 10Gb storage, 1vCPU, root access

## Initial setup

Stand up your environment for Horizon Services and open a shell to update utilities.  
*NOTE*: You will perform all of these tasks as root through `sudo`.

``` bash
sudo -s
apt-get -y update
apt-get install -y curl jq make gcc
```

Install Go from source (don't use `apt` or `snap`) since the `src` and `pkg` and `bin` folders will be needed.

``` bash
curl https://dl.google.com/go/go1.11.4.linux-amd64.tar.gz | tar -xzf- -C /usr/local/
export PATH=$PATH:/usr/local/go/bin
```

You may confirm that Go was successfully installed and is in your PATH by checking the version:

``` bash
go version
```

Install Docker from source (to get the required newest version), and utilities.

``` bash
curl -fsSL get.docker.com | sh
apt-get install -y docker-compose
```

You can confirm Docker was installed successfully by checking the version:

``` bash
docker --version
```

Set up your GOPATH and related environment variables.  
*NOTE*: The GOPATH _must_ not be in a system folder or else the test scripts will throw errors.

``` bash
mkdir -p /go/src/github.com/open-horizon
export GOPATH=/go
export ANAX_SOURCE=/go/src/github.com/open-horizon/anax
```

Install required `go` utilities.  *NOTE*: You must use this method, and not `apt`, or the test scripts will throw errors.

``` bash
go get -u github.com/kardianos/govendor
```

Clone the Open Horizon project code.

``` bash
cd /go/src/github.com/open-horizon
git clone https://github.com/open-horizon/anax.git
```

## Next

[Build and Run](02-build-and-run-horizon.md) the Horizon Dev Services.
