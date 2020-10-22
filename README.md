# telemetry-hub

### Create the project

```shell
oc new-project telemetry-hub
```

### Install Red Hat AMQ Online

Install Red Hat AMQ Online from the OperatorHub into project `telemetry-hub`:

```shell
oc apply -f 010-operator.yaml -n telemetry-hub
```

### Basic configuration

```shell
oc create -f bundles -n telemetry-hub

oc create -f 020-authentication-service-none.yaml -n telemetry-hub
oc create -f 021-address-space-none.yaml -n telemetry-hub

oc create -f 030-address.yaml -n telemetry-hub
```

### Connection

#### Address of the MQTT endpoint

```shell
oc get addressspace telemetry-hub -o jsonpath='{.status.endpointStatuses[?(@.name=="mqtt")].externalHost}{"\n"}'
```

#### Extract Certificates

```shell
cd test

oc get addressspace telemetry-hub -o jsonpath='{.status.endpointStatuses[?(@.name=="mqtt")].cert}{"\n"}' | base64 --decode > client.crt
oc get addressspace telemetry-hub -o jsonpath='{.status.caCert}' | base64 --decode > ca.crt
```

### Testing

#### Local MQTT broker

```shell
docker pull eclipse-mosquitto
docker create --name mqtt -p 1883:1883 eclipse-mosquitto

docker start mqtt
```

Test the broker:

```shell
cd test
go run test.go
```

#### Test Red Hat AMQ Online

Get the MQTT endpoint address:

```shell
oc get addressspace telemetry-hub -o jsonpath='{.status.endpointStatuses[?(@.name=="mqtt")].externalHost}{"\n"}'
```

Test the MQTT endpoint:

```shell
cd test
go run test.go -host <mqtt_endpoint_address> -port 443 -secure -protocol ssl -queue data
```