---

copyright:
  years: 2023
lastupdated: "2023-12-14"

subcollection: vpc-resiliency

keywords:

---

# High Availability Architecture Decisions
{: #high-availability}

The following table summarizes the high availability architecture decisions for deploying resilient workloads on IBM Cloud VPC infrastructure.

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| High Availability Deployment | - Ensure availability of resources in the event of outages. \n - Support SLA targets for application availability. | - Single-zone, single-region \n - Multi-zone, single-region \n - Multi-zone, multi region | Single-zone, single-region  | Recommended for low to medium priority applications or non-production workloads. \n - Provides protection from host failures \n  - Supports 99.9% availability |
| | | | Multi-zone, single-region | Recommended for core business applications and production-level workloads with stringent resiliency requirements \n - Provides protection from zone outage \n - Supports 99.99% availability for active-active application architecture with virtual servers deployed across 3 zones \n - Supports business continuity policies with country boundaries or geo data residence constraints |
| | | | Multi-zone, multi-region | Recommended for mission critical applications with continuous or near continuous availability requirements and for disaster recovery. \n - Provides protection from region outage \n * Supports \>99.99% availability for active-active application architecture with application-aware data replication across regions \n  - Supports business continuity policies with cross geo or distance requirements |
{: caption="Table 1. High availability architecture decisions" caption-side="bottom"}
