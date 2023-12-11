---

copyright:
  years: 2023
lastupdated: "2023-11-28"

subcollection: whitepaper-vpc-resiliency

keywords:

---

# Other architecture decisions
{: #other-architecture}

## Networking architecture decisions
{: #networking-architecture}

The following sections summarize the networking architecture decisions for resilient solutions on IBM Cloud VPC infrastructure.

### Load balancing architecture decisions
{: #load-balancing}

| No. | Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| 1.1                             | Application Load Balancer | - Route web user http/https requests                                                                                        | - VPC ALB - VPC NLB                                              | VPC ALB                              | VPC ALB is recommended for web-based workloads. - Provides layer 4 and layer 7 load balancing - Supports HTTP, HTTPS, and TCP requests - Supports SSL offloading.                                          |
|                                 |                           |                                                                                                                             |                                                                  | VPC NLB                              | VPC NLB is recommended for workloads that require low latency and high data throughput or VNF routing. - Provides layer 4 load balancing - Supports Source IP Preservation - Supports Direct Server Return |
| 1.2                             | Global Load Balancing     | - Distribute requests across regions or fail over workloads to alternate region in the event of failure in the primary site | - IBM Cloud DNS (Private) - IBM Cloud Internet Services (public) | IBM Cloud Internet Services (public) | The Cloud Internet Services (CIS) Global Load Balancer distributes user requests across regions in multi-region app deployments.                                                                           |
{: caption="Table 1. Load balancing architecture decisions" caption-side="bottom"}

### Domain name system architecture decisions
{: #dns}

| No. | Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| 2.1                             | Public DNS                | - Provide DNS resolution to support use of hostnames instead of IP addresses for applications                               | - IBM Cloud Internet Services (CIS) - IBM Cloud DNS              | IBM Cloud Internet Services (CIS)    | IBM Cloud Internet Services supports provisioning and configuring DNS records for public DNS resolution and can be integrated with the public VPC ALBs for the web tier.                                   |
| 2.2                             | Private DNS               |                                                                                                                             | - IBM Cloud DNS                                                  | IBM Cloud DNS                        | IBM Cloud DNS manages private DNS records, resolves domain names from IBM Cloud's private network, and can be integrated with the private VPC ALBs for the app and DB tiers. |
{: caption="Table 2. Domain name system (DNS) architecture decisions" caption-side="bottom"}

## Security architecture decisions
{: #security}

The following sections summarize the security architecture decisions for resilient solutions on IBM Cloud VPC infrastructure.

### Data encryption architecture decisions
{: #encryption}

| No. | Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| 1.1                                                            | Encryption Approach            | - Encrypt all data to protect from unauthorized disclosure                                             | - Encryption with provider keys - Encryption with customer-managed keys                           | Encryption with customer-managed keys          | Encrypting data with customer-managed keys is recommended since it provides added security and customer control needed to meet regulatory compliance requirements.                                                                                                                                                                                                                                                                 |
| 1.2                                                            | Data Encryption Application    | - Encrypt all application data to protect from unauthorized disclosure                                 | - Storage encryption with customer-managed keys - App-level encryption with customer-managed keys | Storage Encryption with customer-managed keys  | Application data can be stored on VPC File, VPC Block, or Cloud Object Storage (COS) which support encryption with customer-managed keys by selecting a Key Management Service (KMS) for the respective storage service.                                                                                                                                                                                                           |
| 1.3                                                            | Data Encryption  Backups       | - Encrypt all backup data to protect from unauthorized disclosure                                      | Storage encryption with customer-managed keys App-level encryption                                | Storage encryption with customer-managed keys  | Backup data can be stored in COS or VPC Block storage which support encryption with customer-managed keys by selecting a KMS.                                                                                                                                                                                                                                                                                                      |
| 1.4                                                            | Data Encryption  Logs          | - Encrypt all operational and audit logs at rest to protect from unauthorized disclosure               | Storage encryption with customer-managed keys App-level encryption                                | Storage encryption with customer-managed keys  | All operational and audit logs are stored in COS which supports encryption with customer-managed keys by selecting a KMS. |
{: caption="Table 3. Data encryption architecture decisions" caption-side="bottom"}

### Key management architecture decisions
{: #kms}

| No. | Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| 2.1                                                            | Key Lifecycle Management & HSM | - Encrypt data at rest and in transit using customer managed keys to protect from unauthorized access  | Key Protect Hyper Protect Crypto Services (HPCS)                                                  | Key Protect                                    | Key Protect is recommended for applications that need to comply with regulations requiring encryption of data with customer-managed keys. Key Protect provides key management services using a shared (multi-tenant) FIPS 140-2 Level 3 certified hardware security modules (HSMs).                                                                                                                                                |
|                                                                |                                |                                                                                                        |                                                                                                   | Hyper Protect Crypto Services (HPCS)           | HPCS is recommended for financial services and highly regulated industry applications. HPCS provides Key Management Services with the highest level of security and control offered by any cloud provider in the industry. It uses a dedicated (single-tenant) FIPS 140-2 Level 4 certified Hardware Security Module and supports customer-managed master keys, giving the customer exclusive control of the entire key hierarchy. |
| 2.2                                                            | Certificate Management         | - Protect secrets through their entire lifecycle and secure them using access control measures         | Secrets Manager BYO Certificate Manager                                                           | Secrets Manager                                | IBM Secrets Manager creates, leases, and centrally manages secrets used by IBM Cloud Services or customer applications. Secrets are stored in a dedicated instance of Secrets Manager and can be encrypted using any of IBM Cloud Key Management Services. |
{: caption="Table 4. Key management architecture decisions" caption-side="bottom"}

## Service management architecture decisions
{: #service}

The following sections summarize the service management architecture decisions for resilient solutions on IBM Cloud VPC infrastructure.

### Monitoring architecture decisions
{: #monitoring}

| No. | Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| 1.1               | Operational Monitoring of Cloud infrastructure and services | - Monitor system and app health to determine need to failover to alternate site. Operational metrics include CPU and memory usage, API response times, etc.     | - IBM Cloud Monitoring - BYO Monitoring Tool                       | IBM Cloud Monitoring                                               | - IBM Cloud Monitoring collects and monitors operational metrics for cloud infrastructure as well as cloud platform and services and provides a single view for all metrics                                                                                                 |
| 1.2               | Operational Monitoring for Applications                     |                                                                                                                                                                 | - IBM Cloud Monitoring - Instana (SaaS) - BYO Monitoring Tool      | IBM Cloud Monitoring + Instana (SaaS)                              | - Use Instana along with IBM Cloud Monitoring to get additional application performance metrics and automate application performance management. Instana provides data and actionable insights to monitor the applications and automate root-cause analysis.                |
{: caption="Table 5. Monitoring architecture decisions" caption-side="bottom"}

### Logging architecture decisions
{: #logging}

| No. | Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| 1.1               | Operational Monitoring of Cloud infrastructure and services | - Monitor system and app health to determine need to failover to alternate site. Operational metrics include CPU and memory usage, API response times, etc.     | - IBM Cloud Monitoring - BYO Monitoring Tool                       | IBM Cloud Monitoring                                               | - IBM Cloud Monitoring collects and monitors operational metrics for cloud infrastructure as well as cloud platform and services and provides a single view for all metrics                                                                                                 |
| 1.2               | Operational Monitoring for Applications                     |                                                                                                                                                                 | - IBM Cloud Monitoring - Instana (SaaS) - BYO Monitoring Tool      | IBM Cloud Monitoring + Instana (SaaS)                              | - Use Instana along with IBM Cloud Monitoring to get additional application performance metrics and automate application performance management. Instana provides data and actionable insights to monitor the applications and automate root-cause analysis.                |
{: caption="Table 6. Logging architecture decisions" caption-side="bottom"}

### Auditing architecture decisions
{: #auditing}

| No. | Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| 3.1               | Audit Logging                                               | - Monitor audit logs to track changes to cloud resources and detect potential security problems                                                                 | IBM Cloud Activity Tracker - Hosted Event Search - Event Routing   | IBM Cloud Activity Tracker- Hosted Event Search                    | IBM Cloud Activity Tracker-Hosted Event Search provides interfaces to capture, store, view, search, and monitor user-initiated actions to provision, access, and manage IBM Cloud resources.                                                                                |
{: caption="Table 6. Auditing architecture decisions" caption-side="bottom"}

### Alerting architecture decisions
{: #alerting}

| No. | Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| 4.1               | Operational alerts                                          | - Provide a mechanism to identify and send notifications about operational issues found across application and infrastructure                                   | IBM Cloud Monitoring +  IBM Cloud Logging + Event Notifications    | IBM Cloud Monitoring +  IBM Cloud Logging + Event Notifications    | - IBM Cloud Monitoring and IBM Cloud Logging support the configuration of alerts to detect operational issues and send notifications to targeted channels.  Event Notifications can be used to route the alert events to service destinations to automate response actions. |
| 4.2               | Audit alerts                                                | - Provide a mechanism to identify and send notifications about issues found in audit logs                                                                       | IBM Cloud Activity Tracker + IBM Monitoring +  Event Notifications | IBM Cloud Activity Tracker + IBM Monitoring +  Event Notifications | - IBM Activity Tracker supports the configuration of alerts to detect audit issues and send notifications to targeted channels.  Event Notifications can be used to route the alert events to service destinations to automate response actions.                            |
{: caption="Table 6. Alerting architecture decisions" caption-side="bottom"}
