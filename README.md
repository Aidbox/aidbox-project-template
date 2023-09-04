# Aidbox project template

This repository contains a **template** for the **Aidbox** project with:

- **v4.3.0 R4B FHIR version**
- A preconfigured example of **SubscriptionTopic** for the **observation resource**.
- **subscription-demo-server** which allow to recieve notification from Aidbox, and review them.


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

Command ~docker compose up~ will start Aidbox instance configured with ~SubscriptionTopic~ for ~Observation~ resource with some filters and criteria preconfigured.
By default *Aidbox* is avialable at ~http://localhost:8888~, default *User* is: _username_  ~admin~, _password_  ~password~.

The demo subscription server, which accepts messages from Aidbox, is now running. The UI to monitor these delivered messages is available at ~http://localhost:9001~.

While creating Subscriptions in Aidobx, use ~http://subscription-demo-server:9000/<something>~ as endpoint value.


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
  "id": "test-sub-1",
  "end": "2024-12-31T12:00:00.000-00:00"
}' \
http://localhost:8888/fhir/Subscription
```

## Verify that the handshake notification has been delivered.

## Create a resource, that should trigger the notification:
The next command will trigger the notification. However, since ~max-count~ in the subscription is specified as ~3~, this command should be repeated three times to deliver the notification immediately.
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

## Verify that event-notification has been delivered.

## Check heartbeat notification.
If no new resources fitting the subscription are created during ~heartbeat-period~ (30 sec), the heartbeat notification will be delivered:

# What's next
Check documentation for [Aidbox SubscriptionTopic module](https://docs.aidbox.app/modules-1/topic-based-subscriptions)

You may learn more on Aidbox project in Aidbox documentation. For production read another doc
