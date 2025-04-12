# OPENTELEMETRY eBPF profiler / collector pipeline

**This is not the official OTEL eBPF tool**

This Directory references the GrafanaLabs Opentelemetry eBPF profiler setup and the workflow it requires to send data to pyroscope. 

## More information:
- [OpenTelemetry profiling support (Pyroscope Docs)](https://grafana.com/docs/pyroscope/latest/configure-client/opentelemetry/ebpf-profiler/)
- [OpenTelemetry eBPF profiler examples (Github)](https://github.com/grafana/pyroscope/tree/main/examples/grafana-alloy-auto-instrumentation/ebpf-otel)
- [GrafanaLabs OpenTelemetry eBPF profiler fork (Github)](https://github.com/grafana/opentelemetry-ebpf-profiler)


## Pipelines in this Directory

This directory has working examples / applications which follow three distinct pipelines:

1. OpenTelemetry eBPF Profiler -> OpenTelemetry Collector -> Pyroscope (Fixed versions) | ([docs supported...](https://grafana.com/docs/pyroscope/latest/configure-client/opentelemetry/ebpf-profiler/))
2. OpenTelemetry eBPF Profiler -> Grafana Alloy -> Pyroscope | (Untested...)

3. OpenTelemetry eBPF Profiler (Grafana fork) -> OpenTelemetry Collector / Grafana Alloy -> Pyroscope | (untested..)

## Directories and their pipelines

1. ./fixed
   - This directory refers to the first pipeline and is based on official Grafanalabs examples (**Working**)

2. ./alloy_pipeline
   - This refers to the second options and skips using the OTEL collector

3. ./forked_collector_pyro

