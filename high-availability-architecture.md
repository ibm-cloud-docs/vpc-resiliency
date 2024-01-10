---

copyright:
  years: 2023
lastupdated: "2023-12-14"

subcollection: vpc-resiliency

keywords:

---

# Architecture decisions for high availability
{: #high-availability-architecture}

The following table summarizes the high availability architecture decisions for deploying resilient workloads on IBM Cloud VPC infrastructure.

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| High availability deployment | - Ensure availability of resources if outages occur. \n - Support SLA targets for application availability. | - Single-zone, single-region \n - Multi-zone, single-region \n - Multi-zone, multi-region | Single-zone, single-region  | Recommended for low to medium priority applications or nonproduction workloads. \n - Provides protection from host failures \n  - Supports 99.9% availability |
| | | | Multi-zone, single-region | Recommended for core business applications and production-level workloads with stringent resiliency requirements \n - Provides protection from zone outage \n - Supports 99.99% availability for active-active application architecture with virtual servers that are deployed across 3 zones \n - Supports business continuity policies with country boundaries or geo data residence constraints |
| | | | Multi-zone, multi-region | Recommended for mission-critical applications with continuous or near continuous availability and disaster recovery requirements. \n - Provides protection from region outage \n * Supports \>99.99% availability for active-active application architecture with application-aware data replication across regions \n  - Supports business continuity policies with cross geo or distance requirements |
{: caption="Table 1. Architecture decisions for high availability" caption-side="bottom"}
