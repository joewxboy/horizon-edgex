# Install the Anax Agent software

This continues the instructions from [Horizon Dev Services Setup](01-horizon-services-setup.md) and 
[Build and Run](02-build-and-run-horizon.md) the Horizon Dev Services.

## for OSX

Install brew package manager

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

brew update
brew upgrade
brew install wget
```

[Get and install](https://docs.docker.com/docker-for-mac/install/) Docker

```
wget https://download.docker.com/mac/stable/Docker.dmg
```

Test after install

```
docker --version
```

Download and install the Edge Fabric client

```
wget http://pkg.bluehorizon.network/macos/horizon-cli-2.23.1.pkg
```

Install first, then start the client

```
horizon-container start
```

Test after install

```
hzn version

hzn node list
```

-----

## For Ubuntu

IMPORTANT, DO NOT SKIP THIS STEP

Become root so the following several steps will work properly

```
sudo -s
```

Update the package manager

```
apt -y update
```

Install and test Docker

```
curl -fsSL get.docker.com | sh
docker --version
```

Configure package manager for the Edge Fabric client

IMPORTANT, Copy the next 6 lines verbatim and paste in one operation

```
wget -qO - http://pkg.bluehorizon.network/bluehorizon.network-public.key | apt-key add -
aptrepo=updates
cat <<EOF > /etc/apt/sources.list.d/bluehorizon.list
deb [arch=$(dpkg --print-architecture)] http://pkg.bluehorizon.network/linux/ubuntu xenial-$aptrepo main
deb-src [arch=$(dpkg --print-architecture)] http://pkg.bluehorizon.network/linux/ubuntu xenial-$aptrepo main
EOF
```

Refresh package manager index list

```
apt-get -y update
```

Install and test the Edge Fabric Client

```
apt-get install -y bluehorizon
```

IMPORTANT, exit out of root, back to your user account

```
exit
```

Check the version to confirm that Horizon is installed and working

```
hzn version
```

Check to ensure that it is working and your machine/node is "unconfigured"

```
hzn node list
```

# next

[Configure the Agent](04-configure-anax.md).