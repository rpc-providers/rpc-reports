# RPC-providers reports

## Reports
Monitoring information can be found [here](https://github.com/rpc-providers/rpc-monitor) and the monitor can be reached [here](https://monitor.rpc-providers.net/d/bdkaq43z8xybka/rpc-providers?orgId=1).

Reports are made monthly by extracting data from the prometheus data source and displaying them in monthly reports using [this script](https://github.com/rpc-providers/rpc-monitor/blob/master/report.sh). 

### Uptime
The monitoring provides a few possible metrics for calculating uptime:
* blockzero error: the monitor couldn't retrieve block 0 from the endpoint.
* connect error: the monitor couldn't connect to the wss endpoint.
* version error: the monitor couldn't retrieve the version information from the endpoint.
 
Uptime is calculated by blockzero errors. If the monitor can connect but cannot retrieve block information it should be considered an error. 

Since the monitoring checks every 15 minutes an error gets counted with a duration of 15 minutes.  

The error ratio can be found by 1 - (sum of errors) / (expected number of scrapes) which with a scrape of every 15 minutes results in 1 - (number of errors over 30 days) / (30 days * 24 hours * 4 scrapes per hour). To convert the error ratio to a percentage we have to multiply by 100.  

Or in promql syntax:

```
(1 - (sum_over_time(rpc_error{wss="$endpoint",zone="$zone",network="$network",error="blockzero"}[30d])) / (30 * 24 * 4)) * 100
```

### RPC calls
The number of rpc calls is measured by the sum of increases of the counter "substrate_rpc_calls_started" of a selected chain of a selected job (which is the federated prometheus job of one provider) over the last 30 days (this is not always exactly in sync with 1 month but "good enough" for the purpose of reporting and also helps when comparing months). The reporting is done in millions.

Or in promql syntax:

```
sum(increase(substrate_rpc_calls_started{chain="$chain",job="$job"}[30d]))
```
