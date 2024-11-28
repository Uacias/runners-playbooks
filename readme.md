 - job_name: "kubernetes-pods"
        honor_labels: true

        kubernetes_sd_configs:
          - role: pod

        relabel_configs:
          - source_labels:
              [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
            action: keep
            regex: true
          - source_labels:
              [__meta_kubernetes_pod_annotation_prometheus_io_scrape_slow]
            action: drop
            regex: true
          - source_labels:
              [__meta_kubernetes_pod_annotation_prometheus_io_scheme]
            action: replace
            regex: (https?)
            target_label: __scheme__
          - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
            action: replace
            target_label: __metrics_path__
            regex: (.+)
          - source_labels:
              [
                __meta_kubernetes_pod_annotation_prometheus_io_port,
                __meta_kubernetes_pod_ip,
              ]
            action: replace
            regex: (\d+);(([A-Fa-f0-9]{1,4}::?){1,7}[A-Fa-f0-9]{1,4})
            replacement: "[$2]:$1"
            target_label: __address__
          - source_labels:
              [
                __meta_kubernetes_pod_annotation_prometheus_io_port,
                __meta_kubernetes_pod_ip,
              ]
            action: replace
            regex: (\d+);((([0-9]+?)(\.|$)){4})
            replacement: $2:$1
            target_label: __address__
          - action: labelmap
            regex: __meta_kubernetes_pod_annotation_prometheus_io_param_(.+)
            replacement: __param_$1
          - action: labelmap
            regex: __meta_kubernetes_pod_label_(.+)
          - source_labels: [__meta_kubernetes_namespace]
            action: replace
            target_label: namespace
          - source_labels: [__meta_kubernetes_pod_name]
            action: replace
            target_label: pod
          - source_labels: [__meta_kubernetes_pod_phase]
            regex: Pending|Succeeded|Failed|Completed
            action: drop
          - source_labels: [__meta_kubernetes_pod_node_name]
            action: replace
            target_label: node


- job_name: "kubernetes-pods"
  kubernetes_sd_configs:
    - role: pod
  relabel_configs:
    - source_labels: [__meta_kubernetes_namespace]
      action: keep
      regex: arc-runners




## Prometheus server data Persistent Volume existing claim name
    ## Requires server.persistentVolume.enabled: true
    ## If defined, PVC must be created manually before volume will be bound
    existingClaim: ""


## Prometheus data retention period (default if not specified is 15 days)
  ##
  retention: "15d"

  ## Prometheus' data retention size. Supported units: B, KB, MB, GB, TB, PB, EB.
  ##
  retentionSize: ""