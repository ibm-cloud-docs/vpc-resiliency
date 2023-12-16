---

copyright:
  years: 2023
lastupdated: "2023-12-15"

subcollection: vpc-resiliency

keywords:

---

# Disaster recovery design
{: #disaster-recovery-design}

A disaster recovery (DR) solution includes:
1. Provisioning of infrastructure and required platform and compute services in the recovery site
2. Replicating or backing up data and configuration to the recovery site
3. Application failover to the recovery site.

The recovery site is typically located in a geographically different location to provide protection from natural disasters or outages that impact an entire geographic region. Business continuity policies and regulatory compliance requirements might impose specific distance, country, or geo restrictions for the recovery site.

A disaster recovery solution needs to consider the application’s high availability, business RPO/RTO requirements, and DR site location requirements for regulatory compliance.

## Disaster recovery approaches
{: #disaster-recovery-approach}

Disaster recovery approaches can be summarized as follows:

### Backup and restore (Cold DR site)
{: #backup-restore-dr}

The backup and restore approach centers around the consistent and periodic creation of copies of essential data and system configurations. These backups serve as point-in-time snapshots of the data, capturing the last known consistent state of the information that can be used for recovery purposes. The backups must be stored in a separate location from the primary site to enable recovery if unplanned outages occur affecting the primary site. The recovery process requires the use of backups to provision infrastructure and build the application in the recovery site and to restore the data. Thus, the recovery site is considered a Cold DR site. Depending on the backup frequency and retention policy, the restoration process can return the application to its state before the incident occurred. The time required to restore the data and the amount of data that might potentially be lost between backups (RPO) might impact the speed and completeness of the recovery process. Therefore, this approach is recommended for environments or workloads that can tolerate high recovery times (RTO), for example for dev or test environments or low-priority workloads.

### Active/standby (Hot or Warm DR site)
{: #active-standby-dr}

In the Active/standby approach, the application stack is deployed in both the primary and the standby site. The standby site might have equal or fewer services and resources compared to the primary site. User's transactions are served by the primary site only. Application data, metadata, configuration data, and security data are replicated to the standby site. A global load balancer or DNS changes are used to reroute all client transactions to the standby site when the primary site becomes unavailable. When the standby site is identical to the primary site, it is considered a Hot DR site and the RTO can be low (in the range of minutes to hours). To reduce cost, the standby site might be configured with minimum capacity and autoscaling is enabled to dynamically adjust the compute resources based on the load. This is considered a warm DR site and the recovery time (RTO) is higher (in the range of hours) compared to a hot DR site.

### Active/active
{: #active-active-dr}

In the Active/active approach, the application stack is deployed in both the primary and the secondary site. The application is active in both the primary and the secondary sites and user’s transactions are handled by the application in any of the sites. Application data, metadata, configuration data, and security data are replicated to the secondary site. A global load balancer distributes requests across the two sites. With this approach, it is possible to achieve near zero RPO and RTO. This is the costliest approach and the most complex from an application design perspective. It is recommended for critical applications that require continuous availability and near zero RPO/RTO.

The following table summarizes the main disaster recovery approaches and recommended use.

| DR approach               | RPO (data loss) \n RTO (data recovery)             | Cost | Recommended use  |
|------------------|---------------------|----------|---------|
| Backup and restore \n (Cold DR site) \n - Regular data backups available in DR site \n - Infrastructure and app are deployed on demand in DR site upon outage / unavailability of primary site \n - App and data that is restored from backups                  | RPO: Med/High (based on backup frequency) \n RTO: High (hours to days)          | Low      | - Dev or test environments \n - Low-priority workloads           |
| Active-standby \n (Warm or Hot DR site) \n - Application that is deployed in primary and standby site \n - User transactions that are handled by primary site \n - Data that is replicated to standby site \n - Client transactions rerouted to standby site when primary site is unavailable \n \n Warm DR Site \n - Minimum compute capacity that is deployed at standby site and scaled on demand upon outage/unavailability of primary site | Warm DR Site \n - RPO: Low (data replication) \n - RTO: Medium (minutes to hours)  | Medium   | Core business applications \n - medium to high availability requirements \n - RPO \< 1-hour \n - RTO \< 4 hours  |
| Hot DR site \n - Infrastructure and app that is fully deployed in standby site. | Hot DR Site \n - RPO: Low (data replication) \n  - RTO: Low (minutes)               | High     | Core business applications \n - High availability requirements \n - RPO \< 15 mins \n - RTO \< 1 hour            |
| Active-active \n - Infrastructure and application that is fully deployed in primary and secondary sites \n - User transactions can be handled by application in primary or secondary site \n - Data that is replicated to secondary site      | - RPO: near zero \n - RTO: near zero                                                | High     | Mission-critical applications \n - continuous availability requirements \n - RPO/RTO near zero                 |
{: caption="Table 1. Disaster recovery approaches" caption-side="bottom"}

DR approaches can be combined with high-availability deployment options to meet availability and disaster recovery objectives for enterprise workloads. The following table provides a comparison of the high availability and disaster recovery options.

| HA/DR deployment | Single-zone, Single-region | Multi-zone, Single-region | Multi-zone, Multi-region \n Cold DR site | Multi-zone, Multi-region \n Active-standby \n Warm DR site  | Multi-zone, Multi-region \n Active-standby \n Hot DR site  | Multi-zone, Multi-region \n Active-active |
|------------|------------|------------|------------|------------|------------|------------|
| Availability         | Low                               | Medium/High[^footnote1]                     | Medium/High                     | High               | High/Very High    | Very High  |
| Failure Domain       | Virtual Server                    | Zone                             | Region/Geo                      | Region/Geo         | Region/Geo        | Region/Geo |
| RPO/RTO              | N/A                               | Near zero                        | High                            | Medium             | Low               | Near zero  |
| Cost                 | Low                               | Medium                           | Medium                          | Medium/High        | High              | High       |
| Complexity           | Low                               | Medium                           | Low                             | Medium/High        | High              | Very High  |
| Operational Risk     | Low                               | Low                              | High                            | Medium             | Low/Medium        | Low        |
{: caption="Table 2. Disaster recovery and high availability deployment options" caption-side="bottom"}

[^footnote1]: Availability depends on the number of zones (2 or 3) and app/database HA design

The following table provides recommended DR approaches based on business application class and RPO/RTO. This is an example RPO/RTO per application classification that is provided as a reference. Actual RPO/RTO and business classification depends on each organization.

| Business application class | RPO     | RTO      | DR approach           |
|--------------------------------|-------------|--------------|---------------------------|
| Platinum (Always on)           | Zero        | Near zero    | Active-active             |
| Gold (Almost Always On)        | \<= 15 mins | \<= 1 hour   | Active-standby (Hot DR)   |
| Silver (High availability)     | \<= 1 hour  | 4 - 8 hours  | Active-standby (Warm DR)  |
| Bronze (Moderate availability) | \<= 8 hours | \<= 24 hours | Active-standby (Warm DR)  |
{: caption="Table 3. Disaster recovery recommendations based on business application class" caption-side="bottom"}

## Disaster recovery automation on IBM Cloud
{: #disaster-recovery-automation}

Automating operations is important to enable rapid and consistent recovery processes in response to failures for Cold DR site and Warm DR site disaster recovery approaches.

Use [IBM Cloud Schematics](https://cloud.ibm.com/schematics/overview) to implement runbooks for recovery processes. Schematics workspaces provide Terraform-as-a-Service and Schematics Actions provides Ansible-as-a-Service. [Schematics workspaces](/docs/schematics?topic=schematics-sc-workspaces) automates the deployment and management of IBM Cloud infrastructure and services. Schematics Actions automates configuration management and runs scripted day-2 operations. See [Schematics Use Cases](/docs/schematics?topic=schematics-how-it-works) for more details.

The following table provides a list of recommendations for automating operations to implement disaster recovery.

| Operations | Recommendations | Description |
|-------------|-------------|--------------|
| Restore and create DR resources with images \n (Cold DR Site)  | Take regular snapshots                                  | - Use [IBM Cloud Backup for VPC](/docs/vpc?topic=vpc-storage-overview#vpc-backup-serv-overview) to schedule regular point-in-time snapshots to back up boot and data volumes. \n Snapshots are stored in regional IBM Cloud Object Storage buckets and are available only in the region where they were created. Create [cross-region snapshot copies](/docs/vpc?topic=vpc-snapshots-vpc-about#snapshots_vpc_crossregion_copy) so the snapshots are available for recovery purposes in the target DR region. \n - If the primary site becomes unavailable, create new instances in the DR site from a boot volume snapshot. Attach data volumes to either a new or existing virtual server instance. \n Beware that boot time is increased and performance degraded when provisioning a virtual instance from a bootable snapshot. |
|  | Create a custom image | - Create a [custom image from a boot volume](/docs/vpc?topic=vpc-image-from-volume-vpc&interface=ui) and use it as the golden image, with preinstalled applications and configurations, to reduce the provisioning time for instances in the DR site. \n - The boot volume must be attached to a stopped Virtual Server Instance (VSI) to create the custom image. |
| Streamline app recovery \n (Cold DR and Warm DR sites)       | Use Terraform to provision resources and deploy the app     | - Use Terraform to automate the build-up or scale-out of the DR site on demand.  \n - Monitor the health of the production site and trigger the automation script to build or scale out the DR site. |
|  | Use hostnames for subnets  | Use hostnames and DNS instead of IP addresses to minimize the number of changes that are required to redeploy an application in the DR site. \n - Subnets are zone-specific and do not extend across zones. New IP addresses are assigned to the new instances, which can break any existing configurations such as security rules, application configuration files. |
| Streamline key recovery \n (Warm DR site)                    | Configure key management services for disaster recovery | - For Key Protect, configure the service in the primary site with failover in the DR region to enable automatic rerouting of Key Protect requests if a regional service outage occurs. Create scripts to update the Virtual Private Endpoint (VPE) settings to access the Key Protect Service, specifically the Internet Protocol (IP) address, as part of disaster recovery procedures. \n - For HPCS, configure a failover crypto unit in the DR region. Initialize and configure failover crypto units the same as the operational crypto units before the disaster happens, so they are available if a regional outage occurs. |
{: caption="Table 3. Disaster recovery automation considerations" caption-side="bottom"}
