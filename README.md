# benchmark tests for OpenSearch compression standards

- https://github.com/opensearch-project/opensearch-benchmark
- https://github.com/opensearch-project/opensearch-benchmark-workloads

## run
opensearch dashboard + nodes can be started as docker containers with '2nodes+dashboard_cluster.yml'

## command to run a workload with parameters

```shell
opensearch-benchmark execute-test --pipeline=benchmark-only --workload=big5 --target-host=https://localhost:9200 --client-options=basic_auth_user:admin,basic_auth_password:"<PASSWORD>",verify_certs:false --distribution-version=2.12.0 --workload-params="index_settings_zstd.json"
```
`index_settings_zstd.json` could look like this:
```json
{
    "index_name" : "big5_zstd_lvl6",
    "number_of_replicas" : 0,
    "index_settings" : {
        "codec" : "zstd",
        "codec.compression_level": "6"
    }
}
```
NOTE: only supported parameters are valid, which are supported can be obtained from the workload README.md



## command to retrieve the storage size of all `big5` indexes

```shell
curl "https://localhost:9200/big5_*/_stats/store?pretty" -ku admin:"<PASSWORD>" | code -
```

## compare results

lists last 100 results of test runs:
```shell
opensearch-benchmark list test_executions --limit 100
```

besides compare command-line tool from opensearch-benchmark itself there is a python scripts 'latency_extractor.ipynb' that can extract 50th and 90th percentile latencies from a test-execution. Just replace 'EXECUTION_ID' with anyone from the above shell command.

