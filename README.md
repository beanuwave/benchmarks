# benchmark tests for OpenSearch compression standards

- https://github.com/opensearch-project/opensearch-benchmark
- https://github.com/opensearch-project/opensearch-benchmark-workloads

## command to run a workload with parameters

```shell
opensearch-benchmark execute-test --pipeline=benchmark-only --workload=big5 --target-host=https://localhost:9200 --client-options=basic_auth_user:admin,basic_auth_password:"5N8#oLmKwxt!Gh7xQ33b",verify_certs:false --distribution-version=2.12.0 --workload-params="index_settings_zstd.json"
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
curl "https://localhost:9200/big5_*/_stats/store?pretty" -ku admin:"5N8#oLmKwxt!Gh7xQ33b" | code -
```
