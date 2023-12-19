---

copyright:
  years: 2023
lastupdated: "2023-12-15"

subcollection: vpc-resiliency

keywords:

---

# Architecture decisions for disaster recovery
{: #disaster-recovery-architecture}

The following table summarizes the architecture decisions for disaster recovery when you are deploying resilient workloads on IBM Cloud VPC infrastructure.

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Disaster recovery approach   | - Ensure availability of resources if unplanned outages occur. \n - Establish an alternative site for failover of workloads if failure occurs in the primary site. \n - Support business targets for RPO/RTO. \n - Support Business Continuity and Disaster Recovery plans. | - Active-active \n - Active-standby / Hot DR site \n - Active-standby / Warm DR site \n - Backup and restore (Cold DR site)   | Active-active | Recommended approach for mission-critical applications with continuous availability requirements and near zero RPO/RTO. |
| | | | Active-standby (Hot DR site) | Recommended approach for core business applications \n  - High availability requirements \n - RPO \< 15 mins \n - RTO \< 1 hour |
| | | | Active-standby (Warm DR site) | Recommended approach for core business applications \n - Medium to high availability requirements \n - RPO \< 1-hour \n - RTO \< 4 hours |
| | | | Backup and restore (Cold DR site) | Recommended approach for: \n - Dev/test environments \n - Low-priority workloads |
| DR Automation | Automate recovery tasks to minimize downtime | - IBM Schematics \n - Terraform \n - Ansible \n - BYO tool | IBM Schematics | IBM Schematics provides Terraform-as-a-Service and Ansible-as-a-Service to automate the provisioning and configuration management of the web, app, and DB tiers in the DR site. |
{: caption="Table 1. Architecture decisions for disaster recovery" caption-side="bottom"}
