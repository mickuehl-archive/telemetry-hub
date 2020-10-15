# telemetry-hub

### Create the project

```shell
oc new-project telemetry-hub
```

### Install Red Hat AMQ Online

Install Red Hat AMQ Online from the OperatorHub into project `telemetry-hub`

### Deploy the configuration

```shell
oc create -f bundles -n telemetry-hub

oc create -f 001_address-space-none.yaml -n telemetry-hub
oc create -f 001-AuthenticationService-none -n telemetry-hub

oc create -f 010_address-space.yaml -n telemetry-hub
oc create -f 020_address.yaml -n telemetry-hub
oc create -f 030_user.yaml -n telemetry-hub
```

### Connection

oc get addressspace telemetry-hub -o jsonpath='{.status.endpointStatuses[?(@.name=="mqtt")].externalHost}{"\n"}'

#### Extract Certificates

oc get addressspace telemetry-hub -o jsonpath='{.status.endpointStatuses[?(@.name=="mqtt")].cert}{"\n"}' | base64 --decode > client.crt
oc get addressspace telemetry-hub -o jsonpath='{.status.caCert}' | base64 --decode > ca.crt

### 