# benchmark tests for OpenSearch compression standards

- https://github.com/opensearch-project/opensearch-benchmark
- https://github.com/opensearch-project/opensearch-benchmark-workloads

## run
opensearch dashboard + nodes can be started as docker containers with '2nodes+dashboard_cluster.yml'

## command to run a workload with parameters

```shell
opensearch-benchmark execute-test --pipeline=benchmark-only --workload=big5 --target-host=https://localhost:9200 --client-options=basic_auth_user:<USERNAME>,basic_auth_password:<PASSWORD>,verify_certs:false --distribution-version=2.12.0 --workload-params="index_settings_zstd.json"
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
curl "https://localhost:9200/big5_*/_stats/store?pretty" -ku <USERNAME>:<PASSWORD> | code -
```

## compare results

lists last 100 results of test runs:
```shell
opensearch-benchmark list test_executions --limit 100
```

besides compare command-line tool from opensearch-benchmark itself there is a python scripts 'latency_extractor.ipynb' that can extract 50th and 90th percentile latencies from a test-execution. Just replace 'EXECUTION_ID' with anyone from the above shell command.

# create custom workload


## create schema
<details>
<summary>Example for http_logs schema</summary>

```shell
curl -XPUT https://localhost:9200/http_logs -H 'Content-Type: application/json' -ku <USERNAME>:<PASSWORD> -d '{
        "aliases": {},
    "mappings": {
      "dynamic_templates": [
        {
          "strings_as_keyword": {
            "match_mapping_type": "string",
            "mapping": {
              "doc_values": true,
              "ignore_above": 4096,
              "type": "keyword"
            }
          }
        },
        {
          "doc_values": {
            "match": "*",
            "mapping": {
              "doc_values": true,
              "type": "{dynamic_type}"
            }
          }
        }
      ],
      "properties": {
        "@timestamp": {
          "type": "date",
          "format": "date_optional_time"
        },
        "@type": {
          "type": "keyword"
        },
        "T_connect": {
          "type": "integer"
        },
        "T_handshake": {
          "type": "integer"
        },
        "T_idle": {
          "type": "integer"
        },
        "T_request": {
          "type": "integer"
        },
        "T_total": {
          "type": "integer"
        },
        "T_wait": {
          "type": "integer"
        },
        "abap_component": {
          "type": "keyword"
        },
        "abap_user": {
          "type": "keyword"
        },
        "accept_date": {
          "type": "keyword",
          "ignore_above": 4096
        },
        "accept_timestamp": {
          "type": "text"
        },
        "actconn": {
          "type": "long"
        },
        "api_version": {
          "type": "keyword"
        },
        "app_request_duration": {
          "type": "float"
        },
        "app_uuid": {
          "type": "keyword"
        },
        "auth_type": {
          "type": "keyword"
        },
        "backend_ip": {
          "type": "keyword"
        },
        "backend_name": {
          "type": "keyword",
          "ignore_above": 4096
        },
        "backend_port": {
          "type": "integer"
        },
        "backend_queue": {
          "type": "long"
        },
        "backend_time_ms": {
          "type": "float"
        },
        "beconn": {
          "type": "long"
        },
        "block_reason": {
          "type": "keyword"
        },
        "body_bytes_sent": {
          "type": "long"
        },
        "body_size": {
          "type": "integer"
        },
        "bosh_az": {
          "type": "keyword"
        },
        "bosh_deployment": {
          "type": "keyword"
        },
        "bosh_director": {
          "type": "keyword"
        },
        "bosh_group": {
          "type": "keyword"
        },
        "bosh_id": {
          "type": "keyword"
        },
        "buildpack": {
          "type": "keyword"
        },
        "bytes_read": {
          "type": "long"
        },
        "bytes_send": {
          "type": "integer"
        },
        "captured_request_cookie": {
          "type": "keyword",
          "ignore_above": 4096
        },
        "captured_response_cookie": {
          "type": "keyword",
          "ignore_above": 4096
        },
        "cc_api": {
          "type": "keyword"
        },
        "cc_class_error": {
          "type": "text"
        },
        "cc_db_query_error": {
          "type": "text"
        },
        "cc_error": {
          "type": "text"
        },
        "cc_error_detail": {
          "type": "text"
        },
        "cc_error_title": {
          "type": "text"
        },
        "cc_go_router": {
          "type": "ip"
        },
        "cc_haproxy_ip": {
          "type": "ip"
        },
        "cc_large_query": {
          "type": "text"
        },
        "cc_large_query_keyword": {
          "type": "keyword"
        },
        "cc_large_query_results": {
          "type": "long"
        },
        "cc_lifecycle_event": {
          "type": "text"
        },
        "cc_message": {
          "type": "text"
        },
        "cc_query": {
          "type": "keyword"
        },
        "cc_query_caller": {
          "type": "keyword"
        },
        "cc_query_length": {
          "type": "long"
        },
        "cc_querytime": {
          "type": "float"
        },
        "cc_request": {
          "type": "keyword"
        },
        "cc_source_ip": {
          "type": "ip"
        },
        "ccdb_response": {
          "type": "text"
        },
        "client_id": {
          "type": "keyword"
        },
        "client_ip": {
          "type": "ip"
        },
        "client_port": {
          "type": "integer"
        },
        "cm_application_id": {
          "type": "keyword"
        },
        "cm_instance_id": {
          "type": "keyword"
        },
        "cm_processing_time": {
          "type": "integer"
        },
        "cm_remote_user": {
          "type": "keyword"
        },
        "concourse_build_id": {
          "type": "integer",
          "ignore_malformed": true
        },
        "concourse_build_subtask": {
          "type": "keyword",
          "ignore_above": 4096
        },
        "concourse_job": {
          "type": "keyword"
        },
        "concourse_pipeline": {
          "type": "keyword"
        },
        "connections_per_window": {
          "type": "integer"
        },
        "connectivity_connection_id": {
          "type": "keyword"
        },
        "data": {
          "properties": {
            "RemoteAddr": {
              "type": "keyword"
            },
            "Upgrade": {
              "type": "keyword",
              "ignore_above": 4096
            },
            "X-Vcap-Request-Id": {
              "type": "keyword",
              "ignore_above": 4096
            },
            "address": {
              "properties": {
                "IP": {
                  "type": "keyword",
                  "ignore_above": 4096
                },
                "Port": {
                  "type": "long"
                },
                "Zone": {
                  "type": "keyword",
                  "ignore_above": 4096
                }
              }
            },
            "attempt": {
              "type": "keyword"
            },
            "backend": {
              "type": "keyword"
            },
            "body": {
              "type": "keyword",
              "ignore_above": 4096
            },
            "conn-reused": {
              "type": "boolean"
            },
            "dial-time": {
              "type": "float"
            },
            "dns-lookup-time": {
              "type": "long"
            },
            "forwarded-url": {
              "type": "keyword",
              "ignore_above": 4096
            },
            "got-connection": {
              "type": "boolean"
            },
            "instance_id": {
              "type": "keyword"
            },
            "isTLS": {
              "type": "keyword"
            },
            "isolation_segment": {
              "type": "keyword"
            },
            "modification_tag": {
              "properties": {
                "guid": {
                  "type": "keyword"
                },
                "index": {
                  "type": "integer"
                }
              }
            },
            "num-endpoints": {
              "type": "integer"
            },
            "payload": {
              "type": "keyword",
              "ignore_above": 4096
            },
            "request-time": {
              "type": "float"
            },
            "retriable": {
              "type": "keyword"
            },
            "route-endpoint": {
              "properties": {
                "AZ": {
                  "type": "keyword",
                  "ignore_above": 4096
                },
                "Addr": {
                  "type": "keyword"
                },
                "RemoteAddr": {
                  "type": "keyword"
                },
                "RouteServiceUrl": {
                  "type": "keyword"
                },
                "Tags": {
                  "properties": {
                    "app_name": {
                      "type": "keyword"
                    },
                    "component": {
                      "type": "keyword"
                    },
                    "env": {
                      "type": "keyword",
                      "ignore_above": 4096
                    },
                    "organization_id": {
                      "type": "keyword"
                    },
                    "organization_name": {
                      "type": "keyword"
                    },
                    "process_id": {
                      "type": "keyword"
                    },
                    "process_instance_id": {
                      "type": "keyword"
                    },
                    "process_type": {
                      "type": "keyword"
                    },
                    "source_id": {
                      "type": "keyword"
                    },
                    "space_id": {
                      "type": "keyword"
                    },
                    "space_name": {
                      "type": "keyword"
                    }
                  }
                }
              }
            },
            "route-service-endpoint": {
              "type": "keyword",
              "ignore_above": 4096
            },
            "route_service_url": {
              "type": "keyword",
              "ignore_above": 4096
            },
            "server_cert_domain_san": {
              "type": "keyword"
            },
            "span-id": {
              "type": "keyword",
              "ignore_above": 4096
            },
            "status-code": {
              "type": "long"
            },
            "subject": {
              "type": "keyword",
              "ignore_above": 4096
            },
            "timeout_seconds": {
              "type": "long"
            },
            "tls-handshake-time": {
              "type": "float"
            },
            "took": {
              "type": "long"
            },
            "trace-id": {
              "type": "keyword",
              "ignore_above": 4096
            },
            "wait_seconds": {
              "type": "long"
            },
            "wrote-headers": {
              "type": "boolean"
            }
          }
        },
        "dial_time_ms": {
          "type": "float"
        },
        "dns_time_ms": {
          "type": "float"
        },
        "dst_ip": {
          "type": "ip"
        },
        "dst_mac": {
          "type": "keyword"
        },
        "dst_port": {
          "type": "integer"
        },
        "duration": {
          "type": "float"
        },
        "endpoint": {
          "type": "keyword"
        },
        "error_message": {
          "type": "keyword",
          "ignore_above": 4096
        },
        "ethtype": {
          "type": "keyword"
        },
        "event_data": {
          "type": "keyword"
        },
        "event_type": {
          "type": "keyword"
        },
        "failed_attempts": {
          "type": "integer"
        },
        "failed_attempts_time_ms": {
          "type": "float"
        },
        "fc_err": {
          "type": "integer"
        },
        "feconn": {
          "type": "long"
        },
        "forwarded_for_host_ip": {
          "type": "ip"
        },
        "frontend_name": {
          "type": "keyword",
          "ignore_above": 4096
        },
        "gorouter_time_ms": {
          "type": "float"
        },
        "host": {
          "type": "keyword"
        },
        "http_request": {
          "type": "text"
        },
        "http_request_id": {
          "type": "keyword"
        },
        "http_user_agent": {
          "type": "keyword"
        },
        "http_version": {
          "type": "text"
        },
        "identifier": {
          "type": "keyword"
        },
        "ingestor_message": {
          "type": "text"
        },
        "ingestor_timestamp": {
          "type": "date",
          "format": "date_optional_time"
        },
        "ingestor_type": {
          "type": "keyword"
        },
        "instance_full_name": {
          "type": "keyword"
        },
        "instance_index": {
          "type": "integer"
        },
        "instance_name": {
          "type": "keyword"
        },
        "iptables_prefix": {
          "type": "keyword"
        },
        "java_class": {
          "type": "keyword"
        },
        "kafka_broker_id": {
          "type": "integer"
        },
        "kafka_client_id": {
          "type": "keyword"
        },
        "kafka_controller_id": {
          "type": "integer"
        },
        "kafka_fetcher_id": {
          "type": "integer"
        },
        "kafka_leader_id": {
          "type": "integer"
        },
        "kafka_log_dir": {
          "type": "keyword"
        },
        "kafka_partition": {
          "type": "integer"
        },
        "kafka_replica_id": {
          "type": "integer"
        },
        "kafka_topic": {
          "type": "keyword"
        },
        "level": {
          "type": "keyword",
          "ignore_above": 4096
        },
        "location": {
          "type": "keyword"
        },
        "log": {
          "type": "keyword"
        },
        "log_data_http_code": {
          "type": "integer"
        },
        "log_data_request_id": {
          "type": "keyword"
        },
        "log_duration": {
          "type": "integer"
        },
        "log_end_timestamp": {
          "type": "date"
        },
        "log_level": {
          "type": "keyword"
        },
        "log_logger": {
          "type": "keyword",
          "ignore_above": 4096
        },
        "log_message": {
          "type": "text"
        },
        "log_source": {
          "type": "keyword"
        },
        "log_start_timestamp": {
          "type": "date"
        },
        "log_thread": {
          "type": "keyword",
          "ignore_above": 4096
        },
        "log_timestamp": {
          "type": "date",
          "format": "date_optional_time"
        },
        "message_app_id": {
          "type": "keyword"
        },
        "message_app_index": {
          "type": "integer"
        },
        "message_code": {
          "type": "integer"
        },
        "message_content_length": {
          "type": "long"
        },
        "message_correlation_id": {
          "type": "keyword"
        },
        "message_host": {
          "type": "keyword"
        },
        "message_http_method": {
          "type": "text"
        },
        "message_http_version": {
          "type": "text"
        },
        "message_referer": {
          "type": "keyword"
        },
        "message_routererror": {
          "type": "text"
        },
        "message_tenant_id": {
          "type": "keyword"
        },
        "message_timestamp": {
          "type": "date"
        },
        "message_uriparam": {
          "type": "keyword"
        },
        "message_uripath": {
          "type": "keyword"
        },
        "message_useragent": {
          "type": "keyword"
        },
        "message_useragent_structured": {
          "properties": {
            "device": {
              "type": "keyword"
            },
            "major": {
              "type": "keyword"
            },
            "minor": {
              "type": "keyword"
            },
            "name": {
              "type": "keyword"
            },
            "os": {
              "type": "keyword"
            },
            "os_full": {
              "type": "keyword"
            },
            "os_major": {
              "type": "keyword"
            },
            "os_minor": {
              "type": "keyword"
            },
            "os_name": {
              "type": "keyword"
            },
            "os_patch": {
              "type": "keyword"
            },
            "os_version": {
              "type": "keyword"
            },
            "patch": {
              "type": "keyword"
            },
            "version": {
              "type": "keyword"
            }
          }
        },
        "method": {
          "type": "keyword"
        },
        "panic_func": {
          "type": "keyword"
        },
        "panic_line": {
          "type": "keyword"
        },
        "partial_instance_guid": {
          "type": "keyword"
        },
        "principal_id": {
          "type": "keyword"
        },
        "queue_job_duration": {
          "type": "float"
        },
        "queue_job_id": {
          "type": "integer"
        },
        "queue_job_status": {
          "type": "keyword"
        },
        "queue_job_type": {
          "type": "keyword"
        },
        "queue_job_worker": {
          "type": "keyword"
        },
        "queue_name": {
          "type": "keyword"
        },
        "remote_address": {
          "type": "keyword"
        },
        "remote_user": {
          "type": "keyword"
        },
        "request": {
          "type": "keyword"
        },
        "request_args": {
          "type": "keyword"
        },
        "request_duration": {
          "type": "float"
        },
        "request_fullpath": {
          "type": "keyword"
        },
        "request_header_count": {
          "type": "integer"
        },
        "request_length": {
          "type": "long"
        },
        "request_method": {
          "type": "keyword"
        },
        "request_path": {
          "type": "keyword"
        },
        "request_path_generic": {
          "type": "keyword"
        },
        "request_time": {
          "type": "float"
        },
        "requests_per_window": {
          "type": "integer"
        },
        "response_header_count": {
          "type": "integer"
        },
        "response_size": {
          "type": "integer"
        },
        "response_status": {
          "type": "integer"
        },
        "response_status_text": {
          "type": "text"
        },
        "response_time": {
          "type": "float"
        },
        "response_time_ms": {
          "type": "float"
        },
        "retries": {
          "type": "long"
        },
        "sap_passport": {
          "type": "text"
        },
        "sap_passport_Action": {
          "type": "keyword"
        },
        "sap_passport_ActionType": {
          "type": "keyword"
        },
        "sap_passport_ClientNumber": {
          "type": "keyword"
        },
        "sap_passport_ComponentName": {
          "type": "keyword"
        },
        "sap_passport_ComponentType": {
          "type": "keyword"
        },
        "sap_passport_ConnectionCounter": {
          "type": "integer"
        },
        "sap_passport_ConnectionId": {
          "type": "keyword"
        },
        "sap_passport_PreviousComponentName": {
          "type": "keyword"
        },
        "sap_passport_RootContextId": {
          "type": "keyword"
        },
        "sap_passport_TraceFlags": {
          "type": "keyword"
        },
        "sap_passport_TransactionId": {
          "type": "keyword"
        },
        "sap_passport_UserId": {
          "type": "keyword"
        },
        "sap_passport_passport_version": {
          "type": "long"
        },
        "sap_passport_service": {
          "type": "long"
        },
        "server_name": {
          "type": "keyword",
          "ignore_above": 4096
        },
        "set_cookie_domain": {
          "type": "keyword"
        },
        "severity": {
          "type": "keyword",
          "ignore_above": 4096
        },
        "span_id": {
          "type": "keyword"
        },
        "src_ip": {
          "type": "ip"
        },
        "src_mac": {
          "type": "keyword"
        },
        "src_port": {
          "type": "integer"
        },
        "srv_conn": {
          "type": "long"
        },
        "srv_queue": {
          "type": "long"
        },
        "ssl_c_ca_err": {
          "type": "keyword"
        },
        "ssl_cipher": {
          "type": "keyword"
        },
        "ssl_fc_err": {
          "type": "keyword"
        },
        "ssl_fc_err_str": {
          "type": "keyword"
        },
        "ssl_fc_has_sni": {
          "type": "keyword",
          "ignore_above": 4096
        },
        "ssl_fc_is_resumed": {
          "type": "integer"
        },
        "ssl_fc_sni": {
          "type": "keyword",
          "ignore_above": 4096
        },
        "ssl_mtls_err": {
          "type": "keyword"
        },
        "ssl_mtls_has_crt": {
          "type": "keyword"
        },
        "ssl_mtls_issuer": {
          "type": "keyword"
        },
        "ssl_mtls_sig_alg": {
          "type": "keyword"
        },
        "ssl_mtls_subject": {
          "type": "keyword"
        },
        "ssl_mtls_used": {
          "type": "keyword"
        },
        "ssl_version": {
          "type": "keyword"
        },
        "stacktrace": {
          "type": "text"
        },
        "status_code": {
          "type": "integer"
        },
        "syslog_facility": {
          "type": "keyword"
        },
        "syslog_filename": {
          "type": "keyword"
        },
        "syslog_hostname": {
          "type": "keyword"
        },
        "syslog_message": {
          "type": "text"
        },
        "syslog_msgid": {
          "type": "keyword"
        },
        "syslog_procid": {
          "type": "keyword"
        },
        "syslog_severity": {
          "type": "keyword"
        },
        "syslog_shipper": {
          "type": "keyword"
        },
        "syslog_timestamp": {
          "type": "date",
          "format": "date_optional_time"
        },
        "tags": {
          "type": "keyword"
        },
        "telemetry_event": {
          "type": "keyword"
        },
        "telemetry_shipper": {
          "type": "keyword"
        },
        "termination_state": {
          "type": "keyword",
          "ignore_above": 4096
        },
        "thread_name": {
          "type": "keyword"
        },
        "tls_time_ms": {
          "type": "float"
        },
        "total_resource_match_time": {
          "type": "float"
        },
        "total_resources_to_match": {
          "type": "long"
        },
        "trace_id": {
          "type": "keyword"
        },
        "true_client_ip": {
          "type": "ip"
        },
        "upstream_connect_time": {
          "type": "float"
        },
        "upstream_header_time": {
          "type": "float"
        },
        "upstream_ratelimit_remaining": {
          "type": "long"
        },
        "upstream_response_time": {
          "type": "float"
        },
        "upstream_runtime": {
          "type": "float"
        },
        "upstream_status": {
          "type": "integer"
        },
        "user_agent": {
          "type": "keyword"
        },
        "user_guid": {
          "type": "keyword"
        },
        "vcap_request_id": {
          "type": "keyword"
        },
        "w3c_parent-id": {
          "type": "keyword"
        },
        "w3c_trace-id": {
          "type": "keyword"
        },
        "w3c_traceparent": {
          "type": "keyword"
        },
        "x_cf_app_instance": {
          "type": "keyword"
        },
        "x_cf_forwarded_url": {
          "type": "keyword"
        },
        "x_custom_host": {
          "type": "keyword"
        },
        "x_forwarded_for": {
          "type": "keyword"
        },
        "x_forwarded_host": {
          "type": "keyword"
        },
        "x_forwarded_proto": {
          "type": "keyword"
        },
        "x_request_id": {
          "type": "keyword"
        },
        "x_ssl_client": {
          "type": "integer"
        },
        "x_ssl_client_issuer_dn": {
          "type": "keyword"
        },
        "x_ssl_client_notafter": {
          "type": "date"
        },
        "x_ssl_client_notbefore": {
          "type": "date"
        },
        "x_ssl_client_session_id": {
          "type": "keyword"
        },
        "x_ssl_client_subject_cn": {
          "type": "keyword"
        },
        "x_ssl_client_subject_dn": {
          "type": "keyword"
        },
        "x_ssl_client_verify": {
          "type": "integer"
        }
      }
    },
    "settings": {
      "index": {
        "replication": {
          "type": "DOCUMENT"
        },
        "refresh_interval": "10s",
        "indexing": {
          "slowlog": {
            "level": "warn",
            "threshold": {
              "index": {
                "warn": "10s",
                "trace": "500ms",
                "debug": "2s",
                "info": "5s"
              }
            }
          }
        },
        "translog": {
          "durability": "async"
        },
        "query": {
          "default_field": "ingestor_message"
        },
        "queries": {
          "cache": {
            "enabled": "false"
          }
        },
        "requests": {
          "cache": {
            "enable": "false"
          }
        },
        "unassigned": {
          "node_left": {
            "delayed_timeout": "2m"
          }
        },
        "number_of_replicas": "0",
        "search": {
          "slowlog": {
            "level": "warn",
            "threshold": {
              "fetch": {
                "warn": "1s",
                "trace": "200ms",
                "debug": "500ms",
                "info": "800ms"
              },
              "query": {
                "warn": "10s",
                "trace": "500ms",
                "debug": "2s",
                "info": "5s"
              }
            }
          }
        },
        "number_of_shards": "4"
      }
    }
  }'
```
</details>

## Import ndjson that are < 2MB
```shell
curl -XPOST https://localhost:9200/http_logs/_doc/_bulk -ku <USERNAME>:<PASSWORD> --data-binary  @<DOCUMENT_PATH>.ndjson
```

## Otherwise split it into smaller portions
```shell
split -l 10000 -a 10 carrier.json /tmp/carrier_bulk
```

```shell
for f in $BULK_FILES; do
    curl -XPOST https://localhost:9200/http_logs/_doc/_bulk -H 'Content-Type: application/json' -ku <USERNAME>:<PASSWORD> --data-binary @$f
done
```

## create workload from existing index
```shell
opensearch-benchmark create-workload --workload="sap_logs_haproxy" --target-hosts="https://localhost:9200" --client-options=basic_auth_user:<USERNAME>,basic_auth_password:<PASSWORD>,verify_certs:false --distribution-version=2.12.0 --indices="http_logs" --output-path="<WORKDIR_FOR_BENCHMARKS>"
```

## execute custom workload
```shell
opensearch-benchmark execute-test --pipeline=benchmark-only --workload-path=".\workloads\default\http_logs" --target-host=https://localhost:9200 --client-options=basic_auth_user:<USERNAME>,basic_auth_password:<PASSWORD>",verify_certs:false --distribution-version=2.13.0 --workload-params="index_settings.json"
```