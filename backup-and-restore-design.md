---

copyright:
  years: 2023
lastupdated: "2023-12-14"

subcollection: vpc-resiliency

keywords:

---

# Backup and restore design
{: #backup-and-restore-design}

The backup and restore solution for an enterprise application that is deployed in the cloud enables the recovery of lost or compromised data if an unplanned outage occurs. The solution must protect the application, configuration data, and application data. Ideally, the configuration is managed and controlled in Git as part of the DevOps approach.

Restoring data from a backup might take a long time (hours to days) depending on the amount of data to be restored. Therefore, backup and restore must not be considered as a mechanism for achieving high availability.

Backup and restore can be considered as a Disaster Recovery strategy to recover nonproduction or production workloads that can tolerate recovery time objectives (RTO) and recovery point objectives (RPO) in the range of days. For more information, see [Disaster Recovery Approaches](/docs/vpc-resiliency?topic=vpc-resiliency-disaster-recovery-design#disaster-recovery-approach) for details.

## Backup and restore options on IBM Cloud
{: #backup-and-restore-options}

[IBM Cloud Backup for VPC](/docs/vpc?topic=vpc-backup-service-about&interface=ui) is a cloud service that supports the creation and management of boot and data storage volume snapshots. Use IBM Cloud Backup for VPC to schedule regular backups and restore applications deployed in Virtual Servers for VPC when application-consistent backups are not required.

- Boot Volume backup: Create snapshots of the boot volume for the Virtual Servers for VPC hosting the application. [Restore the bootable snapshots](/docs/vpc?topic=vpc-baas-vpc-restore&interface=ui#baas-restore-concept-boot) and use them to re-create the application virtual server instances. Bootable snapshots are not fully hydrated and impact initial performance. Use the fast restore feature to cache snapshots in a different zone for quick restore of individual volumes.

- Data Volume backup: Create snapshots of the data volumes that are attached to the application. The snapshots replicate the data to other availability zones and regions to support the recovery of configuration or any other critical files. See [Creating Snapshots](/docs/vpc?topic=vpc-snapshots-vpc-create&interface=ui) for details.

[IBM Storage Protect](https://cloud.ibm.com/catalog/content/SPonIBMCloud-20c54034-d319-48c0-beb6-0b4adc54265c-global){: external} is an enterprise-level backup solution for virtual, physical, cloud, and software-defined environments, and core applications. Use IBM Storage Protect to create application-consistent backups for database applications that are deployed on Virtual Servers for VPC and to create file or folder level backups.

| Backup feature            | IBM Storage Protect                      | IBM Cloud Backup for VPC                              |
|-------------------------------|--------------------------------|--------------------------------|
| Backup capabilities       | - Agent-based \n - Scheduled backups \n - Backups Management      | - Scheduled backups \n - Backups Management \n - Fast restore clone \n - Cross-regional copies                |
| Backup scope            | Selected VSIs, selected volumes or files in VSIs                        | Selected volumes (boot or data) attached to any VSIs                                         |
| File or folder level support | Yes                                                                     | No                                                                                           |
| Backup storage           | VPC Block Storage or IBM Cloud Object Storage                                            | IBM Cloud Object Storage                                                                                      |
| Database protection      | Application Consistent Backup (Oracle, IBM Db2, MongoDB, MS SQL Server) | Not supported                                                                                |
| Encryption          | In-transit and at rest                                                  | In-transit and at rest                                                                       |
| Recommendation          | Database or folder level backup for multiple virtual servers                             | Complex backup operations for multiple virtual servers that do not require application data consistency |
{: caption="Table 1. Compariosn of backup options for VPC VSIs" caption-side="bottom"}
