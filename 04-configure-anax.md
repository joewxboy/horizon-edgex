# Configure the Anax Agent

This continues the instructions from [Horizon Dev Services Setup](01-horizon-services-setup.md) and 
[Build and Run](02-build-and-run-horizon.md) the Horizon Dev Services and 
[Install the Anax agent](03-install-agent.md) software.

First, configure environment variables so the agent can connect to the exchange.

*NOTE*: Replace `127.0.0.1` below with the actual _external_ IP address of the exchange VM.

``` bash
export HZN_EXCHANGE_URL=http://127.0.0.1:8080/v1
export ORG_ID=testorg
export HZN_ORG_ID=testorg
export HZN_EXCHANGE_USER_AUTH=joe:cool
hzn exchange user list
```

The results of `hzn exchange user list` should be something like the following:

``` json
{
  "testorg/joe": {
    "admin": true,
    "email": "joe@everywhere.com",
    "lastUpdated": "2019-08-19T12:25:06.754Z[UTC]",
    "password": "********",
    "updatedBy": "root/root"
  }
}
```

Second, we'll generate signing keys and place them in a temporary directory:

``` bash
mkdir -p /tmp/hzndev
chmod a+rwx /tmp/hzndev
hzn key create -l 4096 testorg joe@everywhere.com -d /tmp/hzndev
```

This will return something like:

```
Created keys:
 	/tmp/hzndev/testorg-433683b019219acf8cb22790746d92937b62d994-private.key
	/tmp/hzndev/testorg-433683b019219acf8cb22790746d92937b62d994-public.pem
```

Copy the string in the key filenames between `testorg-` and `-p` in the filename.  We'll use that below.

Place the [service-jpw.json](service-jpw.json) and [pattern.json](pattern.json) files 
into the `/tmp/hzndev` folder, or modify the paths below to point to where they currently reside.

This will publish the _service_ definition to the exchange.  Remember to replace the `433...` string with your value:

```
hzn exchange service publish -I -f /tmp/hzndev/service-jpw.json \
  -k /tmp/hzndev/testorg-433683b019219acf8cb22790746d92937b62d994-private.key \
  -K /tmp/hzndev/testorg-433683b019219acf8cb22790746d92937b62d994-public.pem
```

Now check to ensure that the service definition was published and is available in the exchange:

```
hzn exchange service list
```

The above should respond with the following, if successful:

``` json
[
  "testorg/com.github.joewxboy.horizon.edgex_1.0.1_amd64"
]
```

Next, publish the _pattern_ to the exchange:

```
hzn exchange pattern publish -f /tmp/hzndev/pattern.json
```

Now check to ensure the pattern is available:

```
hzn exchange pattern list
```

It should respond with:

``` json
[
  "testorg/pattern-edgex-amd64"
]
```

Last, let's configure the Anax agent so that it can register for the pattern.

Set some environment variables:

``` bash
export HZN_ORG_ID='testorg'
export HZN_DEVICE_ID='ubuntuvm'
export HZN_DEVICE_TOKEN='iamnotapw'
export HZN_EXCHANGE_USER_AUTH='joe:cool'
export EXCHANGE_NODEAUTH="$HZN_DEVICE_ID:$HZN_DEVICE_TOKEN"
export PATTERN='testorg/pattern-edgex-amd64'
```

Create a file in /tmp/hzndev named [input.json](input.json):

``` json
{
  "services": [
    {
      "org": "testorg",
      "url": "com.github.joewxboy.horizon.edgex",
      "versionRange": "[0.0.0,INFINITY)",
      "variables": {
      	"EXPORT_DISTRO_CLIENT_HOST":     "export-client",
        "EXPORT_DISTRO_DATA_HOST":       "edgex-core-data",
        "EXPORT_DISTRO_CONSUL_HOST":     "edgex-config-seed",
        "EXPORT_DISTRO_MQTTS_CERT_FILE": "none",
        "EXPORT_DISTRO_MQTTS_KEY_FILE":  "none",
        "LOG_LEVEL":                     "info",
        "LOGTO":                         ""
      }
    }
  ]
}
```

Manually create the volumes that will be used:

```
docker volume create db-data
docker volume create log-data
docker volume create consul-data
docker volume create consul-config
```

(Optionally) verify the volumes have been created: `docker volume list`

Manually setup and open the permissions on the host directories being bound:

```
mkdir -p /var/run/edgex/logs
mkdir -p /var/run/edgex/data
mkdir -p /var/run/edgex/consul/data
mkdir -p /var/run/edgex/consul/config
mkdir -p /root/res
chmod -R a+rwx /var/run/edgex
chmod -R a+rwx /root/res
```

Copy the configuration files from the `res` folder in this repository to the `/root/res` folder on the host.

Then register for the pattern:

``` bash
hzn register -n $EXCHANGE_NODEAUTH $HZN_ORG_ID $PATTERN -f /tmp/hzndev/input.json
```

To confirm that your edge node is registered for the pattern, run:

``` bash
hzn node list
```

and confirm that the response shows your node ID of `ubuntuvm` 
and that you're configured for the `testorg/pattern-edgex-amd64` pattern.

To check on the status of the agreement, use:

``` bash
hzn agreement list
```

or to see verbose details:

``` bash 
hzn eventlog list
```

And once the agreement is finalized, you should be able to see the containers running:

``` bash
sudo docker ps
```

-----

## The End
