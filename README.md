# rsocket-server

This application works in tandem with [rsocket-client](https://github.com/ciberkleid/rsocket-client).
Please see the client app README for complete instructions.



## Run in Cloud Foundry using TCP:
```
cf push -f manifest-tcp.yml
```

## Run in Cloud Foundry using Websocket:
```
cf push -f manifest-ws.yml
```

## Run in Cloud Foundry using an internal TCP route:
To complete this step, you will need to deploy the `rsocket-client` application as well. Instructions [here](https://github.com/ciberkleid/rsocket-client).
```
cf push -f manifest-internal.yml
# Note: please deploy client before proceeding
cf add-network-policy rsocket-client-internal --destination-app rsocket-server-internal --protocol tcp --port 8080
cf scale rsocket-server-internal -i 1
# Note: please restart the client application
```