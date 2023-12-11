---

copyright:
  years: 2023
lastupdated: "2023-11-28"

subcollection: whitepaper-vpc-resiliency

keywords:

---

# Disaster recovery architecture decisions
{: #disaster-recovery-architecture}

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
