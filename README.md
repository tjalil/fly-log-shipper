# fly-log-shipper

Ship logs from fly to other providers using [NATS](https://docs.nats.io/) and [Vector](https://vector.dev/)

In this repo you will find various [Vector Sinks](https://vector.dev/docs/reference/configuration/sinks/) along with the required fly config. The end result is a Fly.IO application that automatically reads your organisation logs and sends them to external providers.

# Quick start

1. Create a new fly logger app based on our docker image

```
fly launch --image flyio/log-shipper:latest --no-public-ips
```

2. Set [NATS source secrets](#nats-source-configuration) for your new app
3. Set your desired [provider](#provider-configuration) from below

**Thats it** - no need to setup NATs clients within your apps, as fly apps are already sending monitoring information back to fly which we can read.

However for advanced uses you can still configure a NATs client in your apps to talk to this NATs server. See [NATS](#nats)

## NATS source configuration

| Secret         | Description                                                                                                      |
| -------------- | ---------------------------------------------------------------------------------------------------------------- |
| `ORG`          | Organisation slug (default to `personal`)                                                                        |
| `ACCESS_TOKEN` | Fly personal access token (required; set with `fly secrets set ACCESS_TOKEN=$(fly auth token)`)                  |
| `SUBJECT`      | Subject to subscribe to. See [[NATS]] below (defaults to `logs.>`)                                               |
| `QUEUE`        | Arbitrary queue name if you want to run multiple log processes for HA and avoid duplicate messages being shipped |
| `NETWORK`      | 6PN network, if you want to run log-shipper through a  WireGuard connection (defaults to `fdaa:0:0`)             |

After generating your `fly.toml`, remember to update the internal port to match the `vector` internal port
defined in `vector-configs/vector.toml`. Not doing so will result in health checks failing on deployment.

```
[[services]]
  http_checks = []
  internal_port = 8686
```

---

Set the secrets below associated with your desired log destination

## Provider configuration

### AppSignal

| Secret                   | Description            |
| ------------------------ | ---------------------- |
| `APPSIGNAL_PUSH_API_KEY` | AppSignal push API key |

### AWS S3

| Secret                  | Description                                                                             |
| ----------------------- | --------------------------------------------------------------------------------------- |
| `AWS_ACCESS_KEY_ID`     | AWS Access key with access to the log bucket                                            |
| `AWS_SECRET_ACCESS_KEY` | AWS secret access key                                                                   |
| `AWS_BUCKET`            | AWS S3 bucket to store logs in                                                          |
| `AWS_REGION`            | Region for the bucket                                                                   |
| `S3_ENDPOINT`           | (optional) Endpoint URL for S3 compatible object stores such as Cloudflare R2 or Wasabi |

### Axiom

| Secret          | Description   |
| --------------- | ------------- |
| `AXIOM_TOKEN`   | Axiom token   |
| `AXIOM_DATASET` | Axiom dataset |

### Baselime

| Secret              | Description                                   |
|---------------------|-----------------------------------------------|
| `BASELIME_API_KEY`  | Baselime API key                              |
| `BASELIME_DATASET`  | (optional) Baselime dataset (default "flyio") |

### Better Stack Logs (formerly Logtail)

| Secret                      | Description                    |
|-----------------------------|--------------------------------|
| `BETTER_STACK_SOURCE_TOKEN` | Better Stack Logs source token |

### Datadog

| Secret            | Description                                   |
| ----------------- | --------------------------------------------- |
| `DATADOG_API_KEY` | API key for your Datadog account              |
| `DATADOG_SITE`    | (optional) The Datadog site. ie: datadoghq.eu |

### Highlight

| Secret                 | Description          |
| ---------------------- | -------------------- |
| `HIGHLIGHT_PROJECT_ID` | Highlight Project ID |

### Honeybadger

| Secret                | Description         |
| --------------------- | ------------------- |
| `HONEYBADGER_API_KEY` | Honeybadger API key |

### Honeycomb

| Secret              | Description       |
| ------------------- | ----------------- |
| `HONEYCOMB_API_KEY` | Honeycomb API key |
| `HONEYCOMB_DATASET` | Honeycomb dataset |

### Humio

| Secret           | Description                             |
| ---------------- | --------------------------------------- |
| `HUMIO_TOKEN`    | Humio token                             |
| `HUMIO_ENDPOINT` | (optional) Endpoint URL to send logs to |

### HyperDX

| Secret            | Description     |
| ----------------- | --------------- |
| `HYPERDX_API_KEY` | HyperDX API key |

### Logdna

| Secret           | Description    |
| ---------------- | -------------- |
| `LOGDNA_API_KEY` | LogDNA API key |

### Logflare

| Secret                  | Description                                             |
| ----------------------- | ------------------------------------------------------- |
| `LOGFLARE_API_KEY`      | Logflare ingest API key                                 |
| `LOGFLARE_SOURCE_TOKEN` | Logflare source token (uuid on your Logflare dashboard) |

### Loki

| Secret          | Description   |
| --------------- | ------------- |
| `LOKI_URL`      | Loki Endpoint |
| `LOKI_USERNAME` | Loki Username |
| `LOKI_PASSWORD` | Loki Password |

### New Relic

One of these is required for New Relic logs. New Relic recommend the license key be used (ref: https://docs.newrelic.com/docs/logs/enable-log-management-new-relic/enable-log-monitoring-new-relic/vector-output-sink-log-forwarding/)

| Secret                  | Description                      |
| ----------------------- | -------------------------------- |
| `NEW_RELIC_INSERT_KEY`  | (optional) New Relic Insert key  |
| `NEW_RELIC_LICENSE_KEY` | (optional) New Relic License key |
| `NEW_RELIC_REGION`      | (optional) eu or us (default us) |
| `NEW_RELIC_ACCOUNT_ID`  | New Relic Account Id             |

### OpsVerse

| Secret                  | Description            |
| ----------------------- | ---------------------- |
| `OPSVERSE_LOGS_ENDPOINT`| OpsVerse Logs Endpoint |
| `OPSVERSE_USERNAME`     | OpsVerse Username      |
| `OPSVERSE_PASSWORD`     | OpsVerse Password      |

### Papertrail

| Secret                | Description         |
| --------------------- | ------------------- |
| `PAPERTRAIL_ENDPOINT` | Papertrail endpoint |
| `PAPERTRAIL_ENCODING_CODEC` | Papertrail codec (default is "json") |

### Sematext

| Secret            | Description     |
| ----------------- | --------------- |
| `SEMATEXT_REGION` | Sematext region |
| `SEMATEXT_TOKEN`  | Sematext token  |


### Signoz

| Secret                | Description                                                     |
| --------------------- | --------------------------------------------------------------- |
| `SIGNOZ_INGESTION_KEY`| Signoz Access Token                                             |
| `SIGNOZ_URI`       | Signoz URI (default is 'https://ingest.us.signoz.cloud/logs/vector') |

### Uptrace

| Secret                  | Description        |
| -----------------       | ------------------ |
| `UPTRACE_API_KEY`       | Uptrace API key    |
| `UPTRACE_PROJECT`       | Uptrace project ID |
| `UPTRACE_SINK_INPUT`    | `"log_json"`, etc. |
| `UPTRACE_SINK_ENCODING` | `"json"`, etc.     |

For UPTRACE_SINK_ENCODING Vector expects one of `avro`, `gelf`, `json`, `logfmt`, `native`,
`native_json`, `raw_message`, `text` for key `sinks.uptrace`.

### EraSearch

| Secret            | Description                     |
| ----------------- | ------------------------------- |
| `ERASEARCH_URL`   | EraSearch Endpoint              |
| `ERASEARCH_AUTH`  | EraSearch User                  |
| `ERASEARCH_INDEX` | EraSearch Index you want to use |

### HTTP

| Secret       | Description            |
| ------------ | ---------------------- |
| `HTTP_URL`   | HTTP/HTTPS Endpoint    |
| `HTTP_TOKEN` | HTTP Bearer auth token |

### Slack ( experimental )

HTTP sink that can be used for sending log alerts to Slack.

| Secret                 | Description            |
| ---------------------- | ---------------------- |
| `SLACK_WEBHOOK_URL`    | Slack WebHook URL      |
| `SLACK_ALERT_KEYWORDS` | Keywords to alert on   |

Example for setting keywords `fly secrets set SLACK_ALERT_KEYWORDS="[r'SIGTERM', r'reboot']"`

---

# NATS

The log stream is provided through the [NATS protocol](https://docs.nats.io/nats-protocol/nats-protocol) and is limited to subscriptions to logs in your organisations.

## Connecting

> Note: You do **not** have to manually connect a NAT Client, see [Quick Start](#quick-start)

If you want to add custom behaviours or modify the subject sent from your app, then you can connect your app to the NATs server manually.

Any fly app can connect to the NATs server on `nats://[fdaa::3]:4223` (IPV6).

**Note: you will need to supply a user / password.**

> **User**: is your Fly organisation slug, which you can obtain from `fly orgs list` > **Password**: is your fly token, which you can obtain from `fly auth token`

### Example using the NATs client

Launch a nats client based on the nats-server image

```
fly launch --image="synadia/nats-server:nightly" --name="nats-client"
```

SSH into the new app

```
fly -a nats-client ssh console
```

```
nats context add nats --server [fdaa::3]:4223 --description "NATS Demo" --select \
  --user <YOUR FLY ORG SLUG> \
  --password <YOUR PAT>
```

```
nats pub "logs.test" "hello world"
```

## Subject

The subject schema is `logs.<app_name>.<region>.<instance_id>` and the standard
[NATS wildcards](https://docs.nats.io/nats-concepts/subjects#wildcards) can be used.
In this app, the `SUBJECT` secret can be used to set the subject and limit the scope of the logs streamed.

## Queue

If you would like to run multiple vm's for high availability, the NATS endpoint supports
[subscription queues](https://docs.nats.io/nats-concepts/queue) to ensure messages are only sent to one
subscriber of the named queue. The `QUEUE` secret can be set to configure a queue name for the client.

---

# Vector

The `nats` source component sends logs to other downstream transforms and sinks in the Vector config.
This processes the log lines and sends them to various providers.
The config is generated from a shell wrapper script which uses conditionals on environment variables to
decide which Vector sinks to configure in the final config.
