**VPC Resiliency on IBM Cloud**

**Whitepaper**

Contents

[1 Introduction](#introduction)

[2 Basic Resiliency Concepts](#basic-resiliency-concepts)

[2.1 High Availability](#high-availability)

[2.2 Backup and Restore](#backup-and-restore)

[2.3 Disaster Recovery](#disaster-recovery)

[3 VPC Resiliency on IBM Cloud Design Considerations](#vpc-resiliency-on-ibm-cloud-design-considerations)

[3.1 High Availability Design Considerations](#_Toc151028063)

[3.1.1 High Availability Deployment Options on IBM Cloud](#high-availability-deployment-options-on-ibm-cloud)

[3.2 Backup and Restore Design Considerations](#backup-and-restore-design-considerations)

[3.2.1 Backup and Restore Options on IBM Cloud](#backup-and-restore-options-on-ibm-cloud)

[3.3 Disaster Recovery Design Considerations](#_Toc151028067)

[3.3.1 Disaster Recovery Automation on IBM Cloud](#disaster-recovery-automation-on-ibm-cloud)

[3.4 Networking Design Considerations](#_Toc151028069)

[3.5 Security Design Considerations](#security-design-considerations)

[3.6 Service Management Design Considerations](#service-management-design-considerations)

[4 Architecture Decisions for Resilient Solutions on IBM Cloud](#architecture-decisions-for-resilient-solutions-on-ibm-cloud)

[4.1 High Availability Architecture Decisions](#_Toc151028073)

[4.2 Disaster Recovery Architecture Decisions](#_Toc151028074)

[4.3 Backup and Restore Architecture Decisions](#_Toc151028075)

[4.4 Networking Architecture Decisions](#_Toc151028076)

[4.5 Security Architecture Decisions](#_Toc151028077)

[4.6 Service Management Architecture Decisions](#_Toc151028078)

[5 References](#_Toc151028079)

# Introduction

High Availability and Disaster Recovery are important considerations to meet continuous business operations and resiliency requirements for enterprise workloads.

High availability solutions are designed to minimize down time under normal operations. Disaster recovery solutions are designed to minimize down time in the event of unplanned outages, whatever the cause.

Cloud platforms are intrinsically built to tolerate failures and provide features to help build reliable and highly available solutions such as: 1) elastic compute and storage at various geographic locations, 2) availability zones engineered to be isolated from failures in other zones, and 3) highly available cloud services deployed across multiple zones or regions, e.g. object storage, Database as a service. However, just moving an application to the cloud does not make it fault-tolerant or highly available. Application resiliency in public clouds is a shared responsibility. To build resilient applications in the cloud you must:

1.  Design applications for availability and recoverability, e.g. applications are

    -   built to scale using modern design practices and microservices architecture for scalability and portability.

    -   built for resiliency using highly available patterns.

    -   DevOps-enabled to support automated provisioning and continuous integration and delivery (CI/CD).

    -   Infrastructure as Code (IaC) enabled for automated provisioning and configuration management.

2.  Leverage platform and infrastructure deployment patterns for high availability and disaster recovery.

3.  Use data replication and backups to protect the data and support failover and disaster recovery.

4.  Implement Operations Management strategy to support availability targets, e.g.

-   establish DevOps process and toolchain for continuous integration / continuous delivery, GitOps enabled for Day 2 operations and procedures.

-   monitor the application and platform infrastructure to detect failures and degradations.

-   setup alerts and operations to automate problem resolution and enable a timely response to incidents.

This document provides design considerations and architecture decisions for deploying resilient applications on IBM Cloud Virtual Private Cloud (VPC) infrastructure. It covers the following solution aspects and domains, as defined in the [Architecture Framework](https://cloud.ibm.com/docs/architecture-framework?topic=architecture-framework-intro)\*:

-   **Resiliency:** High Availability, Backup and Restore, Disaster Recovery

-   **Networking:** Load Balancing, DNS

-   **Security:** Data Security

-   **Service Management:** Monitoring, Logging, Auditing, Alerting

\*The Architecture Framework provides a consistent approach to design cloud solutions by addressing requirements across a set of "aspects" and "domains", which are technology-agnostic architectural areas that need to be considered for any enterprise solution. See [Introduction to the Architecture Framework](https://cloud.ibm.com/docs/architecture-framework?topic=architecture-framework-intro) for more details.

# Basic Resiliency Concepts

## High Availability

High Availability (HA) is the ability of a system or application to keep running in a healthy state, without significant downtime.

A common practice for achieving high availability is to use redundant resources to avoid single points of failure. Leverage availability zones and regions on IBM Cloud to implement redundant deployments and fault isolation patterns for the application infrastructure. Use platform as a service and other cloud services that are provisioned across multiple availability zones to provide built-in resiliency. However, to meet business continuity requirements, the application must also be designed for high availability.

Applications designed for high availability are typically deployed in clusters following an active-active or active-standby architecture.

**Active-Active architecture**

In an active-active architecture, two active instances of the application are deployed across different failure domains to provide scalability and high availability. All the instances handle application requests concurrently. A load balancer is used to distribute requests among the active application instances. Stateful applications, like databases, require data replication between instances.

**Active-Standby architecture**

In an active-standby architecture, two instances of the application are deployed: an active instance and a standby instance. However, application requests are sent to the active instance only. A load balancer is used to route requests to the standby instance when the active instance becomes unavailable. Stateful applications, like databases, require synchronous data replication between instances.

## Backup and Restore

Backups are used to provide protection from data loss. Backup and restore solutions create copies of data and support restoring data from backup copies to enable the recovery of lost or compromised data in the event of an unplanned outage, such as hardware failure, data center failure, data corruption, accidental deletion, or even a cybersecurity breach.

## Disaster Recovery

Disaster Recovery is the ability to protect a system or application from natural or unplanned outages by having a set of policies, tools, and procedures to enable the recovery or continuation of vital technology infrastructure and applications following an event, whatever the cause (e.g. natural disaster or outage caused by hardware failure, data corruption, accidental deletion, cybersecurity breach, etc.)

Disaster Recovery requirements are generally expressed in terms of:

-   Recovery Time Objective (RTO: the targeted duration of time within which a business process shall be restored after a disaster or outage.

-   Recovery Point Objective (RPO): the maximum targeted time period in which data might be lost from an IT service due to a disaster or outage.

# VPC Resiliency on IBM Cloud Design Considerations

## High Availability Design Considerations

IBM Cloud supports high availability application deployments in a single zone, across multiple zones in a multi-zone region, and across multiple regions to achieve high availability.

Failure domains determine the level of protection from infrastructure failures for each option. Application instances deployed in multiple availability zones are within a metro area connected over a low latency network and data can be replicated synchronously across the zones. Application instances deployed in multiple regions are typically in different geos connected over a WAN and data can be replicated asynchronously across the regions. The following table shows application deployment options based on failure domains available in a public cloud.

| **HA Deployment** | **Single-Zone** **Single Region** | **Multi-Zone,**  **Single Region** | **Multi-Zone,**  **Multi-Region** |
|-------------------|-----------------------------------|------------------------------------|-----------------------------------|
| Availability      | Low/Med                           | High                               | Very High                         |
| Failure Domain    | Virtual Server / Physical Host    | Zone                               | Region                            |
| Cost / Complexity | Low                               | Medium                             | High                              |

### High Availability Deployment Options on IBM Cloud

**Single-Zone deployment**

In single-zone deployments, multiple compute instances are deployed in one zone leveraging [Placement Groups](https://cloud.ibm.com/docs/vpc?topic=vpc-about-placement-groups-for-vpc&interface=ui) to provision virtual servers in separate physical hosts and [VPC Autoscale](https://cloud.ibm.com/docs/vpc?topic=vpc-creating-auto-scale-instance-group&interface=ui) to enable dynamic adjustment of capacity based on load changes. Single zone deployment provides cost-effective solutions with 99.9% infrastructure availability which might be appropriate for non-production environments or non-business critical applications. However, there is no protection from zone outages.

**Multi-Zone, Single-Region Deployment**

In a multi-zone, single-region deployment, multiple compute instances are deployed across two or more availability zones within a region. Multi-zone, single region deployment can provide up to 99.99% infrastructure availability, when the application is deployed across 3 availability zones. This deployment protects the application from zone failures and is suitable for production-level enterprise workloads with \>99.9% availability requirements. Actual application availability depends on the application high availability design.

**Multi-Zone, Multi-Region Deployment**

A multi-zone, multi-region deployment provides protection against region outages. This is the recommended deployment for mission critical applications with continuous or near continuous availability requirements. This deployment also supports out of region disaster recovery and business continuity policies with cross geo or distance requirements.

Multi-zone deployments rely on application-aware data replication across availability zones and support active-active and active-standby architecture patterns. Multi-zone, multi-region deployments support architecture patterns for enterprise applications with continuous availability/always on requirements. The following tables show a comparison of the different deployment options and recommended use.

| **Deployment**             | **Availability** | **Description**                                                                                                                                                                  | **Recommended Usage**                                                                                                                                                                      |
|----------------------------|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Single Zone                | 99.9%1           | - Multiple compute instances in one zone - Protection from infrastructure failures - Low/Medium cost                                                                             | - Low to medium priority applications - Non-production workloads                                                                                                                           |
| Multi-zone,  Single Region | 99.99%1          | - Multiple compute instances across 2 or more availability zones - Synchronous data replication across zones - Protection from zone outages  - Medium/high cost                  | - Core business applications - Production level workloads with stringent resiliency requirements  - Business continuity policies with country boundaries or geo data residence constraints |
| Multi-zone,  Multi-Region  | \>99.99%2        | - Multiple compute instances across multiple availability zones in 2 or more regions - Asynchronous data replication across regions - Protection from region outages - High cost | - Mission critical applications with continuous or near continuous availability requirements - Business continuity policies with cross geo or distance requirements - Disaster Recovery    |

1.  Based on Cloud infrastructure [SLA](https://www.ibm.com/support/customer/csol/terms/?id=i126-9268&lc=en#detail-document) . 99.99% availability requires redundant deployments across 3 availability zones within a region and application designed following active-active clustering architecture.

2.  Based on active-active architecture with application-aware data replication across regions

## Backup and Restore Design Considerations

The backup and restore solution for an enterprise application deployed in the cloud must protect the application, configuration data, and application data. Ideally, configuration should be managed and controlled in Git as part of the DevOps approach.

Restoring data from a backup might take a long time (hours to days) depending on the amount of data to be restored. Therefore, backup and restore must not be considered as a mechanism for achieving high availability.

Backup and restore can be considered as a Disaster Recovery strategy to recover non-production workloads or production workloads that can tolerate recovery time objectives (RTO) and recovery point objectives (RPO) in the range of days.

### Backup and Restore Options on IBM Cloud

[IBM Cloud Backup for VPC](https://cloud.ibm.com/docs/vpc?topic=vpc-backup-service-about&interface=ui) is a cloud service that supports the creation and management of boot and data storage volume snapshots. Use IBM Cloud Backup for VPC and IBM Storage Protect to backup and restore applications deployed in VPC Virtual Servers as follows:

-   Virtual Server backup: create a custom image from the boot volume of the VPC Virtual Servers hosting the application and use them as the golden image for the application and configuration. Use the custom image to quickly recover the application instances in another availability zone or region. See [Creating an image from a volume](https://cloud.ibm.com/docs/vpc?topic=vpc-image-from-volume-vpc&interface=ui) for details. Note that the virtual server must be stopped to create an image from the boot volume.

-   Boot Volume backup: create snapshots of the boot volume for the VPC Virtual Servers hosting the application. [Restore the bootable snapshots](https://cloud.ibm.com/docs/vpc?topic=vpc-baas-vpc-restore&interface=ui#baas-restore-concept-boot) and use them to re-create the application virtual server instances. Note that bootable snapshots are not fully hydrated which will impact initial performance.

-   Data Volume backup: create snapshots of the data volumes attached to the application. The snapshots replicate the data to other availability zones and regions to support the recovery of configuration or any other critical files. See [Creating Snapshots](https://cloud.ibm.com/docs/vpc?topic=vpc-snapshots-vpc-create&interface=ui) for details.

[IBM Storage Protect](https://cloud.ibm.com/catalog/content/SPonIBMCloud-20c54034-d319-48c0-beb6-0b4adc54265c-global) is an enterprise-level backup solution for virtual, physical, cloud, and software-defined environments, as well as core applications. Use IBM Storage Protect to create application-consistent backups for database applications deployed on VPC Virtual Servers and to create file or folder level backups.

| **Backup Feature**            | **IBM Storage Protect**                                                 | **IBM Cloud Backup for VPC**                                                                 |
|-------------------------------|-------------------------------------------------------------------------|----------------------------------------------------------------------------------------------|
| **Backup Capabilities**       | Agent based Scheduled backups Backups Management                        | Scheduled backups Backups Management Fast restore clone Cross-regional copies                |
| **Backup Scope**              | Selected VSIs, selected volumes or files in VSIs                        | Selected volumes (boot or data) attached to any VSIs                                         |
| **File/Folder level support** | Yes                                                                     | No                                                                                           |
| **Backup Storage**            | VPC Block Storage or IBM COS                                            | IBM COS                                                                                      |
| **Database Protection**       | Application Consistent Backup (Oracle, IBM Db2, MongoDB, MS SQL Server) | Not supported                                                                                |
| **Encryption**                | In-transit and at rest                                                  | In-transit and at rest                                                                       |
| **Recommendation**            | DB or folder level backup for multiple VSIs                             | Complex backup operations for multiple VSIs that do not require application data consistency |

[Comparison of Backup Options for VPC VSIs in IBM Cloud]

## Disaster Recovery Design Considerations

A disaster recovery solution includes provisioning of infrastructure and required platform and compute services in the recovery site, replicating or backing up data and configuration to the recovery site, and application fail over to the recovery site. The recovery site is typically located in a geographically different location to provide protection from natural disasters or outages impacting an entire geographic region. Business continuity policies and regulatory compliance requirements might impose specific distance, country, or geo restrictions for the recovery site.

A disaster recovery solution needs to consider the application’s high availability, business RPO/RTO requirements, and DR site location requirements for regulatory compliance. Disaster Recovery approaches can be summarized as follows:

**Backup & Restore (Cold DR Site)**

The backup and restore approach centers around the consistent and periodic creation of copies of essential data and system configurations. These backups serve as point-in-time snapshots of the data, capturing the last known consistent state of the information that can be used for recovery purposes. The backups must be stored in a separate location from the primary site to enable recovery in the event of unplanned outages affecting the primary site. Recovery process requires using backups to provision infrastructure and build the application in the recovery site and to restore the data. Thus, the recovery site is considered a Cold DR site. Depending on the backup frequency and retention policy, the restoration process can return the application to its state before the incident occurred. The time required to restore the data and the amount of data that could potentially be lost between backups (RPO) may impact the speed and completeness of the recovery process. Therefore, this approach is recommended for environments or workloads that can tolerate high recovery times (RTO), e.g. for dev/test environments or low priority workloads.

**Active/Standby (Hot or Warm DR Site)**

In the Active/Standby approach, the application stack is deployed in both the primary and the standby site. The standby site may have equal or fewer services and resources compared to the primary site. User's transactions are served by the primary site only. Application data, metadata, configuration data, and security data are replicated to the standby site. A global load balancer or DNS changes are used to re-route all client transactions to the standby site when the primary site becomes unavailable. When the standby site is identical to the primary site, it is considered a Hot DR site and the RTO can be low (in the range of minutes to hours). To reduce cost, the standby site might be configured with minimum capacity and autoscaling enabled to dynamically adjust the compute resources based on the load. This is considered a warm DR site and the recovery time (RTO) might be higher (in the range of hours) compared to a hot DR site.

**Active/Active**

In the Active/Active approach the application stack is deployed in both the primary and the secondary site. The application is active in both the primary and the secondary sites and user’s transactions can be handled by the application in any of the sites. Application data, metadata, configuration data, and security data are replicated to the secondary site. A global load balancer distributes requests across the two sites. With this approach, it is possible to achieve near zero RPO and RTO. This is the costliest approach and most complex from an application design perspective. It is recommended for critical applications that require continuous availability and near zero RPO/RTO.

The following table summarizes the main Disaster Recovery approaches and recommended use.

| **DR Approach**                                                                                                                                                                                                                                                                                                                                                                                                                                                              | **RPO (data loss)** **RTO (data recovery)**                                  | **Cost** | **Recommended Use**                                                                                            |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|----------|----------------------------------------------------------------------------------------------------------------|
| **Backup and Restore (Cold DR Site)** -Regular data backups available in DR site  -Infrastructure and app are deployed on demand in DR site upon outage / unavailability of primary site -App and data restored from backups                                                                                                                                                                                                                                                 | RPO: Med/High (based on backup frequency) RTO: High (hours to days)          | Low      | - Dev/test environments - Low priority workloads                                                               |
| **Active-Standby (Warm or Hot DR Site)** - Application deployed in primary and standby site  - User transactions handled by primary site - Data replicated to standby site - Client transactions re-routed to standby site when primary site is unavailable **Warm DR Site** - Minimum compute capacity deployed at standby site and scaled on demand upon outage/unavailability of primary site  **Hot DR Site** - Infrastructure and app fully deployed in standby site.   | **Warm DR Site** RPO: Low (data replication) RTO: Medium (minutes to hours)  | Medium   | Core business applications with  - medium to high availability requirements  - RPO \< 1 hour - RTO \< 4 hours  |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | **Hot DR Site** RPO: Low (data replication) RTO: Low (minutes)               | High     | Core business applications with  - High availability requirements  - RPO \< 15 mins - RTO \< 1 hour            |
| **Active-Active** - Infrastructure and application fully deployed in primary and secondary sites  - User transactions can be handled by application in primary or secondary site - Data replicated to secondary site                                                                                                                                                                                                                                                         | RPO: near zero RTO: near zero                                                | High     | Mission critical applications with  - continuous availability requirements - RPO/RTO near zero                 |

DR approaches can be combined with high-availability deployment options to meet availability and Disaster Recovery objectives for enterprise workloads. The following provides a comparison of the High Availability and Disaster Recovery options.

| **HA/DR** **Deployment** | **Single-Zone** **Single Region** | **Multi-Zone** **Single Region** | **Multi-Zone** **Multi-Region** |                    |                   |            |
|--------------------------|-----------------------------------|----------------------------------|---------------------------------|--------------------|-------------------|------------|
|                          |                                   |                                  | **Backup & R** **Cold DR Site** | **Active-Standby** | **Active-Active** |            |
|                          |                                   |                                  |                                 | **Warm DR Site**   | **Hot DR Site**   |            |
| **Availability**         | Low                               | Medium/High1                     | Medium/High                     | High               | High/Very High    | Very High  |
| **Failure Domain**       | Virtual Server                    | Zone                             | Region/Geo                      | Region/Geo         | Region/Geo        | Region/Geo |
| **RPO/RTO**              | N/A                               | Near zero                        | High                            | Medium             | Low               | Near Zero  |
| **Cost**                 | Low                               | Medium                           | Medium                          | Medium/High        | High              | High       |
| **Complexity**           | Low                               | Medium                           | Low                             | Medium/High        | High              | Very High  |
| **Operational Risk**     | Low                               | Low                              | High                            | Medium             | Low/Medium        | Low        |

1.  Availability depends on number of zones (2 or 3) and app/database HA design

The following table provides recommended DR approaches based on Business Application class and RPO/RTO. This is an example RPO/RTO per application classification which is provided as a reference. Actual RPO/RTO and business classification depends on each organization.

| **Business Application Class** | **RPO**     | **RTO**      | **DR Approach**           |
|--------------------------------|-------------|--------------|---------------------------|
| Platinum (Always on)           | Zero        | Near zero    | Active-Active             |
| Gold (Almost Always On)        | \<= 15 mins | \<= 1 hour   | Active-Standby (Hot DR)   |
| Silver (High Availability)     | \<= 1 hour  | 4 - 8 hours  | Active-Standby (Warm DR)  |
| Bronze (Moderate Availability) | \<= 8 hours | \<= 24 hours | Active-Standby (Warm DR)  |

### Disaster Recovery Automation on IBM Cloud

Automating operations is important to enable rapid and consistent recovery processes in response to failures for Cold DR site and Warm DR site Disaster Recovery approaches. Use [IBM Cloud Schematics](https://cloud.ibm.com/schematics/overview) to implement runbooks for recovery processes. Schematics Workspaces provides Terraform-as-a-Service and Schematics Actions provides Ansible-as-a-Service. [Schematics Workspaces](https://cloud.ibm.com/docs/schematics?topic=schematics-sc-workspaces) automates the deployment and management of IBM Cloud infrastructure and services. Schematics Actions automates configuration management and runs scripted day-2 operations. See [Schematics Use Cases](https://cloud.ibm.com/docs/schematics?topic=schematics-how-it-works) for more details.

The following table provides a list of considerations for automating operations to implement Disaster Recovery.

| **Situation**                                             | **Option**                                              | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|-----------------------------------------------------------|---------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Restore & create DR resources with images (Cold DR Site)  | Take regular Snapshots                                  | - Use [IBM Cloud Backup for VPC](https://cloud.ibm.com/docs/vpc?topic=vpc-storage-overview#vpc-backup-serv-overview) to schedule regular point-in-time snapshots to back up boot and data volumes.  - The snapshots are stored in regional IBM Cloud Object Storage buckets and are available only in the region where they were created. Create [cross-region snapshot copies](https://cloud.ibm.com/docs/vpc?topic=vpc-snapshots-vpc-about#snapshots_vpc_crossregion_copy) so the snapshots are available for recovery purposes in the target DR region.  - In the event the primary site becomes unavailable, create new instances in the DR site from a boot volume snapshot. Attach data volumes to either a new or existing virtual server instance.  - Note that boot time is increased and performance degraded when provisioning a virtual instance from a bootable snapshot. |
|                                                           | Create a Custom Image                                   | - Create a [custom image from a boot volume](https://cloud.ibm.com/docs/vpc?topic=vpc-image-from-volume-vpc&interface=ui) and use it as the golden image, with preinstalled applications and configurations, to reduce the provisioning time for instances in the DR site. Note that the boot volume must be attached to a stopped Virtual Server Instance (VSI) to create the custom image.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Streamline app recovery (Cold DR and Warm DR sites)       | Use Terraform to provision resources and deploy app     | - Use Terraform to automate the build out or scale out of the DR site on demand.  - Monitor the health of the production site and trigger the automation script to build or scale out the DR site.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|                                                           | Use hostnames for Subnets                               | - Subnets are zone-specific and do not extend across zones. New IP addresses are assigned to the new instances, which can break any existing configurations such as security rules, application configuration files. Use hostnames and DNS instead of IP addresses to minimize the number of changes required to redeploy an application in the DR site.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Streamline key recovery (Warm DR Site)                    | Configure Key Management Services for Disaster Recovery | - For Key Protect, configure the service in the primary site with failover in the DR region to enable automatic re-routing of Key Protect requests in the event of a regional service outage. Create scripts to update the Virtual Private Endpoint (VPE) settings to access the Key Protect Service, specifically the Internet Protocol (IP) address, as part of disaster recovery procedures. - For HPCS, configure a failover crypto unit in the DR region. Initialize and configure failover crypto units the same as the operational crypto units before the disaster happens, so they are available in the event of a regional outage.                                                                                                                                                                                                                                           |

## Networking Design Considerations

IBM Cloud Virtual Private Cloud infrastructure and network services support multi-zone and multi-region deployment architectures for high availability and disaster recovery.

VPC Load balancers are used to distribute traffic across multiple zones within a region and support highly available configurations. There are two types of VPC Load Balancers:

-   Application Load Balancers (ALB): provide layer 4 and layer 7 load balancing, support SSL offloading and are recommended for web-based workloads.

-   Network Load Balancers: provide layer 4 load balancing and supports Source IP Preservation and Direct Server Return. It is recommended for workloads that require low latency and high data throughput.

The following table shows a comparison of these Load Balancers.

| **Category**                 | **Network Load Balancer (NLB)**                                                                                                                                                                                                                 | **Application Load Balancer (ALB)**                                                                                                        |
|------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| **Features**                 | - Layer 4 load balancing - VNF routing - TCP and UDP support - Direct Server Return (DSR) - Source IP Preserved                                                                                                                                 | - Layer 4 and 7 load balancing - Policy-based routing - HTTP, HTTPS, TCP support - SSL offloading - Source IP Preserved (proxy)            |
| **Recommended Use**          | Workloads that require low latency & high data throughput                                                                                                                                                                                       | Web-based workloads                                                                                                                        |
| **HA mode**                  | Active-standby (w/ single VIP)                                                                                                                                                                                                                  | Active-active (w/ multiple VIPs assigned to a DNS name)                                                                                    |
| **Instance group support**   | Yes (see [Integrating NLB for VPC with instance groups](https://cloud.ibm.com/docs/vpc?topic=vpc-nlb-integration-with-instance-groups))                                                                                                         | Yes (see [Integrating ALB for VPC with instance groups](https://cloud.ibm.com/docs/vpc?topic=vpc-lbaas-integration-with-instance-groups))  |
| **Monitoring metrics**       | Yes                                                                                                                                                                                                                                             | Yes                                                                                                                                        |
| **Multi-zone support**       | Accepts members across all three availability zones. Deploy in each zone along with Global Load Balancer for multi-zone availability (see [Multi-zone support](https://cloud.ibm.com/docs/vpc?topic=vpc-network-load-balancers#nlb-use-case-2)) | Yes. Deploy in region and configure subnets across availability zones.                                                                     |
| **Security group support**   | No                                                                                                                                                                                                                                              | Yes (see [Integrating an ALB for VPC with security groups](https://cloud.ibm.com/docs/vpc?topic=vpc-alb-integration-with-security-groups)) |
| **Source IP preserved**      | Yes                                                                                                                                                                                                                                             | Yes ([with proxy protocol](https://cloud.ibm.com/docs/vpc?topic=vpc-advanced-traffic-management#preserving-end-client-ip-address))         |
| **SSL offloading**           | No                                                                                                                                                                                                                                              | Yes                                                                                                                                        |
| **Types of load balancers**  | Public and private                                                                                                                                                                                                                              | Public and private                                                                                                                         |
| **Virtual IP Address (VIP)** | Single                                                                                                                                                                                                                                          | Multiple                                                                                                                                   |
| **Route mode for VNFs**      | Yes (see [Setting up high availability for Virtual Network Functions (VNF)](https://cloud.ibm.com/docs/vpc?topic=vpc-about-vnf))                                                                                                                | No                                                                                                                                         |
| **Member type**              | Virtual server instances                                                                                                                                                                                                                        | Virtual server instances, Bare Metal, Power Systems Virtual Server                                                                         |

The Cloud Internet Service (CIS) provides a Global Load Balancer that can distribute traffic on the public network across availability zones within a region in a multi-zone deployment or across multiple regions in a multi-region deployment.

In active-active DR deployments, use IBM Cloud Internet Services (CIS) to reduce network latency for application users in different geographies. Define location-based pools and configure CIS to redirect users to the closest VPC load balancer based on the geographical location of the user requests.

CIS can also be configured as a proxy to a VPC Application Load Balancer in multi-zone or multi-region deployments to leverage CIS application security capabilities such as Web Application Firewall (WAF) and DDoS protection. See [Proxying DNS records and global load balancers](https://cloud.ibm.com/docs/cis?topic=cis-dns-concepts#dns-concepts-proxying-dns-records) for details.

![A screenshot of a computer Description automatically generated](4d9193657d91ddab0444a111d6e88d65.png)

[Cloud Internet Services (CIS) Proxy for VPC Load Balancers]

## Security Design Considerations

It is important to use encryption to protect data from unauthorized disclosure. All application data, including configuration and meta data, as well as all security data, including logs and credentials to access application or cloud resources must be protected.

IBM Cloud data services encrypt data by default using randomly generated keys and support encryption with customer-managed keys using a Key Management Service (KMS). The following table provides an overview of data protection services in IBM Cloud.

| **Services**                  | **Secret Types**                                                     | **Usage**                               | **Tenancy**   | **HSM Backed**         |
|-------------------------------|----------------------------------------------------------------------|-----------------------------------------|---------------|------------------------|
| Secrets Manager               | Arbitrary secrets IAM credentials Key-value secrets User Credentials | Access to resources                     | Single tenant | N/A                    |
|                               | SSL/TLS certificates                                                 | Data in-transit encryption              |               |                        |
| Key Protect                   | Symmetric encryption keys                                            | Data at-rest encryption TLS/SSL Offload | Multi tenant  | FIPS 140-2 Level 3 HSM |
| Hyper Protect Crypto Services | Symmetric encryption keys                                            | Data at-rest-encryption TLS/SSL Offload | Single tenant | FIPS 140-2 Level 4 HSM |

[Key features of IBM Cloud data protection services]

Secret Management and Key Management Solutions in IBM Cloud are regional services deployed across multiple availability zones in a region for resiliency. For optimal performance and security, keep the root keys in the same region as the encrypted resources and follow disaster recovery recommendations for each data protection service, as follows:

-   To ensure cross-region availability of the secrets, provision a Secrets Manager instance in an alternate region and copy the secrets in the alternate region following the instructions provided in [this document](https://cloud.ibm.com/docs/secrets-manager?topic=secrets-manager-ha-dr&interface=ui#manual-backup).

-   If using Key Protect, configure the service in the primary region with failover to DR region to enable automatic re-routing of Key Protect requests in the event of a regional service outage. If using HPCS, configure failover crypto unit in DR region, and initialize and configure failover crypto units the same as the operational crypto units before the disaster happens, so they are available in the event of a regional outage. See [Restoring your data by using failover crypto units](https://cloud.ibm.com/docs/hs-crypto?topic=hs-crypto-restore-data#restore-data-failover-crypto-units) for more information about how to use failover crypto units to restore data.

-   "Disaster recovery" does not include the ability to recover from user-initiated accidental deletions. IBM Cloud provides a [30-day grace period to restore a deleted key](https://cloud.ibm.com/docs/key-protect?topic=key-protect-delete-purge-keys). To protect a key from accidental deletion after the 30-day grace period, the user must create and maintain up-to-date backups of the key.

## Service Management Design Considerations

Operations Management is a key aspect of building resilient applications. To support the application availability targets you must:

-   Continuously monitor the application and platform infrastructure to detect failures and degradations.

-   Integrate automated monitoring with rich notification tooling to automate problem resolution and enable a timely response to incidents.

It is important to monitor the health of all the components of the solution, including infrastructure, cloud services, and application as well as operational logs to detect and correct issues that might affect the availability of enterprise applications. Proper operational monitoring can help you determine whether you need to fail over to an alternate site or whether operations have returned to normal after a system disruption. Equally important is to track and monitor all activity performed on the IBM Cloud to detect changes and potential security threats that might impact the availability of the applications deployed on IBM Cloud.

Use [IBM Cloud Monitoring](https://cloud.ibm.com/docs/monitoring?topic=monitoring-about-monitor) and [IBM Log Analysis](https://cloud.ibm.com/docs/log-analysis?topic=log-analysis-getting-started) to monitor operational metrics and logs. Use [IBM Cloud Activity Tracker](https://cloud.ibm.com/docs/activity-tracker?topic=activity-tracker-getting-started) to monitor audit logs for the application, IBM Cloud Infrastructure, and other IBM Cloud services. Configure IBM Cloud Monitoring, IBM Logging, and Activity Tracker to set up alerts, send notifications and trigger actions in response to the alerts.

Incident detection, notification, escalation, discovery, and declaration must be considered to provide realistic, achievable objectives that provide business value. Use [IBM Cloud Event Notifications](https://cloud.ibm.com/docs/event-notifications?topic=event-notifications-en-about) to route events associated with IBM Cloud resources (event sources) to a destination (delivery target for a notification) to trigger actions and help automate the response to critical incidents. Define and build event notifications by linking event sources and destinations. As an example, select event sources to detect cloud provider level (e.g., region, zone, services), network level (e.g. load balancers, global load balancers), security level and application level critical events and integrate them with destination targets. Select destinations such as ServiceNow to collect all events and assign owners and AIOps tool to automate response to events like file system is full.

# Architecture Decisions for Resilient Solutions on IBM Cloud

## High Availability Architecture Decisions

| **\#**                | **AD**                       | **Requirement**                                                                                                 | **Alternative**                                                                     | **Decision**                | **Rationale**                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-----------------------|------------------------------|-----------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **High Availability** |                              |                                                                                                                 |                                                                                     |                             |                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|  1                    | High Availability Deployment | - Ensure availability of resources in the event of outages. - Support SLA targets for application availability. | - Single zone, single region - Multi zone, single region - Multi-zone, multi region | Single zone, single region  | - Provides protection from host failures - Supports 99.9% availability Recommended approach for  low to medium priority applications or non-production workloads                                                                                                                                                                                                                                                                                              |
|                       |                              |                                                                                                                 |                                                                                     | Multi-zone, single region   | **Recommended deployment for Multi-Zone Resiliency Pattern** - Provides protection from zone outage - Supports 99.99% availability for active-active application architecture with virtual servers deployed across 3 zones   Recommended approach for: - Core business applications - Production level workloads with stringent resiliency requirements  - Business continuity policies with country boundaries or geo data residence constraints             |
|                       |                              |                                                                                                                 |                                                                                     | Multi-zone, multi-region    | **Recommended deployment for Cross-Region Resiliency Pattern** - Provides protection from region outage - Supports \>99.99% availability for active-active application architecture with application-aware data replication across regions  Recommended approach for: - Mission critical applications with continuous or near continuous availability requirements - Business continuity policies with cross geo or distance requirements - Disaster Recovery |

## Disaster Recovery Architecture Decisions

| **\#**                | **AD**        | **Requirement**                                                                                                                                                                                                                                                         | **Alternative**                                                                                                    | **Decision**                    | **Rationale**                                                                                                                                                                    |
|-----------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|---------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Disaster Recovery** |               |                                                                                                                                                                                                                                                                         |                                                                                                                    |                                 |                                                                                                                                                                                  |
|  1                    | DR Approach   | - Ensure availability of resources in the event of unplanned outages - Establish alternate site for failover of workloads in the event of failure in the primary site - Support business targets for RPO/RTO - Support Business Continuity and Disaster Recovery plans  | - Active-Active - Active-Standby / Hot DR Site - Active-Standby / Warm DR Site - Backup & Restore (Cold DR Site)   | Active-Active                   | Recommended approach for: - Mission critical applications with continuous availability requirements and near zero RPO/RTO                                                        |
|                       |               |                                                                                                                                                                                                                                                                         |                                                                                                                    | Active-Standby / Hot DR Site    | Recommended approach for: Core business applications with  - High availability requirements  - RPO \< 15 mins - RTO \< 1 hour                                                    |
|                       |               |                                                                                                                                                                                                                                                                         |                                                                                                                    | Active-Standby / Warm DR Site   | Recommended approach for: Core business applications with  - medium to high availability requirements  - RPO \< 1 hour - RTO \< 4 hours                                          |
|                       |               |                                                                                                                                                                                                                                                                         |                                                                                                                    | Backup & Restore (Cold DR Site) | Recommended approach for: - Dev/test environments - Low priority workloads                                                                                                       |
| 2                     | DR Automation | - Automate recovery tasks to minimize down time                                                                                                                                                                                                                         | - IBM Schematics - Terraform - Ansible - BYO tool                                                                  | IBM Schematics                  | - IBM Schematics provides Terraform-as-a-Service and Ansible-as-a-Service to automate the provisioning and configuration management of the web, app, and DB tiers in the DR site |

## Backup and Restore Architecture Decisions

| **\#**                 | **AD**            | **Requirement**                                                                                                         | **Alternative**                                            | **Decision**        | **Rationale**                                                                                                                                               |
|------------------------|-------------------|-------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------|---------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Backup and Restore** |                   |                                                                                                                         |                                                            |                     |                                                                                                                                                             |
| 1                      | App server backup | - Backup App server to enable re-deployment of application in the event of unplanned outages                            | - IBM Cloud Backup - IBM Storage Protect                   | IBM Cloud Backup    | Use Cloud Backup to create volume snapshots of App servers when application-consistent backups are not required.                                            |
| 2                      | DB server backup  | - Backup DB server images to enable recovery of databases in the event of unplanned outages                             | - IBM Cloud Backup - IBM Storage Protect                   | IBM Storage Protect | Use Storage Protect to backup DB servers in DB tier since this is also the recommended tool for transaction consistent backups for the data volumes.        |
| 3                      | DB backup         | - Create transaction consistent database backups to enable recovery of database tier in the event of unplanned outages  | - IBM Storage Protect - DB Backup Tool - BYO Tool          | IBM Storage Protect | Use Storage Protect for application consistent backups of the database. - Storage Protect supports Oracle, IBM Db2, MongoDB, Microsoft SQL Server, SAP HANA |
| 4                      | File Backup       | - Create file system backups for according to business processes                                                        | - IBM Storage Protect - BYO Backup Tool                    | IBM Storage Protect | Use Storage Protect if there is a business need to backup or recover specific files.                                                                        |
| 5                      | Backup Automation | - Schedule regular database backups based on RPO requirements to enable data recovery in the event of unplanned outages | - IBM Cloud Backup - IBM Storage Protect - BYO Backup Tool | IBM Storage Protect | Use Storage Protect to schedule regular backups for the DB tier and define backup policies to manage the creation and deletion of backups.                  |

## Networking Architecture Decisions

| **\#**                          | **AD**                    | **Requirement**                                                                                                             | **Alternative**                                                  | **Decision**                         | **Rationale**                                                                                                                                                                                              |
|---------------------------------|---------------------------|-----------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------|--------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. Load Balancing**           |                           |                                                                                                                             |                                                                  |                                      |                                                                                                                                                                                                            |
| 1.1                             | Application Load Balancer | - Route web user http/https requests                                                                                        | - VPC ALB - VPC NLB                                              | VPC ALB                              | VPC ALB is recommended for web-based workloads. - Provides layer 4 and layer 7 load balancing - Supports HTTP, HTTPS, and TCP requests - Supports SSL offloading.                                          |
|                                 |                           |                                                                                                                             |                                                                  | VPC NLB                              | VPC NLB is recommended for workloads that require low latency and high data throughput or VNF routing. - Provides layer 4 load balancing - Supports Source IP Preservation - Supports Direct Server Return |
| 1.2                             | Global Load Balancing     | - Distribute requests across regions or fail over workloads to alternate region in the event of failure in the primary site | - IBM Cloud DNS (Private) - IBM Cloud Internet Services (public) | IBM Cloud Internet Services (public) | The Cloud Internet Services (CIS) Global Load Balancer distributes user requests across regions in multi-region app deployments.                                                                           |
| **2. Domain Name System (DNS)** |                           |                                                                                                                             |                                                                  |                                      |                                                                                                                                                                                                            |
| 2.1                             | Public DNS                | - Provide DNS resolution to support use of hostnames instead of IP addresses for applications                               | - IBM Cloud Internet Services (CIS) - IBM Cloud DNS              | IBM Cloud Internet Services (CIS)    | IBM Cloud Internet Services supports provisioning and configuring DNS records for public DNS resolution and can be integrated with the public VPC ALBs for the web tier.                                   |
| 2.2                             | Private DNS               |                                                                                                                             | - IBM Cloud DNS                                                  | IBM Cloud DNS                        | IBM Cloud DNS manages private DNS records, resolves domain names from IBM Cloud's private network, and can be integrated with the private VPC ALBs for the app and DB tiers.                               |

## Security Architecture Decisions

| **\#**                                                         | **AD**                         | **Requirement**                                                                                        | **Alternative**                                                                                   | **Decision**                                   | **Rationale**                                                                                                                                                                                                                                                                                                                                                                                                                      |
|----------------------------------------------------------------|--------------------------------|--------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. Data: Encryption at Rest**                                |                                |                                                                                                        |                                                                                                   |                                                |                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| 1.1                                                            | Encryption Approach            | - Encrypt all data to protect from unauthorized disclosure                                             | - Encryption with provider keys - Encryption with customer-managed keys                           | Encryption with customer-managed keys          | Encrypting data with customer-managed keys is recommended since it provides added security and customer control needed to meet regulatory compliance requirements.                                                                                                                                                                                                                                                                 |
| 1.2                                                            | Data Encryption Application    | - Encrypt all application data to protect from unauthorized disclosure                                 | - Storage encryption with customer-managed keys - App-level encryption with customer-managed keys | Storage Encryption with customer-managed keys  | Application data can be stored on VPC File, VPC Block, or Cloud Object Storage (COS) which support encryption with customer-managed keys by selecting a Key Management Service (KMS) for the respective storage service.                                                                                                                                                                                                           |
| 1.3                                                            | Data Encryption  Backups       | - Encrypt all backup data to protect from unauthorized disclosure                                      | Storage encryption with customer-managed keys App-level encryption                                | Storage encryption with customer-managed keys  | Backup data can be stored in COS or VPC Block storage which support encryption with customer-managed keys by selecting a KMS.                                                                                                                                                                                                                                                                                                      |
| 1.4                                                            | Data Encryption  Logs          | - Encrypt all operational and audit logs at rest to protect from unauthorized disclosure               | Storage encryption with customer-managed keys App-level encryption                                | Storage encryption with customer-managed keys  | All operational and audit logs are stored in COS which supports encryption with customer-managed keys by selecting a KMS.                                                                                                                                                                                                                                                                                                          |
| **2. Data: Key Lifecycle Management & Certificate Management** |                                |                                                                                                        |                                                                                                   |                                                |                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| 2.1                                                            | Key Lifecycle Management & HSM | - Encrypt data at rest and in transit using customer managed keys to protect from unauthorized access  | Key Protect Hyper Protect Crypto Services (HPCS)                                                  | Key Protect                                    | Key Protect is recommended for applications that need to comply with regulations requiring encryption of data with customer-managed keys. Key Protect provides key management services using a shared (multi-tenant) FIPS 140-2 Level 3 certified hardware security modules (HSMs).                                                                                                                                                |
|                                                                |                                |                                                                                                        |                                                                                                   | Hyper Protect Crypto Services (HPCS)           | HPCS is recommended for financial services and highly regulated industry applications. HPCS provides Key Management Services with the highest level of security and control offered by any cloud provider in the industry. It uses a dedicated (single-tenant) FIPS 140-2 Level 4 certified Hardware Security Module and supports customer-managed master keys, giving the customer exclusive control of the entire key hierarchy. |
| 2.2                                                            | Certificate Management         | - Protect secrets through their entire lifecycle and secure them using access control measures         | Secrets Manager BYO Certificate Manager                                                           | Secrets Manager                                | IBM Secrets Manager creates, leases, and centrally manages secrets used by IBM Cloud Services or customer applications. Secrets are stored in a dedicated instance of Secrets Manager and can be encrypted using any of IBM Cloud Key Management Services.                                                                                                                                                                         |

## Service Management Architecture Decisions

| **\#**            | **AD**                                                      | **Requirement**                                                                                                                                                 | **Alternative**                                                    | **Decision**                                                       | **Rationale**                                                                                                                                                                                                                                                               |
|-------------------|-------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. Monitoring** |                                                             |                                                                                                                                                                 |                                                                    |                                                                    |                                                                                                                                                                                                                                                                             |
| 1.1               | Operational Monitoring of Cloud infrastructure and services | - Monitor system and app health to determine need to failover to alternate site. Operational metrics include CPU and memory usage, API response times, etc.     | - IBM Cloud Monitoring - BYO Monitoring Tool                       | IBM Cloud Monitoring                                               | - IBM Cloud Monitoring collects and monitors operational metrics for cloud infrastructure as well as cloud platform and services and provides a single view for all metrics                                                                                                 |
| 1.2               | Operational Monitoring for Applications                     |                                                                                                                                                                 | - IBM Cloud Monitoring - Instana (SaaS) - BYO Monitoring Tool      | IBM Cloud Monitoring + Instana (SaaS)                              | - Use Instana along with IBM Cloud Monitoring to get additional application performance metrics and automate application performance management. Instana provides data and actionable insights to monitor the applications and automate root-cause analysis.                |
| **2. Logging**    |                                                             |                                                                                                                                                                 |                                                                    |                                                                    |                                                                                                                                                                                                                                                                             |
| 2.1               | Logs Monitoring for Cloud infrastructure and services       | - Monitor operational logs to detect issues that might impact the availability of the system and app                                                            | - IBM Cloud Logging - BYO Logging Tool                             | IBM Cloud Logging                                                  | - IBM Logging collects operational logs from applications, platform resources, and infrastructure and provides interfaces to view and analyze all logs.                                                                                                                     |
| 2.2               | Logs Monitoring for Applications                            |                                                                                                                                                                 | - IBM Cloud Logging - Application Logging - BYO Logging Tool       | IBM Cloud Logging + Application Logging                            | - Application logs can be sent to IBM Cloud Logging to get more application-specific logs                                                                                                                                                                                   |
| **3. Auditing**   |                                                             |                                                                                                                                                                 |                                                                    |                                                                    |                                                                                                                                                                                                                                                                             |
| 3.1               | Audit Logging                                               | - Monitor audit logs to track changes to cloud resources and detect potential security problems                                                                 | IBM Cloud Activity Tracker - Hosted Event Search - Event Routing   | IBM Cloud Activity Tracker- Hosted Event Search                    | IBM Cloud Activity Tracker-Hosted Event Search provides interfaces to capture, store, view, search, and monitor user-initiated actions to provision, access, and manage IBM Cloud resources.                                                                                |
| **4. Alerting**   |                                                             |                                                                                                                                                                 |                                                                    |                                                                    |                                                                                                                                                                                                                                                                             |
| 4.1               | Operational alerts                                          | - Provide a mechanism to identify and send notifications about operational issues found across application and infrastructure                                   | IBM Cloud Monitoring +  IBM Cloud Logging + Event Notifications    | IBM Cloud Monitoring +  IBM Cloud Logging + Event Notifications    | - IBM Cloud Monitoring and IBM Cloud Logging support the configuration of alerts to detect operational issues and send notifications to targeted channels.  Event Notifications can be used to route the alert events to service destinations to automate response actions. |
| 4.2               | Audit alerts                                                | - Provide a mechanism to identify and send notifications about issues found in audit logs                                                                       | IBM Cloud Activity Tracker + IBM Monitoring +  Event Notifications | IBM Cloud Activity Tracker + IBM Monitoring +  Event Notifications | - IBM Activity Tracker supports the configuration of alerts to detect audit issues and send notifications to targeted channels.  Event Notifications can be used to route the alert events to service destinations to automate response actions.                            |

# References

-   Build for reliability practice: <https://www.ibm.com/garage/method/practices/manage/build-for-reliability>

-   Build Resilient Applications on the Cloud: <https://www.ibm.com/cloud/architecture/architectures/resilience>

-   Architecting highly available cloud solutions: <https://www.ibm.com/garage/method/practices/run/cloud-platform-for-ha>

-   Implement a High Availability Architecture: <https://www.ibm.com/garage/method/practices/manage/practice_high_availability>

-   Always On: Assess, Design, Implement, and Manage Continuous Availability:

    [http://www.redbooks.ibm.com/redpapers/pdfs/redp5109.pdf?_gl=1\*732ea0\*_ga\*ODczMjAzMTY1LjE2OTUxMjkyMzQ.\*_ga_FYECCCS21D\*MTY5NTEzNzE3Mi4zLjAuMTY5NTEzNzE3Mi4wLjAuMA](http://www.redbooks.ibm.com/redpapers/pdfs/redp5109.pdf?_gl=1*732ea0*_ga*ODczMjAzMTY1LjE2OTUxMjkyMzQ.*_ga_FYECCCS21D*MTY5NTEzNzE3Mi4zLjAuMTY5NTEzNzE3Mi4wLjAuMA)

-   Automate Application Monitoring: <https://www.ibm.com/garage/method/practices/manage/practice_automated_monitoring>

-   Incident Management <https://www.ibm.com/cloud/architecture/architectures/incidentManagementDomain/>

-   VPC ALB multi-zone configuration:

    [https://cloud.ibm.com/docs/vpc?topic=vpc-load-balancers-about&interface=api\#horizontal-scaling](https://cloud.ibm.com/docs/vpc?topic=vpc-load-balancers-about&interface=api#horizontal-scaling)

-   IBM Cloud Backup for VPC

    <https://cloud.ibm.com/docs/vpc?topic=vpc-backup-service-about>

-   IBM Schematics

    <https://cloud.ibm.com/docs/schematics?topic=schematics-sc-actions>

-   IBM Cloud SLA

    [https://www.ibm.com/support/customer/csol/terms/?id=i126-9268&lc=en\#detail-document](https://www.ibm.com/support/customer/csol/terms/?id=i126-9268&lc=en#detail-document)

-   IBM Storage Protect:

    <https://cloud.ibm.com/catalog/content/SPonIBMCloud-20c54034-d319-48c0-beb6-0b4adc54265c-global>
