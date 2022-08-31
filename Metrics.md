# Metrics 

## Frequency

- Current frequency
`node_cpu_scaling_frequency_hertz` : Current scaled CPU thread frequency in hertz

- Min/Max frequency
`node_cpu_scaling_frequency_min_hertz` : Minimum scaled CPU thread frequency in hertz
`node_cpu_scaling_frequency_max_hertz` : Maximum scaled CPU thread frequency in hertz

- Min/Max frequency
`node_cpu_frequency_min_hertz` : Minimum cpu thread frequency in hertz
`node_cpu_frequency_max_hertz` : Maximum cpu thread frequency in hertz

## Power usage

`node_hwmon_power_average_watt` : Hardware monitor for power usage in watts (average)

## CPU Modes

`node_cpu_seconds_total` : Seconds the cpus spent in each mode

## Temperatures

`node_hwmon_temp_celsius`      : Hardware monitor for temperature (input)
`node_hwmon_temp_crit_celsius` : Hardware monitor for temperature (crit)
`node_hwmon_temp_max_celsius`  : Hardware monitor for temperature (max)

`node_hwmon_sensor_label` : Label for given chip and sensor
`node_hwmon_temp_celsius` : Hardware monitor for temperature (input)

## Interrupts

`node_intr_total` : Total number of interrupts serviced

## Node load

`node_load1`  :  1m load average
`node_load5`  :  5m load average
`node_load15` : 15m load average

## Memory

- Total memory in the node
`node_memory_MemTotal_bytes`     : Memory information field MemTotal_bytes
- Available memory in the node
`node_memory_MemAvailable_bytes` : Memory information field MemAvailable_bytes
`node_memory_MemFree_bytes`      : Memory information field MemFree_bytes (cache memory considerd used)

## Node Info

`node_uname_info` : Labeled system information as provided by the uname system call

## Time

- uptime
`node_boot_time_seconds` : Node boot time, in unixtime
- node time
`node_time_seconds` : System time in seconds since epoch (1970)

## Pod level metrics
- 
`kube_pod_container_resource_limits`
- Node status and capacity as seen by K8s
`kube_node_status_capacity`
- Pod info
`kube_pod_info`

## Some Formulas

- CPU resources requested per node
```
sum by (node) ( kube_pod_container_resource_requests{resource="cpu"} )
```
- MEM resoruces requested per node
```
sum by (node) ( kube_pod_container_resource_requests{resource="memory"} )
```
- CPU resources limits per node
```
sum by (node) ( kube_pod_container_resource_limits{resource="cpu"} )
```
- MEM resources limits per node
```
sum by (node) ( kube_pod_container_resource_limits{resource="memory"} )
```
- Find IDLE CPU cores per node
```
sum by (node) (
        (
            rate(container_cpu_usage_seconds_total{container!="POD",container!=""}[30m]) 
            - on (namespace,pod,container) 
            group_left avg by (namespace,pod,container)
                (kube_pod_container_resource_requests{resource="cpu"})
        ) * -1 >0
    )
```
- Percentage of free memory per node
(node_memory_MemFree_bytes / node_memory_MemTotal_bytes) * 100

- Percentage of available memory per node
(node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) * 100

- Top two nodes with the highest free mem percentage
topk(2, (node_memory_MemFree_bytes / node_memory_MemTotal_bytes) * 100)


## References
- [Querying examples](https://prometheus.io/docs/prometheus/latest/querying/examples/)
- [Promql cheatsheet](https://sysdig.com/blog/getting-started-with-promql-cheatsheet/)
- Prometheus query examples for [monitoring Kubernetes](https://sysdig.com/blog/prometheus-query-examples/)
- [Kubernetes capacity planning](https://sysdig.com/blog/kubernetes-capacity-planning/): How to rightsize the requests of your cluster