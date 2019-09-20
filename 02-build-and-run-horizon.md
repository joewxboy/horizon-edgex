# Build and Run the Open Horizon Services

This continues the instructions from [Install the Open Horizon Services](01-horizon-services-setup.md).

*NOTE*: This assumes you are running as root (with `sudo`) on the Ubuntu VM 
as described in the previous set of instructions and are continuing in the same session. 
If you are not, you may need to export all previous environment variables from step 1.

### Create and Persist Environment Variables

NOTE: Replace `${IP}` in the two places below with the actual IP address.

``` bash
export HZN_ORG_ID=testorg
echo "export HZN_ORG_ID=testorg" >> ~/.bashrc
export HZN_EXCHANGE_URL=http://${IP}:3090/v1/
echo "export HZN_EXCHANGE_URL=http://${IP}:3090/v1/" >> ~/.bashrc
export HZN_EXCHANGE_USER_AUTH='root/root:Horizon-Rul3s'
echo "export HZN_EXCHANGE_USER_AUTH='root/root:Horizon-Rul3s'" >> ~/.bashrc
```

### Edit the Services Configuration JSON

NOTE: Again, this is assuming that you cloned this repository and it is located at `./horizon-edgex/`.

Change to the `oh` folder and edit the `config.json` file appropriately.

``` bash
cd horizon-edge/oh
```

NOTE: I will be referring to nodes in the `config.json` file using XPath-like dot notation.

If nothing else, substitute your server's IP address for the `${IP}` placeholder at `.horizon.hostname`.

The AgBot's name and token are specified at `.services.agbot.bot`.

The Exchange's credentials are at both `.exchange.root` and `.exchange.password`, and `.exchange.admin`.

The organization is specified at `.horizon.namespace` and `.exchange.org`.

These instructions are assuming, and will be referring to, the default values specified at the nodes listed above.

### Build and Start the Services

NOTE: I am assuming that you are still in the `horizon-edgex/oh` directory.

``` bash
make
make up
```

Let's confirm that the Exchange is running (and our environment variable are configured correctly) by 
requesting the endpoints for `version` and `status`:

``` bash
curl -u ${HZN_EXCHANGE_USER_AUTH} ${HZN_EXCHANGE_URL}admin/version
```

``` bash
curl -u ${HZN_EXCHANGE_USER_AUTH} ${HZN_EXCHANGE_URL}admin/status | jq .
```

If all is well, let's continue by listing the existing Organizations:

``` bash
curl -u ${HZN_EXCHANGE_USER_AUTH} ${HZN_EXCHANGE_URL}orgs | jq .
```

Add an Organization named `testorg`:

``` bash
curl -sSf -X POST -u ${HZN_EXCHANGE_USER_AUTH} -H "Content-Type:application/json" -d '{"label": "testorg", "description": "Organization for Testing"}' ${HZN_EXCHANGE_URL}orgs/testorg | jq .
```

And then list the existing Organizations again to see `testorg` now in the list:

``` bash
curl -u ${HZN_EXCHANGE_USER_AUTH} ${HZN_EXCHANGE_URL}orgs | jq .
```

If all is well, let's "prime the pump" by clearing the tables and refilling to a known state:

``` bash
make prime
```

Please note that the step you just performed also told your new AgBot `agbot1` to listen for  
Deployment Patterns and Policies from the `testorg` Organization.

### Add Admin User

First, list the current users in `testorg`, which should be empty and throw a 404 error:

``` bash
curl -sSf -u ${HZN_EXCHANGE_USER_AUTH} ${HZN_EXCHANGE_URL}orgs/testorg/users | jq .
```

Then add the admin user:

``` bash
curl -sSf -X POST -u ${HZN_EXCHANGE_USER_AUTH} -H "Content-Type:application/json" -d '{"password":"cool","email": "joe@everywhere.com", "admin": true}' ${HZN_EXCHANGE_URL}orgs/testorg/users/joe | jq .
```

Then list the current users again to confirm that the new user is now in the list:

``` bash
curl -sSf -u ${HZN_EXCHANGE_USER_AUTH} ${HZN_EXCHANGE_URL}orgs/testorg/users | jq .
```

## Next

[Install the Open Horizon Agent](03-install-agent.md).
