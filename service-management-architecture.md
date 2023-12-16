---

copyright:
  years: 2023
lastupdated: "2023-12-15"

subcollection: vpc-resiliency

keywords:

---

# Architecture decisions for service management
{: #service}

The following tables summarize the service management architecture decisions for deploying resilient workloads on IBM Cloud VPC infrastructure.

## Architecture decisions for monitoring
{: #monitoring}

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Operational monitoring of Cloud infrastructure and services | Monitor system and app health to determine the need to failover to an alternative site. | - IBM Cloud Monitoring \n - BYO monitoring tool | IBM Cloud Monitoring | IBM Cloud Monitoring collects and monitors operational metrics for cloud infrastructure as well as the cloud platform and services and provides a single view for all metrics. |
| Operational monitoring of applications | | - IBM Cloud Monitoring \n - Instana (SaaS) \n - BYO monitoring tool | IBM Cloud Monitoring + Instana (SaaS) | Use Instana along with IBM Cloud Monitoring to get additional application performance metrics and automate application performance management. Instana provides data and actionable insights to monitor the applications and automate root-cause analysis. |
{: caption="Table 1. Architecture decisions for monitoring" caption-side="bottom"}

## Architecture decisions for logging
{: #logging}

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Log monitoring of Cloud infrastructure and services | Monitor operational logs to detect issues that might impact the availability of the system and app. | - IBM Cloud Logging \n - BYO logging tool | IBM Cloud Logging | IBM Cloud Logging collects operational logs from applications, platform resources, and infrastructure and provides interfaces to view and analyze all logs. |
| Log monitoring of applications | | - IBM Cloud Logging \n - Application logging tool \n - BYO logging tool | IBM Cloud Logging + Application logging tool | Use the Application Logging Tool to send application logs to IBM Cloud Logging and aggregate application-specific log details. |
{: caption="Table 2. Architecture decisions for logging" caption-side="bottom"}

## Architecture decisions for auditing
{: #auditing}

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Audit logging | Monitor audit logs to track changes to cloud resources and detect potential security problems. | IBM Cloud Activity Tracker \n - Hosted event search \n - Event routing   | IBM Cloud Activity Tracker- Hosted event Search | IBM Cloud Activity Tracker-Hosted event search provides interfaces to capture, store, view, search, and monitor user-initiated actions to provision, access, and manage IBM Cloud resources. |
{: caption="Table 3. Architecture decisions for auditing" caption-side="bottom"}

## Architecture decisions for alerting
{: #alerting}

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Operational alerts | Provide a mechanism to identify and send notifications about operational issues that are found across application and infrastructure. | IBM Cloud Monitoring + IBM Cloud Logging + Event Notifications | IBM Cloud Monitoring + IBM Cloud Logging + Event Notifications | IBM Cloud Monitoring and IBM Cloud Logging support the configuration of alerts to detect operational issues and send notifications to targeted channels. \n \n Event Notifications are used to route the alert events to service destinations to automate response actions. |
| Audit alerts | Provide a mechanism to identify and send notifications about issues that are found in audit logs. | IBM Cloud Activity Tracker + IBM Monitoring + Event Notifications | IBM Cloud Activity Tracker + IBM Monitoring + Event Notifications | IBM Activity Tracker supports the configuration of alerts to detect audit issues and send notifications to targeted channels. \n \n Event Notifications are used to route the alert events to service destinations to automate response actions. |
{: caption="Table 4. Architecture decisions for alerting" caption-side="bottom"}
