---

copyright:
  years: 2023
lastupdated: "2023-11-28"

subcollection: whitepaper-vpc-resiliency

keywords:

---

# Resiliency architecture decisions
{: #resiliency-architecture}

The following sections summarize architecture decisions for designing resilient solutions on IBM Cloud.

## High Availability Architecture Decisions
{: #high-availability}

| No. | Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
|  1                    | High Availability Deployment | - Ensure availability of resources in the event of outages. - Support SLA targets for application availability. | - Single zone, single region - Multi zone, single region - Multi-zone, multi region | Single zone, single region  | - Provides protection from host failures - Supports 99.9% availability Recommended approach for  low to medium priority applications or non-production workloads                                                                                                                                                                                                                                                                                              |
|                       |                              |                                                                                                                 |                                                                                     | Multi-zone, single region   | Recommended deployment for Multi-Zone Resiliency Pattern - Provides protection from zone outage - Supports 99.99% availability for active-active application architecture with virtual servers deployed across 3 zones   Recommended approach for: - Core business applications - Production level workloads with stringent resiliency requirements  - Business continuity policies with country boundaries or geo data residence constraints             |
|                       |                              |                                                                                                                 |                                                                                     | Multi-zone, multi-region    | Recommended deployment for Cross-Region Resiliency Pattern - Provides protection from region outage - Supports \>99.99% availability for active-active application architecture with application-aware data replication across regions  Recommended approach for: - Mission critical applications with continuous or near continuous availability requirements - Business continuity policies with cross geo or distance requirements - Disaster Recovery |
{: caption="Table 1. High availability architecture decisions" caption-side="bottom"}

## Backup and Restore Architecture Decisions
{: #backup-and-restore}

| No. | Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| 1                      | App server backup | - Backup App server to enable re-deployment of application in the event of unplanned outages                            | - IBM Cloud Backup - IBM Storage Protect                   | IBM Cloud Backup    | Use Cloud Backup to create volume snapshots of App servers when application-consistent backups are not required.                                            |
| 2                      | DB server backup  | - Backup DB server images to enable recovery of databases in the event of unplanned outages                             | - IBM Cloud Backup - IBM Storage Protect                   | IBM Storage Protect | Use Storage Protect to backup DB servers in DB tier since this is also the recommended tool for transaction consistent backups for the data volumes.        |
| 3                      | DB backup         | - Create transaction consistent database backups to enable recovery of database tier in the event of unplanned outages  | - IBM Storage Protect - DB Backup Tool - BYO Tool          | IBM Storage Protect | Use Storage Protect for application consistent backups of the database. - Storage Protect supports Oracle, IBM Db2, MongoDB, Microsoft SQL Server, SAP HANA |
| 4                      | File Backup       | - Create file system backups for according to business processes                                                        | - IBM Storage Protect - BYO Backup Tool                    | IBM Storage Protect | Use Storage Protect if there is a business need to backup or recover specific files.                                                                        |
| 5                      | Backup Automation | - Schedule regular database backups based on RPO requirements to enable data recovery in the event of unplanned outages | - IBM Cloud Backup - IBM Storage Protect - BYO Backup Tool | IBM Storage Protect | Use Storage Protect to schedule regular backups for the DB tier and define backup policies to manage the creation and deletion of backups.                  |
{: caption="Table 2. Backup and restore architecture decisions" caption-side="bottom"}

## Disaster Recovery Architecture Decisions
{: #disaster recovery}

| No. | Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
|  1                    | DR Approach   | - Ensure availability of resources in the event of unplanned outages - Establish alternate site for failover of workloads in the event of failure in the primary site - Support business targets for RPO/RTO - Support Business Continuity and Disaster Recovery plans  | - Active-Active - Active-Standby / Hot DR Site - Active-Standby / Warm DR Site - Backup & Restore (Cold DR Site)   | Active-Active                   | Recommended approach for: - Mission critical applications with continuous availability requirements and near zero RPO/RTO                                                        |
|                       |               |                                                                                                                                                                                                                                                                         |                                                                                                                    | Active-Standby / Hot DR Site    | Recommended approach for: Core business applications with  - High availability requirements  - RPO \< 15 mins - RTO \< 1 hour                                                    |
|                       |               |                                                                                                                                                                                                                                                                         |                                                                                                                    | Active-Standby / Warm DR Site   | Recommended approach for: Core business applications with  - medium to high availability requirements  - RPO \< 1 hour - RTO \< 4 hours                                          |
|                       |               |                                                                                                                                                                                                                                                                         |                                                                                                                    | Backup & Restore (Cold DR Site) | Recommended approach for: - Dev/test environments - Low priority workloads                                                                                                       |
| 2                     | DR Automation | - Automate recovery tasks to minimize down time                                                                                                                                                                                                                         | - IBM Schematics - Terraform - Ansible - BYO tool                                                                  | IBM Schematics                  | - IBM Schematics provides Terraform-as-a-Service and Ansible-as-a-Service to automate the provisioning and configuration management of the web, app, and DB tiers in the DR site |
{: caption="Table 1. Disaster recovery architecture decisions" caption-side="bottom"}
