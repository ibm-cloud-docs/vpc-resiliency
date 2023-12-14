---

copyright:
  years: 2023
lastupdated: "2023-12-12"

subcollection: vpc-resiliency

keywords:

---

{{site.data.keyword.attribute-definition-list}}

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
