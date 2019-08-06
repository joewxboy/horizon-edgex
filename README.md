# horizon-edgex
Registering EdgeX Foundry as an Open Horizon Pattern

## High Level Steps

### If running the Horizon Agent locally, connecting to a remote Exchange

#### Install Pre-requisites and update indexes

On OSX, test to see if brew is installed by typing `brew --version`.  If it is not installed, use the following:

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

On OSX, update brew, optionally upgrade packages, install `wget` utility.

```
brew update
brew upgrade
brew install wget
```

On Ubuntu, update apt package manager and optionally upgrade packages.
NOTE: You may need to run as root, in which case you should prefix the following lines with `sudo`.

```
apt-get -y update
apt-get -y upgrade
```

#### Install Docker

Check to see if Docker is installed by running:

```
docker --version
```

If it is not installed, use the following steps to install it.

On OSX, download the package with the following line, then use Finder to double-click on the file to install.

```
wget https://download.docker.com/mac/stable/Docker.dmg
```

On Linux, run the following line to install.  NOTE: You may need to run it as root, in which case you should prefix the line below with `sudo`.

```
curl -fsSL get.docker.com | sh
```

#### Install the Horizon Agent software

On OSX, download the Agent software with the following line, then use Finder to double-click on the file to install.

```
wget http://pkg.bluehorizon.network/macos/horizon-cli-2.23.11.pkg
```

On Ubuntu, add the repository with the Horizon Agent deb package, and then update with your package manager to install.
NOTE: Copy the first 7 lines verbatim (to the blank line after EOF) and paste in one operation, then the subsequent line.
NOTE: You may need to run `apt-get` as root, in which case you should prefix that line with `sudo`.

```
wget -qO - http://pkg.bluehorizon.network/bluehorizon.network-public.key | apt-key add -
aptrepo=updates
cat <<EOF > /etc/apt/sources.list.d/bluehorizon.list
deb [arch=$(dpkg --print-architecture)] http://pkg.bluehorizon.network/linux/ubuntu xenial-$aptrepo main
deb-src [arch=$(dpkg --print-architecture)] http://pkg.bluehorizon.network/linux/ubuntu xenial-$aptrepo main
EOF

apt-get -y install bluehorizon
```

### Configure the Agent to connect to the Exchange

TBD

### Register the Service with the Exchange

Ensure you can run docker without sudo.

Linux: 
``` bash
sudo usermod -aG docker $USER
```

OSX:
``` bash
sudo dseditgroup -o edit -a $USER -t user admin
sudo dseditgroup -o edit -a $USER -t user wheel
```

And ensure you have Horizon keys, if you don't already. 
To check, look for a file named `~/.hzn/keys/service.private.key`

``` bash
hzn key create "<org>" "<email>"
```

And ensure you `source` your credentials and other env vars, 
like you would to register a pattern.

And ensure you log in to DockerHub:

``` bash
docker login -u <username>
```

Populate the environment variables:

```
source mycreds
```

Publish your service (after verifying it):

NOTE: If your service definition file points to services that you cannot update in the registry, download them and upload to your registry before running the following command.

```
hzn exchange service publish -I -f service-jpw.json -k ~/.hzn/keys/service.private.key -K ~/.hzn/keys/service.public.pem
```

Optionally, check that the service is now in the exchange:

```
hzn exchange service list
```

TBD

### Register the Pattern with the Exchange

Publish the pattern:

```
hzn exchange pattern publish -f ./pattern.json
```

Optionally, confirm that the pattern is in the exchange:

```
hzn exchange pattern list
```

TBD

### Configure the Agent for the Pattern

```
hzn register -n $EXCHANGE_NODEAUTH $HZN_ORG_ID $PATTERN -f input.json
```

TBD
