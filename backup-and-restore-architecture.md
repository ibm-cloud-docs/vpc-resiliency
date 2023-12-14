---

copyright:
  years: 2023
lastupdated: "2023-12-12"

subcollection: vpc-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Backup and restore architecture decisions
{: #backup-and-restore-architecture}

The following table summarizes the backup and restore architecture decisions for deploying resilient workloads on IBM Cloud VPC infrastructure.

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| App server backup | Backup App server to enable re-deployment of application in the event of unplanned outages. | - IBM Cloud Backup for VPC \n - IBM Storage Protect | IBM Cloud Backup for VPC | IBM Cloud Backup for VPC supports fast restore from volume snapshots in the same zone and is recommended to backup App servers when application-consistent backups are not required. |
| DB server backup  | Backup DB server images to enable recovery of databases in the event of unplanned outages. | - IBM Cloud Backup for VPC \n - IBM Storage Protect | IBM Storage Protect | Use Storage Protect to backup DB servers since this is also the recommended tool for transaction consistent backups for the database application. |
| DB backup | Create transaction consistent database backups to enable recovery of application data in the event of unplanned outages. | - IBM Storage Protect \n - DB Backup Tool \n - BYO Tool | IBM Storage Protect | IBM Storage Protect provides application consistent backups for databases. It supports Oracle, IBM Db2, MongoDB, Microsoft SQL Server, SAP HANA. |
| File Backup | Create file system backups to support business processes. | - IBM Storage Protect \n - BYO Backup Tool | IBM Storage Protect | Use Storage Protect if there is a business need to backup or recover specific files. |
| Backup Automation | Schedule regular database backups based on RPO requirements to enable data recovery in the event of unplanned outages. | - IBM Cloud Backup for VPC \n - IBM Storage Protect \n - BYO Backup Tool | IBM Storage Protect | IBM Storage Protect supports backup policies to manage the creation and deletion of database consistent backups. |
{: caption="Table 1. Backup and restore architecture decisions" caption-side="bottom"}
