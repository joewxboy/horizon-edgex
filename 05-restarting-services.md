# Restarting Horizon Dev Services

If you have to shut down or reboot the VM running the Horizon Dev Services, 
here's how to get back into the previous running state:

Login to the machine (of course).

Go to the source code.

``` bash
cd /go/src/github.com/open-horizon/anax
```

Remove the file we made changes to locally, so we can pull fresh code.

``` bash
rm test/Makefile
git pull -f
```

Build and compile the code.

``` bash
make clean
make
make deps
make fss
```

Go into the `test` folder.

``` bash
cd test
```

Allow the Exchange Service to listen to all IP addresses.  Edit the `run-exchange:` command in the `Makefile` to change the IP address from `127.0.0.1` to `0.0.0.0`.

Build and run the Services:

``` bash
make clean
make
make run-dockerreg
make run-exchange
make run-agbot
```

Confirm you have four services running: agbot, exchange-api, exchange-db, e2edevregistry:

``` bash
docker ps
```

Last,  as the root user, we'll create an organization named `testorg` 
and create a user in that organization with admin privileges named `joe` with a password of `cool`:

``` bash
export HZN_EXCHANGE_URL=http://127.0.0.1:8080/v1
export EDGE_EXCHANGE_ROOT_PASS="Horizon-Rul3s"
export HZN_EXCHANGE_USER_AUTH="root/root:$EDGE_EXCHANGE_ROOT_PASS"

curl -sSf -X POST -u "root/root:$EDGE_EXCHANGE_ROOT_PASS" -H "Content-Type:application/json" -d '{"label": "testorg", "description": "Organization for Testing"}' $HZN_EXCHANGE_URL/orgs/testorg | jq .

curl -sSf -X POST -u "root/root:$EDGE_EXCHANGE_ROOT_PASS" -H "Content-Type:application/json" -d '{"password":"cool","email": "joe@everywhere.com", "admin": true}' $HZN_EXCHANGE_URL/orgs/testorg/users/joe | jq .

export ORG_ID=testorg
export HZN_EXCHANGE_USER_AUTH=joe:cool
```
