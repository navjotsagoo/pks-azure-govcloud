# VMWare PKS Deployment

Domain Name: `tanzuplatform.net`
PKS SubDomain: `pks.tanzuplatform.net`

**PKS API**
API Hostname: `api.pks.tanzuplatform.net`
When creating certificate, use the same API domain as your CN instead of any wildcards.

When creating a Load Balancer with a publicIP for the PKS API, allow approved port 8443 for ingress traffic. The documentation suggests to also open port 9021 but that is not approved in Cloud One and only needed when using PKS cli, which platform administrators can use from within their secure cloud one environment to provision PKS clusters.

Using PKS CLI to interact with PKS API Broker that gets installed from the tile requires port 9021. So instead of relying upon Cloud One GCDS DNS networking, use local host-based DNS to use PKS CLI because the CLI does not allow IP based connection.

Modify `/etc/hosts` with the following entry in your jumpbox where you install PKS CLI and plan to run `pks` commands:
`Private_IP-PKS_API_Broker api.pks.tanzuplatform.net`


**Kubernetes Cloud Provider**
The default security group does not apply to any virtual machines on PKS. Instead, PKS VMs will inherit the **default** security group listed in the Ops Manager Director Config Tile. So, make sure that security group will allow inbound access over port 8443 (K8 Master) and 443 (Application Workloads). This is a known issue to be fixed in upcoming versions of VMWare PKS on Azure. Ref: https://docs.pivotal.io/pks/1-6/release-notes.html (Azure Default Security Group Is Not Automatically Assigned to Cluster VMs)

**UAA**
Enable option so clusters can use UAA as the OIDC provider. This will allow for generation of role based credentials and access control to clusters by developer on product teams. Leave the default settings. See file `developers-rbac-access.md` for further details. 

**In Cluster Monitoring**
Select *Enable Metric Sink Resources* and *Enable Log Sink Resources* to allow for logs and metrics to be send to Azure Log Analytics Workspace. This will deploy fluentd-bit as containers across all K8 vms to aggregate logs and send them to a designated endpoint in Azure.

References:
- https://docs.pivotal.io/pks/1-6/azure-index.html
