# Usage

This repo is a modification of [monasca-agent subchart of monasca-helm
repo](https://github.com/monasca/monasca-helm/tree/master/monasca-agent) to
work on Kubernetes version 1.18+ and stable/train release of Monasca agent.

Other customisation include installation of
[stackhpc-monasca-agent-plugins](https://github.com/stackhpc/stackhpc-monasca-agent-plugins)
into agent-collector container which facilitates scraping of Prometheus
endpoints since and apply additional filters and preprocessing that the built
in Prometheus plugin cannot deliver. The Dockerfile for this customisation live
under `docker/agent-collector` folder.

To install the chart using Helm v3:

    git clone https://github.com/stackhpc/monasca-agent-chart

    cat > override.yaml << EOF
    keystone:
      os_username: monasca-agent
      os_user_domain_name: Default
      os_password: abc123
      os_project_name: project
      os_project_domain_name: Default
      url: http://openstack-control-plane:5000/v3
    monasca_url: http://openstack-control-plane:8082/v2.0
    monasca_log_url: http://openstack-control-plane:5607
    rbac:
      enabled: true
    collector:
      image:
        repository: stackhpc/agent-collector
        tag: stable-train
    forwarder:
      image:
        tag: stable-train
    plugins:
      daemonset:
        prometheusv2.yaml.j2: |
          init_config:
              # Timeout on GET requests endpoints
              timeout: 10
          instances:
              - metric_endpoint: 'http://{{ AGENT_HOSTNAME }}:9100/metrics'
                counters_to_rates: True
    log_level: INFO
    prometheus:
      auto_detect_pod_endpoints: true
      auto_detect_service_endpoints: true
    cadvisor:
      enabled: false
    kubernetes:
      enabled: false
    EOF

    helm install monasca-agent monasca-agent-chart/ -f override.yaml
