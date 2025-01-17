# Prometheus Exporter for [updown.io](https://updown.io)

[![build-containers](https://github.com/DazWilkin/updown-exporter/actions/workflows/build.yml/badge.svg)](https://github.com/DazWilkin/updown-exporter/actions/workflows/build.yml)
[![Go Reference](https://pkg.go.dev/badge/github.com/DazWilkin/updown-exporter.svg)](https://pkg.go.dev/github.com/DazWilkin/updown-exporter)
[![Go Report Card](https://goreportcard.com/badge/github.com/dazwilkin/updown-exporter)](https://goreportcard.com/report/github.com/dazwilkin/updown-exporter)

## Metrics

Metrics names are prefixed `updown_`.

|Name|Type|Description|
|----|----|-----------|
|`checks_enabled`|Counter|Status of Check (enabled=1)|
|`exporter_build_info`|Counter|Exporter build info|
|`metrics_response_times`|Histogram|Histogram of a Check's response times|

## Image

`ghcr.io/dazwilkin/updown-exporter:760beaedd9b9a06519be38049ad82f948da536d2`

## API Key

The Exporter needs access to an updown API Key

```bash
export API_KEY="[YOUR-API-KEY]"
```

## Go

```bash
export API_KEY="[YOUR-API-KEY]"

go run . \
--endpoint=0.0.0.0:8080 \
--path=/metrics
```

## Container

```bash
API_KEY="[YOUR-API-KEY]"

IMAGE="ghcr.io/dazwilkin/updown-exporter:760beaedd9b9a06519be38049ad82f948da536d2"

podman run \
--interactive --tty --rm \
--publish=8080:8080 \
--env=API_KEY=${API_KEY} \
${IMAGE} \
  --endpoint=0.0.0.0:8080 \
  --path=/metrics
```

Then browse `http://localhost:8080/metrics` to view the metrics.

## Prometheus

`prometheus.yml`:
```YAML
global:
  scrape_interval: 1m
  evaluation_interval: 1m

scrape_configs:
  # updown Exporter
- job_name: "updown-exporter"
  static_configs:
  - targets:
    - "localhost:8080"
```

## Docker

```bash
API_KEY="[YOUR-API-KEY]"

IMAGE="ghcr.io/dazwilkin/updown-exporter:760beaedd9b9a06519be38049ad82f948da536d2"

docker run \
--detach --tty --rm \
--name="updown-exporter" \
--env=API_KEY=${API_KEY} \
--publish=8080:8080/tcp \
${IMAGE} \
  --endpoint=0.0.0.0:8080 \
  --path=/metrics

docker run \
--detach --rm --tty \
--name="prometheus" \
--publish=9090:9090/tcp \
--volume=${PWD}/prometheus.yml:/etc/prometheus/prometheus.yml:ro \
docker.io/prom/prometheus:v2.36.2 \
--config.file=/etc/prometheus/prometheus.yml \
--web.enable-lifecycle
```

Then browse:

+ [Exporter](http://localhost:8080/metrics)
+ [Prometheus](http://localhost:9090/targets)

## Podman

```bash
API_KEY="[YOUR-API-KEY]"

IMAGE="ghcr.io/dazwilkin/updown-exporter:760beaedd9b9a06519be38049ad82f948da536d2"

POD="updown-exporter"

podman pod create \
--name=${POD} \
--publish=5555:8080/tcp \
--publish=9090:9090/tcp

podman run \
--interactive --tty --rm \
--pod=${POD} \
--name="updown-exporter" \
--env=API_KEY=${API_KEY} \
${IMAGE} \
  --endpoint=0.0.0.0:8080 \
  --path=/metrics

podman run \
--detach --rm --tty \
--pod=${POD} \
--name="prometheus" \
--volume=${PWD}/prometheus.yml:/etc/prometheus/prometheus.yml:ro \
docker.io/prom/prometheus:v2.36.2 \
--config.file=/etc/prometheus/prometheus.yml \
--web.enable-lifecycle
```

Then browse:

+ [Exporter](http://localhost:8080/metrics)
+ [Prometheus](http://localhost:9090/targets)

<hr/>
<br/>
<a href="https://www.buymeacoffee.com/dazwilkin" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/default-orange.png" alt="Buy Me A Coffee" height="41" width="174"></a>