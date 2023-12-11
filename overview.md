---

copyright:
  years: 2023
lastupdated: "2023-11-28"

subcollection: whitepaper-vpc-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Virtual Private Cloud Resiliency
{: #intro}

High Availability and Disaster Recovery are important considerations to meet continuous business operations and resiliency requirements for enterprise workloads.

High availability solutions are designed to minimize down time under normal operations. Disaster recovery solutions are designed to minimize down time in the event of unplanned outages, whatever the cause.

Cloud platforms are intrinsically built to tolerate failures and provide features to help build reliable and highly available solutions such as: 1. elastic compute and storage at various geographic locations, 2. availability zones engineered to be isolated from failures in other zones, and 3. highly available cloud services deployed across multiple zones or regions, e.g. object storage, Database as a service. However, just moving an application to the cloud does not make it fault-tolerant or highly available. Application resiliency in public clouds is a shared responsibility. To build resilient applications in the cloud you must:

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

This document provides design considerations and architecture decisions for deploying resilient applications on IBM Cloud Virtual Private Cloud (VPC) infrastructure. It covers the following solution aspects and domains:
- Networking: Load Balancing, DNS
- Security: Data Security
- Resiliency: High Availability, Backup and Restore
- Service Management: Monitoring, Logging, Auditing, Alerting

The Architecture Framework provides a consistent approach to design cloud solutions by addressing requirements across a set of "aspects" and "domains", which are technology-agnostic architectural areas that need to be considered for any enterprise solution. For more details, see [Introduction to the Architecture Framework](/docs/architecture-framework).

# Resiliency Concepts
{: #concepts}

## High Availability
{: #high-availability}

High Availability (HA) is the ability of a system or application to keep running in a healthy state, without significant downtime.

A common practice for achieving high availability is to use redundant resources to avoid single points of failure. Leverage availability zones and regions on IBM Cloud to implement redundant deployments and fault isolation patterns for the application infrastructure. Use platform as a service and other cloud services that are provisioned across multiple availability zones to provide built-in resiliency. However, to meet business continuity requirements, the application must also be designed for high availability.

Applications designed for high availability are typically deployed in clusters following an active-active or active-standby architecture.

### Active-Active architecture
{: #active-active}

In an active-active architecture, two active instances of the application are deployed across different failure domains to provide scalability and high availability. All the instances handle application requests concurrently. A load balancer is used to distribute requests among the active application instances. Stateful applications, like databases, require data replication between instances.

### Active-Standby architecture
{: #active-standby}

In an active-standby architecture, two instances of the application are deployed: an active instance and a standby instance. However, application requests are sent to the active instance only. A load balancer is used to route requests to the standby instance when the active instance becomes unavailable. Stateful applications, like databases, require synchronous data replication between instances.

## Backup and Restore
{: #backup}

Backups are used to provide protection from data loss. Backup and restore solutions create copies of data and support restoring data from backup copies to enable the recovery of lost or compromised data in the event of an unplanned outage, such as hardware failure, data center failure, data corruption, accidental deletion, or even a cybersecurity breach.

## Disaster Recovery
{: #disaster-recovery}

Disaster Recovery is the ability to protect a system or application from natural or unplanned outages by having a set of policies, tools, and procedures to enable the recovery or continuation of vital technology infrastructure and applications following an event, whatever the cause (e.g. natural disaster or outage caused by hardware failure, data corruption, accidental deletion, cybersecurity breach, etc.)

Disaster Recovery requirements are generally expressed in terms of:

-   Recovery Time Objective (RTO): the targeted duration of time within which a business process shall be restored after a disaster or outage.

-   Recovery Point Objective (RPO): the maximum targeted time period in which data might be lost from an IT service due to a disaster or outage.
