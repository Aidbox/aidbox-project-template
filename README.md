# Aidbox project template

This repository contains a **template** for the **Aidbox** project with:

- Topic-based Subscription enabled integrating with **[Aidbox Workflow Engine](https://docs.aidbox.app/modules-1/workflow-engine)**.
- A preconfigured example of **SubscriptionTopic** that allows **Task** creation for specific trigger events.

Check the `SubscriptionTopic to Aidbox Task (DEMO)` community notebook with the corresponding scenario for the project.

# How to init this project

In order to run Aidbox locally, you need to have [Docker & Docker compose installed](https://docs.docker.com/engine/install/). To begin with new aidbox project, run the following command.

```sh
git clone --depth=1 --branch=topic-based-subscription-gcp-pubsub https://github.com/Aidbox/aidbox-project-template.git aidbox-project && cd aidbox-project && rm -rf .git
```

Get your Aidbox license on [aidbox.app](https://aidbox.app/). And then set `AIDBOX_LICENSE` env variable in [.env file](.env#L1).

Once you set the license key, you may run Aidbox service with Docker compose by running:

``` sh
docker compose up
```

Command _docker compose up_ will start Aidbox instance configured with _SubscriptionTopic_ for _Observation_ resource with some filters and criteria preconfigured.
By default *Aidbox* is available at _http://localhost:8888_, default *User* is: _username_  `admin`, _password_  `password`.


## Available services
- [Aidbox](http://localhost:8888)  `admin:password`
- [Grafana](http://localhost:3000/d/aidbox-topic-subs-metrics-dashboard/topic-based-subscriptions?orgId=1&refresh=10s) `admin:password`
- [Prometheus](http://localhost:9090/targets)

# What's next
Check documentation for [Aidbox SubscriptionTopic module](https://docs.aidbox.app/modules-1/topic-based-subscriptions)

You may learn more on Aidbox project in Aidbox documentation. For production read another doc
