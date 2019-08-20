# Install the Anax Agent software

This continues the instructions from [Horizon Dev Services Setup](01-horizon-services-setup.md) and 
[Build and Run](02-build-and-run-horizon.md) the Horizon Dev Services.

Stand up your environment in the other tier for the Horizon Agent (Anax) and open a shell.  
Do not attempt this in the same environment as the Horizon Services 
due to port conflicts and environment variable collisions.
Instructions below are for either Ubuntu or OSX.

## For OSX

Install brew package manager

``` bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

brew update
brew upgrade
brew install wget
```

[Get and install](https://docs.docker.com/docker-for-mac/install/) Docker

``` bash
wget https://download.docker.com/mac/stable/Docker.dmg
```

Test after install

``` bash
docker --version
```

Download and install the Edge Fabric client

``` bash
wget http://pkg.bluehorizon.network/macos/horizon-cli-2.23.1.pkg
```

Install first (you may use the GUI and double-click on the file to install), 
then start the client using the command below:

``` bash
horizon-container start
```

Test after install

``` bash
hzn version

hzn node list
```

When you look at the output for `hzn node list`, pay attention to the line for the exchange api:

``` json
"exchange_api": "https://alpha.edge-fabric.com/v1/"
```

When the Anax agent is properly configured, 
it will point to the public IP address of the Horizon Services that you stood up earlier instead of `alpha.edge-fabric.com`, 
and is useful for confirming proper configuration.

To fix, stop the container, create a file at `/etc/default/horizon` with the contents below, then restart the container and test.

``` bash
horizon-container stop
sudo mkdir -p /etc/default
sudo touch /etc/default/horizon
```

Then edit the `/etc/default/horizon` file and insert the following contents. 
*NOTE*: Replace `127.0.0.1` with your Horizon Services public IP address.

```
HZN_EXCHANGE_URL=http://127.0.0.1:8080/v1/
HZN_FSS_CSSURL=https://alpha.edge-fabric.com/css/
```

Then re-start the container:

``` bash
horizon-container start
```

And then check the output to confirm that the exchange api value is now set to your exchange service public IP address:

``` bash
hzn node list
```

-----

## For Ubuntu

IMPORTANT, DO NOT SKIP THIS STEP

Become root so the following several steps will work properly

``` bash
sudo -s
```

Update the package manager

``` bash
apt -y update
```

Install and test Docker

``` bash
curl -fsSL get.docker.com | sh
docker --version
```

Configure package manager for the Edge Fabric client

IMPORTANT, Copy the next 6 lines verbatim and paste in one operation. 
*NOTE*: If you did not copy the CRLF after `EOF`, then you will need to press Enter/Return on your keyboard.

``` bash
wget -qO - http://pkg.bluehorizon.network/bluehorizon.network-public.key | apt-key add -
aptrepo=updates
cat <<EOF > /etc/apt/sources.list.d/bluehorizon.list
deb [arch=$(dpkg --print-architecture)] http://pkg.bluehorizon.network/linux/ubuntu xenial-$aptrepo main
deb-src [arch=$(dpkg --print-architecture)] http://pkg.bluehorizon.network/linux/ubuntu xenial-$aptrepo main
EOF

```

Refresh package manager index list

``` bash
apt-get -y update
```

Install and test the Edge Fabric Client

``` bash
apt-get install -y bluehorizon
```

IMPORTANT, exit out of root, back to your user account

``` bash
exit
```

Check the version to confirm that Horizon is installed and working

``` bash
hzn version
```

Check to ensure that it is working and your machine/node is "unconfigured"

``` bash
hzn node list
```
When you look at the output for `hzn node list`, pay attention to the line for the exchange api:

``` json
"exchange_api": "https://alpha.edge-fabric.com/v1/"
```

When the Anax agent is properly configured, 
it will point to the public IP address of the Horizon Services that you stood up earlier instead of `alpha.edge-fabric.com`, 
and is useful for confirming proper configuration.

To fix this, you will edit the responsible configuration file and then restart the agent service.

Edit the file at `/etc/horizon/hzn.json` using `sudo` 
and add the value for the exchange URL to be of the form `http://127.0.0.1:8080/v1/`. 
Please note two important details: first, the protocol is `http` instead of `https` (due to lack of a public signed cert), 
and second, the URL *must* end with a trailing slash, even though the corresponding environment variable does not.  
Replace `127.0.0.1` with the actual public IP address of your Horizon Services.

Restart the agent service:

``` bash
service restart horizon
```

and confirm that the change took effect by re-running `hzn node list` and checking the `exchange_api` value.

# Next

[Configure the Agent](04-configure-anax.md).
