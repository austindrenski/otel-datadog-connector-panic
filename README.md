# OTel `datadog/connector` `panic: invalid access to shared data`

## Minimal reproduction

```yaml
receivers:
  otlp:
    protocols:
      http:

processors:
  batch:

connectors:
  datadog:

exporters:
  debug:
    verbosity: detailed

service:
  pipelines:
    metrics:
      receivers: [ datadog ]
      exporters: [ debug ]

    traces:
      receivers: [ otlp ]
      processors: [ batch ]
      exporters: [ datadog ]

    traces/2:
      receivers: [ datadog ]
      exporters: [ debug ]
```

```console
$ docker compose up
otel-datadog-connector-panic-otel-1  | 2023-11-11T00:53:59.554Z info    service@v0.88.0/telemetry.go:84 Setting up own telemetry...
otel-datadog-connector-panic-otel-1  | 2023-11-11T00:53:59.554Z info    service@v0.88.0/telemetry.go:201        Serving Prometheus metrics      {"address": ":8888", "level": "Basic"}
otel-datadog-connector-panic-otel-1  | 2023-11-11T00:53:59.554Z info    exporter@v0.88.0/exporter.go:275        Development component. May change in the future.        {"kind": "exporter", "data_type": "metrics", "name": "debug"}
otel-datadog-connector-panic-otel-1  | 2023-11-11T00:53:59.554Z info    datadogconnector@v0.88.0/connector.go:45        Building datadog connector      {"kind": "exporter", "name": "datadog", "exporter_in_pipeline": "traces", "receiver_in_pipeline": "metrics"}
otel-datadog-connector-panic-otel-1  | 2023-11-11T00:53:59.556Z info    exporter@v0.88.0/exporter.go:275        Development component. May change in the future.        {"kind": "exporter", "data_type": "traces", "name": "debug"}
otel-datadog-connector-panic-otel-1  | 2023-11-11T00:53:59.556Z info    datadogconnector@v0.88.0/connector.go:45        Building datadog connector      {"kind": "exporter", "name": "datadog", "exporter_in_pipeline": "traces", "receiver_in_pipeline": "traces"}
otel-datadog-connector-panic-otel-1  | 2023-11-11T00:53:59.557Z info    service@v0.88.0/service.go:143  Starting otelcol-contrib...     {"Version": "0.88.0", "NumCPU": 16}
otel-datadog-connector-panic-otel-1  | 2023-11-11T00:53:59.557Z info    extensions/extensions.go:33     Starting extensions...
otel-datadog-connector-panic-otel-1  | 2023-11-11T00:53:59.557Z info    datadogconnector@v0.88.0/connector.go:67        Starting datadogconnector       {"kind": "exporter", "name": "datadog", "exporter_in_pipeline": "traces", "receiver_in_pipeline": "metrics"}
otel-datadog-connector-panic-otel-1  | 2023-11-11T00:53:59.557Z info    datadogconnector@v0.88.0/connector.go:67        Starting datadogconnector       {"kind": "exporter", "name": "datadog", "exporter_in_pipeline": "traces", "receiver_in_pipeline": "traces"}
otel-datadog-connector-panic-otel-1  | 2023-11-11T00:53:59.557Z warn    internal@v0.88.0/warning.go:40  Using the 0.0.0.0 address exposes this server to every network interface, which may facilitate Denial of Service attacks        {"kind": "receiver", "name": "otlp", "data_type": "traces", "documentation": "https://github.com/open-telemetry/opentelemetry-collector/blob/main/docs/security-best-practices.md#safeguards-against-denial-of-service-attacks"}
otel-datadog-connector-panic-otel-1  | 2023-11-11T00:53:59.557Z info    otlpreceiver@v0.88.0/otlp.go:101        Starting HTTP server    {"kind": "receiver", "name": "otlp", "data_type": "traces", "endpoint": "0.0.0.0:4318"}
otel-datadog-connector-panic-otel-1  | 2023-11-11T00:53:59.557Z info    service@v0.88.0/service.go:169  Everything is ready. Begin running and processing data.
otel-datadog-connector-panic-otel-1  | panic: invalid access to shared data
otel-datadog-connector-panic-otel-1  |
otel-datadog-connector-panic-otel-1  | goroutine 398 [running]:
otel-datadog-connector-panic-otel-1  | go.opentelemetry.io/collector/pdata/internal.(*State).AssertMutable(...)
otel-datadog-connector-panic-otel-1  |  go.opentelemetry.io/collector/pdata@v1.0.0-rcv0017/internal/state.go:20
otel-datadog-connector-panic-otel-1  | go.opentelemetry.io/collector/pdata/pcommon.Map.PutBool({0xc0027e89d8?, 0xc002b408dc?}, {0x7cd4186?, 0xc0027e89c0?}, 0xdc?)
otel-datadog-connector-panic-otel-1  |  go.opentelemetry.io/collector/pdata@v1.0.0-rcv0017/pcommon/map.go:157 +0x225
otel-datadog-connector-panic-otel-1  | github.com/open-telemetry/opentelemetry-collector-contrib/internal/datadog.(*TraceAgent).Ingest(0xc002b57f80, {0x8b5c9d8, 0xc002ccc990}, {0xc002b54b58?, 0xc002b408dc?})
otel-datadog-connector-panic-otel-1  |  github.com/open-telemetry/opentelemetry-collector-contrib/internal/datadog@v0.88.0/agent.go:145 +0xba
otel-datadog-connector-panic-otel-1  | github.com/open-telemetry/opentelemetry-collector-contrib/connector/datadogconnector.(*connectorImp).ConsumeTraces(0xc002b5a7d0, {0x8b5c9d8, 0xc002ccc990}, {0xc002b54b58?, 0xc002b408dc?})
otel-datadog-connector-panic-otel-1  |  github.com/open-telemetry/opentelemetry-collector-contrib/connector/datadogconnector@v0.88.0/connector.go:91 +0x42
otel-datadog-connector-panic-otel-1  | go.opentelemetry.io/collector/internal/fanoutconsumer.(*tracesConsumer).ConsumeTraces(0x2?, {0x8b5c9d8, 0xc002ccc990}, {0xc002b54b58?, 0xc002b408dc?})
otel-datadog-connector-panic-otel-1  |  go.opentelemetry.io/collector@v0.88.0/internal/fanoutconsumer/traces.go:73 +0xa3
otel-datadog-connector-panic-otel-1  | go.opentelemetry.io/collector/processor/batchprocessor.(*batchTraces).export(0xc002b59080, {0x8b5c9d8, 0xc002ccc990}, 0x20?, 0x0)
otel-datadog-connector-panic-otel-1  |  go.opentelemetry.io/collector/processor/batchprocessor@v0.88.0/batch_processor.go:407 +0x175
otel-datadog-connector-panic-otel-1  | go.opentelemetry.io/collector/processor/batchprocessor.(*shard).sendItems(0xc002b590c0, 0xc002ea5f3c?)
otel-datadog-connector-panic-otel-1  |  go.opentelemetry.io/collector/processor/batchprocessor@v0.88.0/batch_processor.go:256 +0x53
otel-datadog-connector-panic-otel-1  | go.opentelemetry.io/collector/processor/batchprocessor.(*shard).start(0xc002b590c0)
otel-datadog-connector-panic-otel-1  |  go.opentelemetry.io/collector/processor/batchprocessor@v0.88.0/batch_processor.go:218 +0x1b4
otel-datadog-connector-panic-otel-1  | created by go.opentelemetry.io/collector/processor/batchprocessor.(*batchProcessor).newShard in goroutine 1
otel-datadog-connector-panic-otel-1  |  go.opentelemetry.io/collector/processor/batchprocessor@v0.88.0/batch_processor.go:160 +0x1ab
otel-datadog-connector-panic-otel-1 exited with code 2
```
