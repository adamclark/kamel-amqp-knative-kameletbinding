## Pre-requisites
- The OpenShift Serverless operator has been installed to your cluster and knative serving has been configured.
- The Camel K operator has been installed to your cluster.
- The AMQ Broker operator has been installed to your cluster.

## Deployment
Log into you OpenShift cluster and create a new project:
```
oc login ...
oc new-project kamel-amqp-knative-kb
```

Create the AMQ broker and address:
```
oc apply -f amq/broker-cr.yaml
oc apply -f amq/address-1-cr.yaml
```

Create a knative service to test with:
```
kn service create event-display-1 \
  --concurrency-target=1 \
  --image=quay.io/openshift-knative/knative-eventing-sources-event-display:latest
```

Deploy the Camel K app (it will take a few mins the first time while the IntegrationKit is built):
```
oc apply -f amqp-knative-kb.yaml
```

## Testing

- Send a message to `testAddress1` (this can be done via the AMQ console). You should see a pod for `event-display-1` spin up and the content of the message will appear in the log for `event-display-1`.