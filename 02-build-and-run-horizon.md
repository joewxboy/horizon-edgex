# Build and Run the Horizon Dev Services

This continues the instructions from [Horizon Dev Services Setup](01-horizon-services-setup.md).

*NOTE*: This assumes you are running as root (with `sudo`) on the Ubuntu VM as described in the previous set of instructions and are continuing in the same session.

## Build the Services

``` bash
cd /go/src/github.com/open-horizon/anax
make
cd test
make
```

If there were any errors (not warnings), you may have missed a step.  Please go back and check.

Next, edit the Makefile to expose the exchange service to your VM's external IP address.

Go to the `run-exchange:` section and change the IP address from `127.0.0.1` to `0.0.0.0`.

Then build and run the services:

``` bash
make run-dockerreg
make run-agbot
make run-exchange
```

Last,  as the root user, we'll create an organization named `testorg` and create a user in that organization with admin privileged named `joe` with a password of `cool`:

``` bash
export HZN_EXCHANGE_URL=http://127.0.0.1:8080/v1
export EDGE_EXCHANGE_ROOT_PASS="Horizon-Rul3s"
export HZN_EXCHANGE_USER_AUTH="root/root:$EDGE_EXCHANGE_ROOT_PASS"

curl -sSf -X POST -u "root/root:$EDGE_EXCHANGE_ROOT_PASS" -H "Content-Type:application/json" -d '{"label": "testorg", "description": "Organization for Testing"}' $HZN_EXCHANGE_URL/orgs/testorg | jq .

curl -sSf -X POST -u "root/root:$EDGE_EXCHANGE_ROOT_PASS" -H "Content-Type:application/json" -d '{"password":"cool","email": "joe@everywhere.com", "admin": true}' $HZN_EXCHANGE_URL/orgs/testorg/users/joe | jq .

export ORG_ID=testorg
export HZN_EXCHANGE_USER_AUTH=joe:cool
```

## Next

[Install the Anax Agent](03-install-agent.md) software.
