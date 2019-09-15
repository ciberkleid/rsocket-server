# rsocket-server

This application works in tandem with [rsocket-client](https://github.com/ciberkleid/rsocket-client) and/or [rsocket-client-js](https://github.com/ciberkleid/rsocket-client-js) (Java and JavaScript RSocket clients, respectively).


These setup instructions provide guidance on running the demo locally and on Cloud Foundry. They demonstrate the use of TCP as well as WebSocket, and show the four interactions of RSocket (fire and forget, request/reply, request stream, and request channel).
## TO-DO
- Complete the implementation and setup instructions for the JavaScript client ([rsocket-client-js](https://github.com/ciberkleid/rsocket-client-js)).

## Setup
Clone & build projects:
```
export DEMO_HOME=`echo $PWD`
git clone https://github.com/ciberkleid/rsocket-server.git
git clone https://github.com/ciberkleid/rsocket-client.git
cd $DEMO_HOME/rsocket-server
./mvnw clean package -DskipTests
cd $DEMO_HOME/rsocket-client
./mvnw clean package -DskipTests
cd $DEMO_HOME
```

## Option 1: Run locally using TCP

### Start server
In one terminal window, start the server locally:
```
cd $DEMO_HOME/rsocket-server
./mvnw spring-boot:run -Dspring-boot.run.arguments=--spring.profiles.active=tcplocal
```

The server will listen for rsocket requests over TCP on port 7000.

### Start client
In another terminal window, start the client locally:
```
cd $DEMO_HOME/rsocket-client
./mvnw spring-boot:run -Dspring-boot.run.arguments=--spring.profiles.active=tcplocal2local
```

The client will make a TCP rsocket connection to localhost:7000.
The client will also listen for HTTP requests on port 8080.

### Test it out - make HTTP requests to client (client will make TCP requests to server)
In another terminal window, send HTTP requests to the client.
Each of the following requests demonstrates a different interaction between client and server using rsocket.
Check the output of the command and the logging in the server and client terminal windows to observe the behavior of each interaction type.

```
# Fire and forget
curl http://localhost:8080/hello

# Request / response
curl http://localhost:8080/name/jellyfish

# Stream
curl http://localhost:8080/stream

# Channel
curl http://localhost:8080/channel
```

## Option 2: Run locally using Websocket

### Start server
In one terminal window, start the server locally:
```
cd $DEMO_HOME/rsocket-server
./mvnw spring-boot:run -Dspring-boot.run.arguments=--spring.profiles.active=ws
```

The server will listen for rsocket requests over Websocket on port 8080.

### Start client
In another terminal window, start the client locally:
```
cd $DEMO_HOME/rsocket-client
./mvnw spring-boot:run -Dspring-boot.run.arguments=--spring.profiles.active=wslocal2local
```

The client will make a Websocket rsocket connection to localhost:8080.
The client will also listen for HTTP requests on port 8081.

### Test it out - make HTTP requests to client (client will make Websocket requests to server)
In another terminal window, send HTTP requests to the client.
Each of the following requests demonstrates a different interaction between client and server using rsocket.
Check the output of the command and the logging in the server and client terminal windows to observe the behavior of each interaction type.

```
# Fire and forget
curl http://localhost:8081/hello

# Request / response
curl http://localhost:8081/name/jellyfish

# Stream
curl http://localhost:8081/stream

# Channel
curl http://localhost:8081/channel
```

## Option 3: Run server on Cloud Foundry and client locally using TCP

### Start server
Using the cf cli, target a space on a Cloud Foundry foundation that has a tcp type domain.
Edit the `manifest-tcp.yml` in `rsocket-server` so that the route is valid for your foundation.
Push the server to Cloud Foundry.
```
cd $DEMO_HOME/rsocket-server
cf push -f manifest-tcp.yml
```

### Start client
Edit `application.yml` in `rsocket-client` so that the routes match your Cloud Foundry domain.
In another terminal window, start client locally:
```
cd $DEMO_HOME/rsocket-client
./mvnw spring-boot:run -Dspring-boot.run.arguments=--spring.profiles.active=tcplocal2cloud
```

### Test it out - make HTTP requests to client (client will make TCP requests to server)
In another terminal window, send HTTP requests to the client.
The client will make TCP requests to the server on Cloud Foundry using a public TCP route.
Monitor activity on the server using `cf logs rsocket-server-tcp`.

```
# Fire and forget
curl http://localhost:8080/hello

# Request / response
curl http://localhost:8080/name/jellyfish

# Stream
curl http://localhost:8080/stream

# Channel
curl http://localhost:8080/channel
```

## Option 4: Run server on Cloud Foundry and client locally using Websocket

### Start server
Using the cf cli, target a space on Cloud Foundry.
Edit the `manifest-ws.yml` in `rsocket-server` so that the route is valid for your foundation.
Push the server to Cloud Foundry.
```
cd $DEMO_HOME/rsocket-server
cf push -f manifest-ws.yml
```

### Start client
Edit `application.yml` in `rsocket-client` so that the routes match your Cloud Foundry domain.
In another terminal window, start client locally:
```
cd $DEMO_HOME/rsocket-client
./mvnw spring-boot:run -Dspring-boot.run.arguments=--spring.profiles.active=wslocal2cloud
```

### Test it out - make HTTP requests to client (client will make TCP requests to server)
In another terminal window, send HTTP requests to the client.
The client will make Websocket requests to the server on Cloud Foundry using a public Websocket route.
Monitor activity on the server using `cf logs rsocket-server-ws`.

```
# Fire and forget
curl http://localhost:8080/hello

# Request / response
curl http://localhost:8080/name/jellyfish

# Stream
curl http://localhost:8080/stream

# Channel
curl http://localhost:8080/channel
```

## Option 5: Run server and client on Cloud Foundry using a TCP route
### Start server
Using the cf cli, target a space on a Cloud Foundry foundation that has an internal domain.
Edit `manifest-tcp.yml` in `rsocket-server` so that the route is valid for your foundation.
Push the server to Cloud Foundry.
```
cd $DEMO_HOME/rsocket-server
cf push -f manifest-tcp.yml
```

### Start client
Edit `manifest-tcp.yml` in `rsocket-client` so that the routes match your Cloud Foundry domain.
Deploy the client to Cloud Foundry.
```
cd $DEMO_HOME/rsocket-client
cf push -f manifest-tcp.yml
```

### Test it out - make HTTP requests to client (client will make TCP requests to server on an internal route)
In another terminal window, send HTTP requests to the client.
Each of the following requests demos a different interaction between client and server using rsocket.
Check the output of the command and the logging in the server and client terminal windows to observe the behavior of each interaction type.

```
# Fire and forget
curl http://rsocket-client-tcp.apps.clearlake.cf-app.com/hello

# Request / response
curl http://rsocket-client-tcp.apps.clearlake.cf-app.com/name/jellyfish

# Stream
curl http://rsocket-client-tcp.apps.clearlake.cf-app.com/stream

# Channel
curl http://rsocket-client-tcp.apps.clearlake.cf-app.com/channel
```

## Option 6: Run server and client on Cloud Foundry using a Websocket TCP route
### Start server
Using the cf cli, target a space on a Cloud Foundry foundation that has an internal domain.
Edit `manifest-ws.yml` in `rsocket-server` so that the route is valid for your foundation.
Push the server to Cloud Foundry.
```
cd $DEMO_HOME/rsocket-server
cf push -f manifest-ws.yml
```

### Start client
Edit `manifest-ws.yml` in `rsocket-client` so that the routes match your Cloud Foundry domain.
Deploy the client to Cloud Foundry.
```
cd $DEMO_HOME/rsocket-client
cf push -f manifest-ws.yml
```

### Test it out - make HTTP requests to client (client will make TCP requests to server on an internal route)
In another terminal window, send HTTP requests to the client.
Each of the following requests demos a different interaction between client and server using rsocket.
Check the output of the command and the logging in the server and client terminal windows to observe the behavior of each interaction type.

```
# Fire and forget
curl http://rsocket-client-ws.apps.clearlake.cf-app.com/hello

# Request / response
curl http://rsocket-client-ws.apps.clearlake.cf-app.com/name/jellyfish

# Stream
curl http://rsocket-client-ws.apps.clearlake.cf-app.com/stream

# Channel
curl http://rsocket-client-ws.apps.clearlake.cf-app.com/channel
```

## Option 7: Run server and client on Cloud Foundry using an internal TCP route

### Start server
Using the cf cli, target a space on a Cloud Foundry foundation that has an internal domain.
Edit `manifest-internal.yml` in `rsocket-server` so that the route is valid for your foundation.
Push the server to Cloud Foundry.
```
cd $DEMO_HOME/rsocket-server
cf push -f manifest-internal.yml
```

### Start client
Edit `manifest-internal.yml` in `rsocket-client` so that the routes match your Cloud Foundry domain.
Deploy the client to Cloud Foundry.
Notice that we deploy with 0 instances initially.
We will scale to 1 instance after enabling direct communication between client and server.
```
cd $DEMO_HOME/rsocket-client
cf push -f manifest-internal.yml
cf add-network-policy rsocket-client-internal --destination-app rsocket-server-internal --protocol tcp --port 8080
cf scale rsocket-client-internal -i 1
```

### Test it out - make HTTP requests to client (client will make TCP requests to server on an internal route)
In another terminal window, send HTTP requests to the client.
Each of the following requests demos a different interaction between client and server using rsocket.
Check the output of the command and the logging in the server and client terminal windows to observe the behavior of each interaction type.

```
# Fire and forget
curl http://rsocket-client-tcp-internal.apps.clearlake.cf-app.com/hello

# Request / response
curl http://rsocket-client-tcp-internal.apps.clearlake.cf-app.com/name/jellyfish

# Stream
curl http://rsocket-client-tcp-internal.apps.clearlake.cf-app.com/stream

# Channel
curl http://rsocket-client-tcp-internal.apps.clearlake.cf-app.com/channel
```
