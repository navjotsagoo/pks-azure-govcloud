# Send Cluster and Container Logs to Azure Log Analytics Workspace

PKS has introduced custom CRDs that allow for configuring fluentd to channel logs to external endpoints.

Create a `Sink` to Send Logs to Azure Workspace.

Example
```
apiVersion: pksapi.io/v1beta1
kind: ClusterLogSink
metadata:
  name: azure-log-workspace-sink
spec:
  type: azure
  output_properties:
      Customer_ID: <Workspace_ID>
      Shared_key:  <Workspace_Secret_Key>
      Endpoint: ods.opinsights.azure.us
```

Reference
- https://docs.pivotal.io/pks/1-6/create-sinks.html
- https://github.com/influxdata/telegraf/tree/master/plugins/outputs/azure_monitor
- https://github.com/yokawasa/fluent-plugin-azure-loganalytics
- https://docs.microsoft.com/en-us/azure/azure-government/documentation-government-services-monitoringandmanagement 
