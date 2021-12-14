*Find Number of Pods per Namespace*: sum by (namespace) (kube_pod_info)
*Find CPU Overcommit*: sum(kube_pod_container_resource_limits{resource="cpu"}) - sum(kube_node_status_capacity{resource="cpu"})
*Memory Overcommit*: sum(kube_pod_container_resource_limits{resource="memory"}) - sum(kube_node_status_capacity{resource="memory"})
*Find Unhealthy Kubernetes Pods*: min_over_time(sum by (namespace, pod) (kube_pod_status_phase{phase=~"Pending|Unknown|Failed"})[15m:1m]) > 0
*Find Kubernetes Pods CrashLooping*: increase(kube_pod_container_status_restarts_total[15m]) > 3
*Find Number of Containers Without CPU Limits In Each Namespace*: count by (namespace)(sum by (namespace,pod,container)(kube_pod_container_info{container!=""}) unless sum by (namespace,pod,container)(kube_pod_container_resource_limits{resource="cpu"}))
*Find PersistentVolumeClaim in Pending State: kube_persistentvolumeclaim_status_phase{phase="Pending"}
*Find Unstable Nodes*: sum(changes(kube_node_status_condition{status="true",condition="Ready"}[15m])) by (node) > 2
*Find Idle CPU cores*: sum((rate(container_cpu_usage_seconds_total{container!="POD",container!=""}[30m]) - on (namespace,pod,container) group_left avg by (namespace,pod,container)(kube_pod_container_resource_requests{resource="cpu"})) * -1 >0)
*Find Idle Memory*: sum((container_memory_usage_bytes{container!="POD",container!=""} - on (namespace,pod,container) avg by (namespace,pod,container)(kube_pod_container_resource_requests{resource="memory"})) * -1 >0 ) / (1024*1024*1024)

*Find Node Status:*
sum(kube_node_status_condition{condition="Ready",status="true"})
sum(kube_node_status_condition{condition="NotReady",status="true"})
sum(kube_node_spec_unschedulable) by (node)




Prometheus pros and cons: https://www.containiq.com/post/kubernetes-monitoring-with-prometheus

Reference: 
https://www.containiq.com/post/promql-cheat-sheet-with-examples
https://medium.com/@amimahloof/kubernetes-promql-prometheus-cpu-aggregation-walkthrough-2c6fd2f941eb
https://coralogix.com/blog/promql-tutorial-5-tricks-to-become-a-prometheus-god/
https://sysdig.com/blog/getting-started-with-promql-cheatsheet/
