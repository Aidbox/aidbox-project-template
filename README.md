# Aidbox project template

This repository contains a **template** for the **Aidbox** project with:

- **v4.3.0 R4B FHIR version**
- A preconfigured example of **SubscriptionTopic** for the **observation resource**.
- **subscription-demo-server** which allow to receive notification from Aidbox, and review them.


# How to init this project

In order to run Aidbox locally, you need to have [Docker & Docker compose installed](https://docs.docker.com/engine/install/). To begin with new aidbox project, run the following command.

```sh
git clone --depth=1 --branch=main https://github.com/Aidbox/aidbox-project-template.git aidbox-project && cd aidbox-project && rm -rf .git
```

Get your Aidbox license on [aidbox.app](https://aidbox.app/). And then set `AIDBOX_LICENSE` env variable in [.env file](.env#L1).

Once you set the license key, you may run Aidbox service with Docker compose by running:

``` sh
docker compose up
```

Command _docker compose up_ will start Aidbox instance configured with _SubscriptionTopic_ for _Observation_ resource with some filters and criteria preconfigured.
By default *Aidbox* is available at _http://localhost:8888_, default *User* is: _username_  `admin`, _password_  `password`.

The demo subscription server, which accepts messages from Aidbox, is now running. The UI to monitor these delivered messages is available at _http://localhost:9001_

While creating Subscriptions in Aidobx, use `http://subscription-demo-server:9000/<something>` as endpoint value.

## Available services
- [Aidbox](http://localhost:8888)  `admin:password`
- [Subscriber Demo Server](http://localhost:9001)
- [Grafana](http://localhost:3000/d/aidbox-topic-subs-metrics-dashboard/topic-based-subscriptions?orgId=1&refresh=10s) `admin:password`
- [Prometheus]([http://localhost:9090](http://localhost:9090/targets)

# How to create Subscription and check notification.
## To subscribe to a preconfigured SubscriptionTopic, execute the following command:
``` sh
curl -X POST \
-u root:secret \
-H "Content-Type: application/json" \
-H "Accept: application/json" \
-d '{
  "meta": {
    "profile": ["http://hl7.org/fhir/uv/subscriptions-backport/StructureDefinition/backport-subscription"]
  },
  "criteria": "http://aidbox.app/SubscriptionTopic/observations",
  "channel": {
    "extension": [
      {
        "url": "http://hl7.org/fhir/uv/subscriptions-backport/StructureDefinition/backport-heartbeat-period",
        "valueUnsignedInt": 30
      },
      {
        "url": "http://hl7.org/fhir/uv/subscriptions-backport/StructureDefinition/backport-timeout",
        "valueUnsignedInt": 60
      },
      {
        "url": "http://hl7.org/fhir/uv/subscriptions-backport/StructureDefinition/backport-max-count",
        "valuePositiveInt": 3
      }
    ],
    "type": "rest-hook",
    "endpoint": "http://subscription-demo-server:9000/callback-test-1",
    "payload": "application/fhir+json",
    "_payload": {
      "extension": [
        {
          "url": "http://hl7.org/fhir/uv/subscriptions-backport/StructureDefinition/backport-payload-content",
          "valueCode": "id-only"
        }
      ]
    }
  },
  "_criteria": {
    "extension": [
      {
        "url": "http://hl7.org/fhir/uv/subscriptions-backport/StructureDefinition/backport-filter-criteria",
        "valueString": "Observation?value=42"
      }
    ]
  },
  "resourceType": "Subscription",
  "reason": "R4/B Test Topic-Based Subscription for Observation",
  "status": "requested",
  "end": "2024-12-31T12:00:00.000-00:00"
}' \
http://localhost:8888/fhir/Subscription
```

## Verify that the handshake notification has been delivered.
<img width="1297" alt="Screenshot 2023-09-04 at 16 14 23" src="https://github.com/Aidbox/aidbox-project-template/assets/1230663/97bccc28-aedb-4099-8c23-b4effeaaf911">

## Create a resource, that should trigger the notification:
The next command will trigger the notification. However, since _max-count_ in the subscription is specified as _3_, this command should be repeated three times to deliver the notification immediately.
``` sh
curl -X POST \
-u root:secret \
-H "Content-Type: application/json" \
-H "Accept: application/json" \
-d '{
  "valueQuantity": {
    "value": 42,
    "unit": "mmol/l"
  },
  "resourceType": "Observation",
  "status": "final",
  "code": {
    "coding": [
      {
        "system": "http://loinc.org",
        "code": "15074-8",
        "display": "Glucose [Moles/volume] in Blood"
      }
    ]
  }
}' \
http://localhost:8888/fhir/Observation
```

## How to Set Up a Dashboard in Grafana with Topic-Related Information

``` sh
curl -X POST \
-u root:secret \
-H "Content-Type: application/json" \
-H "Accept: application/json" \
-d '{"method" : "aidbox.metrics/update-aidbox-topics-dashboard"}' \
-v \
http://localhost:8888/rpc

```

By default Grafana are avilable at `http://localhost:3000/` with `admin:admin`.

## Verify that event-notification has been delivered.
<img width="1397" alt="Screenshot 2023-09-04 at 15 42 08" src="https://github.com/Aidbox/aidbox-project-template/assets/1230663/e399e8f9-3cf2-4ad9-a3e4-3f1085eeab92">

## Check heartbeat notification.
If no new resources fitting the subscription are created during _heartbeat-period_ (30 sec), the heartbeat notification will be delivered:
<img width="1427" alt="Screenshot 2023-09-04 at 15 44 29" src="https://github.com/Aidbox/aidbox-project-template/assets/1230663/b4768775-1d36-4122-94c5-1891e652d6d1">

# What's next
Check documentation for [Aidbox SubscriptionTopic module](https://docs.aidbox.app/modules-1/topic-based-subscriptions)

You may learn more on Aidbox project in Aidbox documentation. For production read another doc
