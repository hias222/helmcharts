# install

## additional components

In datamapping <https://github.com/hias222/datamapping> see details to connect to AWS IoT Core

Attention:
If you want to use multi frontend message queues, which means, that you use more than one microservices in the backend for open the websocket connect in the frontend, you must add multiple SQS Queues. You need for each process one SQS.

The alternative is to use kafka with multi groups, but it is a huge effort and much more expensive as SQS.

### Example to forward IoT Core Messages to SQS

* create thing in awt Iot Core
* create mappings to this thing

#### Mapping for live data

```bash
## Rule
SELECT * FROM 'mainchannel' 
## to SQS
Queue name datamapping
Use Base64 to encode the message data
IAM Role service-role/datamappingSQS 
```

Role

```json
{
    "Version": "2012-10-17",
    "Statement": {
        "Effect": "Allow",
        "Action": "sqs:SendMessage",
        "Resource": "arn:aws:sqs:eu-central-1:654384432543:datamapping"
    }
}
```

#### Mapping for store data 

```bash
## Rule
SELECT * FROM 'storechannel' 
## ro SQS
Queue name storechannel
Use Base64 to encode the message data
IAM Role service-role/storechannelSQS 
```

Role

```json
{
    "Version": "2012-10-17",
    "Statement": {
        "Effect": "Allow",
        "Action": "sqs:SendMessage",
        "Resource": "arn:aws:sqs:eu-central-1:654384432543:storechannel"
    }
}
```

### SQS

Queues

#### datamapping

```bash
Visibility timeout Info
Should be between 0 seconds and 12 hours.
Message retention period Info
Should be between 1 minute and 14 days.
Delivery delay Info
Should be between 0 seconds and 15 minutes.
Maximum message size Info
KB
Should be between 1 KB and 256 KB.
Receive message wait time Info
Seconds
```

Access

```json
{
  "Version": "2008-10-17",
  "Id": "__default_policy_ID",
  "Statement": [
    {
      "Sid": "__owner_statement",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::654384432543:root"
      },
      "Action": "SQS:*",
      "Resource": "arn:aws:sqs:eu-central-1:654384432543:datamapping"
    }
  ]
}
```

#### store

```bash
Visibility timeout Info
Should be between 0 seconds and 12 hours.
Message retention period Info
Should be between 1 minute and 14 days.
Delivery delay Info
Should be between 0 seconds and 15 minutes.
Maximum message size Info
KB
Should be between 1 KB and 256 KB.
Receive message wait time Info
Seconds
```

```json
{
  "Version": "2008-10-17",
  "Id": "__default_policy_ID",
  "Statement": [
    {
      "Sid": "__owner_statement",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::654384432543:root"
      },
      "Action": "SQS:*",
      "Resource": "arn:aws:sqs:eu-central-1:654384432543:storechannel"
    }
  ]
}
```
## create chart

cd swimcloud/charts
helm create datahub

## namespace

kubectl create -f admin/namespace-local.json

## ingress

minikube addons enable ingress

## deploy chart

helm upgrade --install swimcloud . \
  --namespace=local \
  --set displaysocket.image.tag="0.1.0" \
  --set frontend.image.tag="0.1.0" \
  --debug \
  --dry-run

helm upgrade --install swimcloud . --namespace=local --set displaysocket.image.tag="0.1.1" --set frontend.image.tag="0.1.0" --debug 

helm upgrade --install swimcloud . --namespace=local --set global.DEST_MQTT_MODE="MQTT" --debug --dry-run
helm upgrade --install swimcloud . --namespace=local --debug --dry-run

## check

helm list -A
helm uninstall swimcloud -n local

### get pods

kubectl get pods -n=local

### get logs

kubectl logs swimcloud-displaysocket-c77ff9cd8-f8lbh -n=local

### get web

kubectl get ingress -n=local
minikube ip
--> add to hots